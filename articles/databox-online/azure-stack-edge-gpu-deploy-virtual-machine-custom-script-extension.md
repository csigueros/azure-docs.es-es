---
title: Uso de extensiones de script personalizado para máquinas virtuales en el dispositivo Azure Stack Edge Pro
description: Describe cómo instalar extensiones de script personalizado en máquinas virtuales (VM) que se ejecutan en un dispositivo Azure Stack Edge Pro mediante plantillas.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 6ed084e26b12851985e2b3b906be1367b7b4a437
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736771"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Implementación de una extensión de script personalizado en máquinas virtuales que se ejecutan en el dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

La extensión de script personalizado descarga y ejecuta scripts o comandos en máquinas virtuales que se ejecutan en los dispositivos Azure Stack Edge Pro. En este artículo se detalla cómo instalar y ejecutar la extensión de script personalizado mediante el uso de una plantilla de Azure Resource Manager.

## <a name="about-custom-script-extension"></a>Acerca de la extensión de script personalizado

La extensión de script personalizado es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage u otra ubicación de Internet accesible, o se pueden proporcionar scripts o comandos al tiempo de ejecución de la extensión.

La extensión de script personalizado se integra con las plantillas de Azure Resource Manager. También puede ejecutarla mediante la CLI de Azure, PowerShell o la API REST de Azure Virtual Machines.

## <a name="os-for-custom-script-extension"></a>SO para la extensión de script personalizado

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>SO admitido para la extensión de script personalizado en Windows

La extensión de script personalizado para Windows se ejecutará en los siguientes sistemas operativos. Otras versiones pueden funcionar, pero no se han probado internamente en máquinas virtuales que se ejecutan en dispositivos Azure Stack Edge Pro.

| Distribución | Versión |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>SO admitido para la extensión de script personalizado en Linux

La extensión de script personalizado para Linux se ejecutará en los siguientes sistemas operativos. Otras versiones pueden funcionar, pero no se han probado internamente en máquinas virtuales que se ejecutan en dispositivos Azure Stack Edge Pro.

| Distribución | Versión |
|---|---|
| Linux: Ubuntu | 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.4, 7.5, 7.7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Prerrequisitos

1. [Descargue las plantillas de máquina virtual y los archivos de parámetros](https://aka.ms/ase-vm-templates) en la máquina cliente. Descomprima la descarga en un directorio que usará como directorio de trabajo.

1. Debe tener una máquina virtual creada e implementada en el dispositivo. Para crear máquinas virtuales, siga todos los pasos de [Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro mediante plantillas](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Si tiene que descargar un script de forma externa de la misma forma que desde GitHub o Azure Storage, durante la configuración de la red de proceso, habilite el puerto conectado a Internet para el proceso. Esto le permite descargar el script.

    En el ejemplo siguiente, el puerto 2 estaba conectado a Internet y se usaba para habilitar la red de proceso. Si ha identificado que Kubernetes no es necesario en el paso anterior, puede omitir la dirección IP del nodo de Kubernetes y la asignación de direcciones IP del servicio externo.

    ![Captura de pantalla del panel Proceso de un dispositivo Azure Stack Edge. La configuración de Proceso del puerto 2 está resaltada.](media/azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Instalación de la extensión de script personalizado

En función del sistema operativo de la máquina virtual, podría instalar la extensión de script personalizado para Windows o para Linux.
 

### <a name="custom-script-extension-for-windows"></a>Extensión de la secuencia de comandos personalizada para Windows

Para implementar la extensión de script personalizado para Windows para una máquina virtual que se ejecuta en el dispositivo, edite el archivo de parámetros `addCSExtWindowsVM.parameters.json` y, a continuación, implemente la plantilla `addCSextensiontoVM.json`.

#### <a name="edit-parameters-file"></a>Edición del archivo de parámetros

El archivo `addCSExtWindowsVM.parameters.json` toma los parámetros siguientes:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Proporcione el nombre de la máquina virtual, el nombre de la extensión y el comando que quiere ejecutar.

Este es el archivo de parámetros de ejemplo que se usó en este artículo.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Implementar plantilla

Implemente la plantilla `addCSextensiontoVM.json`. Esta plantilla implementa la extensión en una máquina virtual existente. Ejecute el siguiente comando:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> La implementación de la extensión es un trabajo de larga duración y tarda unos 10 minutos en completarse.

Este es una salida de ejemplo:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Seguimiento de la implementación

Para comprobar el estado de implementación de las extensiones de una máquina virtual determinada, ejecute el comando siguiente: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Este es una salida de ejemplo:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Una vez completada la implementación, `ProvisioningState` cambia a `Succeeded`.

La salida de la extensión se registra en archivos que se encuentran en la siguiente carpeta de la máquina virtual de destino. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Los archivos especificados se descargan en la siguiente carpeta de la máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
donde *n* es un entero decimal que puede variar entre las ejecuciones de la extensión. El valor 1.* coincide con el valor `typeHandlerVersion` actual y real de la extensión. Por ejemplo, el directorio real de esta instancia era `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0`. 


En esta instancia, el comando a ejecutar para la extensión personalizada era: `md C:\\Users\\Public\\Documents\\test`. Cuando la extensión se haya instalado correctamente, puede comprobar que el directorio se ha creado en la máquina virtual en la ruta de acceso especificada en el comando. 


### <a name="custom-script-extension-for-linux"></a>Extensión de script personalizado para Linux

Para implementar la extensión de script personalizado para Windows para una máquina virtual que se ejecuta en el dispositivo, edite el archivo de parámetros `addCSExtLinuxVM.parameters.json` y, a continuación, implemente la plantilla `addCSExtensiontoVM.json`.

#### <a name="edit-parameters-file"></a>Edición del archivo de parámetros

El archivo `addCSExtLinuxVM.parameters.json` toma los parámetros siguientes:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Proporcione el nombre de la máquina virtual, el nombre de la extensión y el comando que quiere ejecutar.

Este es un archivo de parámetros de ejemplo que se usó en este artículo:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> La implementación de la extensión es un trabajo de larga duración y tarda unos 10 minutos en completarse.

Este es una salida de ejemplo:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute` se estableció para crear un archivo `file2.txt` en el directorio `/home/Administrator` y el contenido del archivo es `some text`. En este caso, puede comprobar que el archivo se creó en la ruta de acceso especificada.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Seguimiento del estado de implementación    
    
Template Deployment es un trabajo de larga duración. Para comprobar el estado de implementación de las extensiones de una máquina virtual determinada, abra otra sesión de PowerShell (ejecutar como administrador). Ejecute el siguiente comando: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Este es una salida de ejemplo: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Una vez completada la implementación, `ProvisioningState` cambia a `Succeeded`.

La salida de la ejecución de las extensiones se registra en el archivo siguiente:`/var/lib/waagent/custom-script/download/0/`.


## <a name="remove-custom-script-extension"></a>Eliminación de la extensión de script personalizado

Para quitar la extensión de script personalizado, use el siguiente comando:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Este es una salida de ejemplo:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Pasos siguientes

[Cmdlets de Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
