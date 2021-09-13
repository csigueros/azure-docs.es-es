---
title: Aplicación de una configuración de State Configuration de Azure Automation en máquinas virtuales Linux mediante PowerShell
description: En este artículo se explica cómo configurar una máquina virtual Linux para que tenga un estado deseado mediante State Configuration de Azure Automation con PowerShell.
ms.topic: conceptual
services: automation
ms.subservice: dsc
ms.date: 08/31/2021
ms.openlocfilehash: 600fdfafa64e0827ac023ae01810ec54bdaa95fe
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438505"
---
# <a name="configure-linux-desired-state-with-azure-automation-state-configuration-using-powershell"></a>Configuración del estado deseado en Linux con State Configuration de Azure Automation mediante PowerShell

En este tutorial, aplicará una configuración de State Configuration de Azure Automation con PowerShell a una máquina virtual Linux de Azure para comprobar si tiene un estado deseado. El estado deseado consiste en identificar si el servicio apache2 está presente en el nodo.

State Configuration de Azure Automation permite especificar configuraciones para las máquinas y garantizar que dichos servidores tengan el estado especificado a lo largo del tiempo. Para obtener información general sobre State Configuration, consulte [Introducción a State Configuration de Azure Automation](./automation-dsc-overview.md).

En este tutorial, aprenderá a:
> [!div class="checklist"]
> - Incorporar una máquina virtual Linux de Azure para que DSC de Azure Automation la administre
> - Composición de una configuración
> - Instalar el módulo de PowerShell para Automation
> - Importar una configuración en Azure Automation
> - Compilación de una configuración en una configuración de nodo
> - Asignación de una configuración de nodo a un nodo administrado
> - Modificar la asignación de configuraciones de nodo
> - Comprobación del estado de cumplimiento de un nodo administrado

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure Automation Para obtener más información sobre las cuentas de Automation y sus requisitos, consulte [Introducción a la autenticación de cuentas de Automation](./automation-security-overview.md).
- Una máquina virtual de Azure Resource Manager que ejecute Ubuntu 18.04 LTS o posterior. Para obtener instrucciones sobre cómo crear una máquina virtual Linux de Azure, consulte [Creación de una máquina virtual Linux en Azure con PowerShell](../virtual-machines/windows/quick-create-powershell.md).
- El [módulo Az](/powershell/azure/new-azureps-module-az) de PowerShell instalado en la máquina que va a usar para escribir, compilar y aplicar una configuración de estado a una máquina virtual Linux de Azure de destino. Asegúrese de disponer de la versión más reciente. Si es necesario, ejecute `Update-Module -Name Az`.

## <a name="create-a-configuration"></a>Creación de una configuración

Revise el código siguiente y observe la presencia de dos [configuraciones](/powershell/scripting/dsc/configurations/configurations) de nodo: `IsPresent` y `IsNotPresent`. Esta configuración llama a un recurso en cada bloque de nodo: el [recurso nxPackage](/powershell/scripting/dsc/reference/resources/linux/lnxpackageresource). Este recurso administra la presencia del paquete **apache2**. A continuación, en un editor de texto, copie el código siguiente en un archivo local y asígnele el nombre `LinuxConfig.ps1`:

```powershell
Configuration LinuxConfig
{
    Import-DscResource -ModuleName 'nx'

    Node IsPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Present'
            PackageManager    = 'Apt'
        }
    }

    Node IsNotPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Absent'
        }
    }
}
```

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

En la máquina, inicie sesión en la suscripción de Azure con el cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) de PowerShell y siga las instrucciones que aparecen en pantalla.

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="initialize-variables"></a>Inicialización de variables

Para aumentar la eficacia y reducir la probabilidad de error al ejecutar los cmdlets, revise el código de PowerShell más adelante según sea necesario y, luego, ejecútelo.

| Variable | Value |
|---|---|
|$resourceGroup| Reemplace `yourResourceGroup` por el nombre real del grupo de recursos.|
|$automationAccount| Reemplace `yourAutomationAccount` por el nombre real de la cuenta de Automation.|
|$VM| Reemplace `yourVM` por el nombre real de la máquina virtual Linux de Azure.|
|$configurationName| Deje el valor `LinuxConfig`. Nombre de la configuración usada en este tutorial.|
|$nodeConfigurationName0|Deje el valor `LinuxConfig.IsNotPresent`. Nombre de una configuración de nodo usada en este tutorial.|
|$nodeConfigurationName1|Deje el valor `LinuxConfig.IsPresent`. Nombre de una configuración de nodo usada en este tutorial.|
|$moduleName|Deje el valor `nx`. Nombre del módulo de PowerShell que se usa para DSC en este tutorial.|
|$moduleVersion| Obtenga el número de versión más reciente de `nx` en la [Galería de PowerShell](https://www.powershellgallery.com/packages/nx). En este tutorial se usa la versión `1.0`.|

```powershell
$resourceGroup = "yourResourceGroup"
$automationAccount = "yourAutomationAccount"
$VM = "yourVM"
$configurationName = "LinuxConfig"
$nodeConfigurationName0 = "LinuxConfig.IsNotPresent"
$nodeConfigurationName1 = "LinuxConfig.IsPresent"
$moduleName = "nx"
$moduleVersion = "1.0"
```

## <a name="install-nx-module"></a>Instalación del módulo nx

Azure Automation usa una serie de módulos de PowerShell para habilitar cmdlets en runbooks y recursos de DSC en configuraciones de DSC. **nx** es el módulo con recursos de DSC para Linux. Instale el módulo **nx** con el cmdlet [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule). Para obtener más información sobre los módulos, consulte [Administración de módulos en Azure Automation](./shared-resources/modules.md). Ejecute el siguiente comando:

```powershell
New-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName `
    -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

La salida debería ser similar a la que se muestra a continuación:

   :::image type="content" source="media/dsc-linux-powershell/new-azautomationmodule-output.png" alt-text="Salida del comando New-AzAutomationModule.":::

Puede comprobar la instalación ejecutando el comando siguiente:

```powershell
Get-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName 
```

## <a name="import-configuration-to-azure-automation"></a>Importación de una configuración en Azure Automation

Llame al cmdlet [Import-AzAutomationDscConfiguration](/powershell/module/az.automation/import-azautomationdscconfiguration) para cargar una configuración de nodo en la cuenta de Automation. Revise el valor de `-SourcePath` con la ruta de acceso real y, a continuación, ejecute el siguiente comando:

```powershell
Import-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -SourcePath "path\LinuxConfig.ps1" `
   -Published
```

La salida debería ser similar a la que se muestra a continuación:

   :::image type="content" source="media/dsc-linux-powershell/import-azautomationdscconfiguration-output.png" alt-text="Salida del comando Import-AzAutomationDscConfiguration.":::

Puede ver la configuración desde la cuenta de Automation que ejecuta el siguiente comando:

```powershell
Get-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $configurationName
```

## <a name="compile-configuration-in-azure-automation"></a>Compilación de una configuración en Azure Automation

Para poder aplicar un estado deseado a un nodo, se debe compilar una configuración que defina dicho estado en una o varias configuraciones de nodo.  Llame al cmdlet [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) para compilar la configuración `LinuxConfig` en Azure Automation. Para obtener más información sobre la compilación, consulte [Compilación de configuraciones DSC](./automation-dsc-compile.md). Ejecute el siguiente comando:

```powershell
Start-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

La salida debería ser similar a la que se muestra a continuación:

   :::image type="content" source="media/dsc-linux-powershell/start-azautomationdsccompilationjob-output.png" alt-text="Salida del comando Start-AzAutomationDscCompilationJob.":::

Puede ver el trabajo de compilación desde su cuenta de Automation mediante el siguiente comando:

```powershell
Get-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

Espere a que el trabajo de compilación finalice antes de continuar. La configuración se debe compilar en una configuración de nodo antes de que se pueda asignar a un nodo. Ejecute el código siguiente para comprobar el estado cada 5 segundos:

```powershell
while ((Get-AzAutomationDscCompilationJob `
         -ResourceGroupName $resourceGroup `
         -AutomationAccountName $automationAccount `
         -ConfigurationName $configurationName).Status -ne "Completed") 
{
   Write-Output "Wait"
   Start-Sleep -Seconds 5
}
Write-Output "Compilation complete"
```

Una vez completado el trabajo de compilación, también puede ver los metadatos de configuraciones de nodo mediante el siguiente comando:

```powershell
Get-AzAutomationDscNodeConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount
```

## <a name="register-the-azure-linux-vm-for-an-automation-account"></a>Registro de la máquina virtual Linux de Azure para una cuenta de Automation

Registre la máquina virtual Linux de Azure como nodo Desired State Configuration (DSC) para la cuenta Azure Automation. El cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) solo admite máquinas virtuales que ejecutan el sistema operativo Windows. La máquina virtual Linux de Azure primero debe configurarse para DSC. Para ver los pasos detallados, consulte [Introducción a la configuración de estado deseado (DSC) para Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

1. Ejecute el código que se muestra abajo para que se cree un script de Python con el comando de registro de PowerShell para que se ejecute posteriormente en la máquina virtual Linux de Azure:

   ```powershell
    $primaryKey = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).PrimaryKey
    
    $URL = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).Endpoint
    
    Write-Output "sudo /opt/microsoft/dsc/Scripts/Register.py $primaryKey $URL"
   ```

   Estos comandos obtienen la dirección URL y la clave de acceso principal de la cuenta de Automation y la concatenan con el comando de registro. Asegúrese de quitar los retornos de carro de la salida. Este comando se utilizará en otro paso más adelante.

1. Conéctese a una máquina virtual Linux de Azure. Si usó una contraseña, puede usar la sintaxis siguiente. Si usó un par de claves pública y privada, consulte [SSH en Linux](./../virtual-machines/linux/mac-create-ssh-keys.md) para obtener los pasos detallados. Los otros comandos recuperan información sobre qué paquetes se pueden instalar, incluidas qué actualizaciones hay de los paquetes instalados actualmente, e instala Python.

   ```cmd
   ssh user@IP

   sudo apt-get update
   sudo apt-get install -y python
   ```

1. Instale Open Management Infrastructure (OMI). Para obtener más información sobre OMI, consulte [Open Management Infrastructure](https://github.com/Microsoft/omi). Compruebe cuál es la [versión](https://github.com/Microsoft/omi/releases) más reciente. Revise la versión de lanzamiento siguiente según sea necesario y, a continuación, ejecute los comandos en la sesión SSH:

   ```bash
   wget https://github.com/microsoft/omi/releases/download/v1.6.8-0/omi-1.6.8-0.ssl_110.ulinux.x64.deb

   sudo dpkg -i ./omi-1.6.8-0.ssl_110.ulinux.x64.deb
   ```

1. Instale Desired State Configuration de PowerShell para Linux. Para obtener más información, consulte [DSC en Linux](https://github.com/microsoft/PowerShell-DSC-for-Linux). Compruebe cuál es la [versión](https://github.com/microsoft/PowerShell-DSC-for-Linux/releases) más reciente. Revise la versión de lanzamiento siguiente según sea necesario y, a continuación, ejecute los comandos en la sesión SSH:

   ```bash
   wget https://github.com/microsoft/PowerShell-DSC-for-Linux/releases/download/v1.2.1-0/dsc-1.2.1-0.ssl_110.x64.deb

   sudo dpkg -i ./dsc-1.2.1-0.ssl_110.x64.deb
   ```

1. Ahora puede registrar el nodo mediante el script de Python `sudo /opt/microsoft/dsc/Scripts/Register.py <Primary Access Key> <URL>` creado en el paso 1. Ejecute los comandos en la sesión SSH. La salida siguiente debería ser similar a la que se muestra a continuación:

   ```output
   instance of SendConfigurationApply
   {
        ReturnValue=0
   }
   
   ```

1. Puede comprobar el registro en PowerShell mediante el siguiente comando:

   ```powershell
     Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM  
   ```

   La salida debería ser similar a la que se muestra a continuación:

      :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnode-output.png" alt-text="Salida del comando Get-AzAutomationDscNode.":::

## <a name="assign-a-node-configuration"></a>Asignación de una configuración de nodo

Llame al cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) para establecer la asignación de configuraciones de nodo. Ejecute los comandos siguientes:

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $VM

# Set node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName0 `
   -NodeId $node.Id `
   -Force
```

La salida debería ser similar a la que se muestra a continuación:

   :::image type="content" source="media/dsc-linux-powershell/set-azautomationdscnode-output.png" alt-text="Salida del comando Set-AzAutomationDscNode.":::

## <a name="modify-the-node-configuration-mapping"></a>Modificación de la asignación de configuraciones de nodo

Llame al cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) para modificar la asignación de configuraciones de nodo. Aquí, modificará la asignación de configuraciones de nodo actual de `LinuxConfig.IsNotPresent` a `LinuxConfig.IsPresent`. Ejecute el siguiente comando:

```powershell
# Modify node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName1 `
   -NodeId $node.Id `
   -Force
```

## <a name="check-the-compliance-status-of-a-managed-node"></a>Comprobación del estado de cumplimiento de un nodo administrado

Cada vez que State Configuration realiza una comprobación de coherencia en un nodo administrado, el nodo envía un informe de estado al servidor de extracción. El siguiente ejemplo usa el cmdlet [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) para informar sobre el estado de cumplimiento de un nodo administrado.

```powershell
Get-AzAutomationDscNodeReport `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeId $node.Id `
   -Latest
```

La salida debería ser similar a la que se muestra a continuación:

   :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnodereport-output.png" alt-text="Salida del comando Get-AzAutomationDscNodeReport.":::

Es posible que el primer informe no esté disponible inmediatamente; puede tardar hasta 30 minutos después de habilitar un nodo. Para obtener más información sobre los datos de informes, consulte [Uso de un servidor de informes de DSC](/powershell/scripting/dsc/pull-server/reportserver).

## <a name="clean-up-resources"></a>Limpieza de recursos

Los pasos siguientes lo ayudarán a eliminar los recursos creados para este tutorial que ya no son necesarios.

1. Quite el nodo DSC de la administración por parte de una cuenta de Automation. Aunque no puede registrar un nodo mediante PowerShell, puede anular su registro con PowerShell. Ejecute los comandos siguientes:

    ```powershell
    # Get the ID of the DSC node
    $NodeID = (Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM).Id
    
    Unregister-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Id $NodeID `
       -Force

    # Verify using the same command from Register the Azure Linux VM for an Automation account. A blank response indicates success.
    Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM
    ```

1. Quite los metadatos de las configuraciones de nodo de DSC en Automation. Ejecute los comandos siguientes:

    ```powershell
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0 `
       -IgnoreNodeMappings `
       -Force
    
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1 `
       -IgnoreNodeMappings `
       -Force

    # Verify using the same command from Compile configuration in Azure Automation.
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0
    
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1
    ```

      La eliminación correcta se indica mediante una salida similar a la siguiente: `Get-AzAutomationDscNodeConfiguration : NodeConfiguration LinuxConfig.IsNotPresent not found`.

1. Quite la configuración de DSC de Automation. Ejecute el siguiente comando:

    ```powershell
    Remove-AzAutomationDscConfiguration `
       -AutomationAccountName $automationAccount `
       -ResourceGroupName $resourceGroup `
       -Name $configurationName `
       -Force

    # Verify using the same command from Import configuration to Azure Automation.
    Get-AzAutomationDscConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $configurationName
    ```

   La eliminación correcta se indica mediante una salida similar a la siguiente: `Get-AzAutomationDscConfiguration : Operation returned an invalid status code 'NotFound'`.

1. Quite el módulo nx de Automation. Ejecute el siguiente comando:

    ```powershell
    Remove-AzAutomationModule `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $moduleName -Force

    # Verify using the same command from Install nx module.
    Get-AzAutomationModule `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $moduleName
    ```

   La eliminación correcta se indica mediante una salida similar a la siguiente: `Get-AzAutomationModule : The module was not found. Module name: nx.`.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aplicado una configuración de State Configuration de Azure Automation con PowerShell a una máquina virtual Linux de Azure para comprobar si tenía un estado deseado. Para obtener una explicación más exhaustiva de la composición de la configuración, consulte:

> [!div class="nextstepaction"]
> [Composición de configuraciones DSC](./compose-configurationwithcompositeresources.md)