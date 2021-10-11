---
title: 'Tutorial: Creación de un runbook de flujo de trabajo de PowerShell en Azure Automation'
description: Este tutorial le enseña a crear, probar y publicar un runbook de flujo de trabajo de PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1550caff2fbd28a08e89c3fa570216ff8002430
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057707"
---
# <a name="tutorial-create-a-powershell-workflow-runbook-in-automation"></a>Tutorial: Creación de un runbook de flujo de trabajo de PowerShell en Automation

Este tutorial le guiará para crear un [runbook de flujo de trabajo de PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks) en Azure Automation. Los runbooks de flujo de trabajo de PowerShell son runbooks de texto basados en el flujo de trabajo de Windows PowerShell. Puede crear y modificar directamente el código del runbook con el editor de texto en Azure Portal. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de un runbook de flujo de trabajo de PowerShell
> * Prueba y publicación del runbook
> * Ejecución y seguimiento del estado del trabajo de runbook
> * Agregar autenticación para administrar recursos de Azure
> * Actualización de los parámetros de runbook para iniciar una máquina virtual de Azure

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure Automation con al menos una identidad administrada asignada por el usuario. Para más información, consulte [Habilitación de identidades administradas](../quickstarts/enable-managed-identity.md).
* Módulos de az: `Az.Accounts` y `Az.Compute` se han importado en la cuenta de Automation. Para más información, consulte [Importación de módulos Az](../shared-resources/modules.md#import-az-modules).
* Dos o más [máquinas virtuales de Azure](../../virtual-machines/windows/quick-create-powershell.md). Puesto que detiene e inicia estas máquinas, no deben ser máquinas virtuales de producción.
* El [módulo Azure Az de PowerShell](/powershell/azure/new-azureps-module-az) instalado en la máquina. Para instalar o actualizar, vea [Instalación del módulo Azure Az de PowerShell](/powershell/azure/install-az-ps).

## <a name="assign-permissions-to-managed-identities"></a>Asignación de permisos a las identidades administradas

Asigne permisos a la [identidad administrada](../automation-security-overview.md#managed-identities-preview) apropiada para permitirle detener una máquina virtual. El runbook puede usar la identidad administrada asignada por el sistema de la cuenta de Automation o una identidad administrada asignada por el usuario. Se proporcionan los pasos necesarios para asignar permisos a cada identidad. En los pasos siguientes se usa Azure Portal. Si prefiere usar PowerShell, consulte [Asignación de roles de Azure mediante Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a su cuenta de Automation.

1. En **Configuración de la cuenta**, seleccione **Identidad (versión preliminar)** .

1. En la pestaña **Asignado por el sistema**, en **Permisos**, seleccione **Asignaciones de roles de Azure** para abrir la página **Asignaciones de roles de Azure**.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-role-assignments-portal.png" alt-text="Selección de asignaciones de roles de Azure en el portal.":::

1. Seleccione **+ Agregar asignación de roles (versión preliminar)** , para abrir la página **Agregar asignación de roles (versión preliminar)** . 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-add-role-assignment-portal.png" alt-text="Agregar asignaciones de roles en el portal.":::

1. Seleccione los valores apropiados.

   |Propiedad |Descripción |
   |---|---|
   |Ámbito| Ámbito es el conjunto de recursos al que se aplica la asignación de roles. En la lista desplegable, seleccione **Grupo de recursos**.|
   |Subscription|Este campo se debe rellenar automáticamente con la suscripción.|
   |Grupo de recursos|En la lista desplegable, seleccione el grupo de recursos sobre el que conceder permisos de identidad.|
   |Rol|En la lista desplegable, seleccione **Usuario de DevTest Labs**.|

1. Seleccione **Guardar** y cierre la página **Asignaciones de roles de Azure** para volver a la pestaña **Asignado por el sistema**.

1. Seleccione la pestaña **Usuario asignado**.

1. Seleccione la identidad administrada asignada por el usuario en la lista para abrir la página **Identidad administrada**.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/select-user-assigned-identity-portal.png" alt-text="Selección de una identidad administrada asignada por el usuario en el portal.":::

1. Anote el valor de **Id. de cliente** para usarlo posteriormente.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/managed-identity-client-id-portal.png" alt-text="Mostrar el identificador de cliente de identidad administrada en el portal":::

1. En el menú de la izquierda, seleccione **Asignaciones de roles de Azure** y, después, **+ Agregar asignación de roles (versión preliminar)** para abrir la página **Agregar asignación de roles (versión preliminar)** . 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/user-assigned-add-role-assignment-portal.png" alt-text="Agregue asignaciones de roles en el portal para la identidad asignada por el usuario.":::

1. Seleccione los valores apropiados.

   |Propiedad |Descripción |
   |---|---|
   |Ámbito| En la lista desplegable, seleccione **Grupo de recursos**.|
   |Subscription|Este campo se debe rellenar automáticamente con la suscripción.|
   |Grupo de recursos|En la lista desplegable, seleccione el grupo de recursos sobre el que conceder permisos de identidad.|
   |Rol|En la lista desplegable, seleccione **Usuario de DevTest Labs**.|

1. Seleccione **Guardar** y cierre la página **Asignaciones de roles de Azure** para volver a la pestaña **Asignado por el usuario**.

## <a name="create-new-runbook"></a>Creación de un runbook

Empiece por crear un [runbook de flujo de trabajo de PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks) sencillo. Una ventaja de los flujos de trabajo de Windows PowerShell es la capacidad para realizar un conjunto de comandos en paralelo, en lugar de hacerlo secuencialmente como con un script típico.

1. En la página de la cuenta de Automation abierta, en **Automatización de procesos**, seleccione **Runbooks**.

1. Seleccione **+ Crear un runbook**.
    1. Asigne al runbook el nombre `MyFirstRunbook-Workflow`.
    1. En el menú desplegable **Tipo de Runbook**, seleccione **Flujo de trabajo de PowerShell**.
    1. Seleccione **Crear**.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook.png" alt-text="Creación de un runbook de flujo de trabajo de PowerShell desde el portal":::

## <a name="add-code-to-the-runbook"></a>Agregar código al runbook

Puede escribir el código directamente en el runbook o seleccionar los cmdlets, runbooks y recursos desde el control Biblioteca y agregarlos al runbook con los parámetros relacionados. En este tutorial, escribirá el código directamente en el runbook.

El runbook está vacío actualmente, solo con la palabra clave `Workflow` necesaria, el nombre del runbook y las llaves que encierran el flujo de trabajo completo.

```powershell
Workflow MyFirstRunbook-Workflow
{
}
```

1. Puede usar la palabra clave `Parallel` para crear un bloque de script con varios comandos que se ejecutarán simultáneamente. Escriba el código siguiente *entre* corchetes:

   ```powershell
   Parallel {
        Write-Output "Parallel"
        Get-Date
        Start-Sleep -s 3
        Get-Date
    }

   Write-Output " `r`n"
   Write-Output "Non-Parallel"
   Get-Date
   Start-Sleep -s 3
   Get-Date  
   ```

1. Guarde el Runbook, para lo que debe seleccionar **Guardar**.

## <a name="test-the-runbook"></a>Probar el runbook

Antes de publicar el runbook para que esté disponible en producción, debe probarlo para asegurarse de que funciona correctamente. La prueba de un runbook ejecuta su versión de borrador y permite ver la salida de forma interactiva.

1. Seleccione **Panel de prueba** para abrir la página **Probar**.

1. Seleccione **Iniciar** para iniciar la prueba.  Se crea un [trabajo de runbook](../automation-runbook-execution.md) y su estado se muestra en el panel.

   El estado del trabajo se inicia como En cola, que indica que está esperando a que haya disponible un trabajo de runbook en la nube. El estado cambia a Iniciando cuando un trabajo lo solicita. Por último, el estado pasa a En ejecución cuando el runbook comienza a ejecutarse.

1. Cuando se completa el trabajo del runbook, la página **Prueba** muestra su salida. La salida debe ser similar a la siguiente:

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-parallel-output.png" alt-text="Salida paralela del runbook de flujo de trabajo de PowerShell":::

   Revise el resultado. Todo el contenido del bloque `Parallel`, incluido el comando `Start-Sleep`, se ejecuta al mismo tiempo. Los mismos comandos fuera del bloque `Parallel` se ejecutaron secuencialmente, como muestran las diferentes marcas de fecha y hora. 

1. Cierre la página **Probar** para volver al lienzo.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar el runbook

El runbook que ha creado aún está en modo Borrador. Tiene que publicarlo para poder ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En este caso, no tiene una versión publicada aún porque acaba de crear el runbook.

1. Seleccione **Publicar** para publicar el runbook y **Sí** cuando se le solicite.

1. El campo **Estado** ahora muestra **Publicado**. Examine las opciones que se encuentran en la parte superior permiten iniciar el runbook ahora, programar una hora de inicio futura o crear un [webhook](../automation-webhooks.md) para que el runbook pueda iniciarse mediante una llamada HTTP. Para iniciar el runbook, seleccione **Iniciar** y **Sí** cuando se le solicite.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-overview.png" alt-text="Página de información general del runbook de flujo de trabajo de PowerShell":::

1. Se abre una página **Trabajo** para el trabajo de runbook que se ha creado. En este caso, deje abierto el panel para poder ver el progreso del trabajo. El campo **Estado** coincide con los estados que vio al probar el runbook.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-page-overview.png" alt-text="Captura de pantalla de la página Trabajo del runbook.":::

1. Cuando el estado del runbook aparezca como **Completado**, seleccione **Salida**. La salida debería ser similar a la de la prueba.

1. Cierre la página **Trabajo** para volver a la página **Información general** del runbook.

1. Seleccione **Trabajos** en **Recursos**. Esta página enumera todos los trabajos creados por el runbook. Solo debería ver un trabajo en la lista ya que solo ejecutó el trabajo una vez.

1. Seleccione el trabajo para abrir la misma página **Trabajo** que vio cuando se inició el runbook. Use esta página para ver los detalles de cualquier trabajo creado para el runbook. Cierre la página **Trabajo** para volver a la página **Información general** del runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Agregar autenticación para administrar recursos de Azure

Ha probado y publicado su runbook, pero hasta ahora no hace nada útil. Quiere que administre recursos de Azure. Sin embargo, no puede hacerlo a menos que se autentique con las credenciales de la suscripción. El runbook usa la identidad administrada asignada por el sistema de la cuenta de Automation para autenticarse en Azure, con el fin de realizar la acción de administración en la máquina virtual. El runbook se puede modificar fácilmente para que use una identidad administrada asignada por el usuario.

1. Seleccione **Información general** y, después, en **Editar** para abrir el editor de texto.

1. Reemplace el código existente por el siguiente:

   ```powershell
   workflow MyFirstRunbook-Workflow
   {
   $resourceGroup = "resourceGroupName"
    
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process
    
   # Connect to Azure with system-assigned managed identity
   $AzureContext = (Connect-AzAccount -Identity).context
    
   # set and store context
   $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
   }
   ```

   Edite la variable `$resourceGroup` con un valor válido que represente el grupo de recursos.

1. Si desea que el runbook se ejecute con la identidad administrada asignada por el sistema, deje el código tal y como está. Si prefiere usar una identidad administrada asignada por el usuario, haga lo siguiente:
    1. En la línea 9, quite `$AzureContext = (Connect-AzAccount -Identity).context`,
    1. Reemplace el valor por `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`.
    1. Escriba el identificador de cliente que obtuvo anteriormente.

1. Seleccione **Guardar** y, luego, **Panel de prueba**.

1. Seleccione **Iniciar** para iniciar la prueba. Cuando termine, verá una salida similar a la siguiente, que muestra información básica de su cuenta. Esta acción confirma que la credencial es válida.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/runbook-auth-output.png" alt-text="Información básica que confirma las credenciales.":::

1. Cierre la página **Probar** para volver al lienzo.

## <a name="add-code-to-start-a-virtual-machine"></a>Incorporación de código para iniciar una máquina virtual

Ahora que el runbook está autenticado en la suscripción a Azure, puede administrar los recursos. Agregue un comando para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual de su suscripción de Azure. Por ahora, codificará ese nombre en el runbook. 

1. Agregue el código siguiente como última línea inmediatamente antes de la llave de cierre. Reemplace `VMName` por el nombre real de la máquina virtual. 

   ```powershell
   Start-AzVM -Name "VMName" -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Pruebe el runbook y confirme que la máquina virtual se ha iniciado. Luego, vuelva al lienzo.

## <a name="add-input-parameters-to-the-runbook"></a>Adición de parámetros de entrada al runbook

Actualmente, el runbook inicia la máquina virtual que ha codificado de forma rígida en el runbook. Será más útil si puede especificar la máquina virtual cuando se inicia el runbook. Agregue parámetros de entrada al runbook para proporcionar esa funcionalidad.

1. Reemplace la línea 3, `$resourceGroup = "resourceGroupName"`, por la siguiente:

    ```powershell
    Param(
        [string]$resourceGroup,
        [string]$VMName
    )
   ```

1. Reemplace el comando `Start-AzVM` anterior por el siguiente:

   ```powershell
   Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Pruebe el runbook y confirme que la máquina virtual se ha iniciado. Luego, vuelva al lienzo.

## <a name="manage-multiple-vms-simultaneously"></a>Administración simultanea de varias máquinas virtuales

Puede utilizar la construcción `ForEach -Parallel` para procesar comandos para cada elemento de una colección simultáneamente. Revise el código para que el runbook ahora:
- Acepte una colección de nombres de máquina virtual.
- Acepte un parámetro para detener o iniciar las máquinas virtuales.
- Realice las acciones en paralelo en todas las máquinas virtuales.

1. Reemplace el código existente por el siguiente:

    ```powershell
    workflow MyFirstRunbook-Workflow
    {
    Param(
        [string]$resourceGroup,
        [string[]]$VMs,
        [string]$action
    )
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
    
    # Start or stop VMs in parallel
    if($action -eq "Start")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Start-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
            }
        }
    elseif ($action -eq "Stop")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Stop-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
            }
        }
    else {
            Write-Output "`r`n Action not allowed. Please enter 'stop' or 'start'."
        }
    }
    ```

1. Si desea que el runbook se ejecute con la identidad administrada asignada por el sistema, deje el código tal y como está. Si prefiere usar una identidad administrada asignada por el usuario, haga lo siguiente:
    1. En la línea 13, quite `$AzureContext = (Connect-AzAccount -Identity).context`.
    1. Reemplácelo por `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`.
    1. Escriba el identificador de cliente que obtuvo anteriormente.

1. Seleccione **Guardar**, luego **Publicar** y, finalmente, **Sí** cuando se le pida confirmación.

1. En la página **Información general**, seleccione **Iniciar**.

1. Rellene los parámetros y seleccione **Aceptar**.

   |Parámetro |Descripción |
   |---|---|
   |RESOURCEGROUP|Escriba el nombre del grupo de recursos de las máquinas virtuales.|
   |Máquinas virtuales|Escriba los nombres de las máquinas virtuales, para lo que debe usar la sintaxis siguiente: `["VM1","VM2","VM3"]`|
   |Acción|Escriba `stop` o `start`.|

1. Vaya a la lista de máquinas virtuales y actualice la página cada pocos segundos. Observe que la acción de cada máquina virtual se produce en paralelo. Sin la palabra clave `-Parallel`, las acciones se habrían realizado secuencialmente. Aunque las máquinas virtuales se iniciarán secuencialmente, cada una de ellas puede alcanzar la fase de **ejecución** en momentos ligeramente diferentes en función de sus características.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando este runbook, elimínelo mediante los siguientes pasos:

1. Vaya a su cuenta de Automation.
1. En **Automatización de procesos**, seleccione **Runbooks**.
1. Seleccione el Runbook.
1. En la página **Información general**, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un runbook de flujo de trabajo de PowerShell. Para examinar los runbooks de Python 3, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Creación de un runbook de Python 3 (versión preliminar)](automation-tutorial-runbook-textual-python-3.md)