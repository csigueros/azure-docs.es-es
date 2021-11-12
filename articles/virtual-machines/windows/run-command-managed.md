---
title: Ejecución de scripts en una máquina virtual Windows de Azure con la característica administrada Ejecutar comando (versión preliminar)
description: En este tema se explica cómo ejecutar scripts en una máquina virtual Windows de Azure mediante la característica Ejecutar comando.
services: automation
ms.service: virtual-machines
ms.collection: windows
author: cynthn
ms.author: cynthn
ms.date: 10/28/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7a1617fea3ea7de71a040852f4906ecffe1b0432
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557896"
---
# <a name="preview-run-scripts-in-your-windows-vm-by-using-managed-run-commands"></a>Versión preliminar: Ejecución de scripts en la máquina virtual Windows con la característica administrada Ejecutar comando

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles 

> [!IMPORTANT]
> Actualmente la característica administrada **Ejecutar comando** está en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La característica Ejecutar comando usa el agente de máquina virtual (VM) para ejecutar scripts en una máquina virtual Windows de Azure. Puede usar estos scripts para la administración general de máquinas o aplicaciones. Pueden ayudarle a diagnosticar y corregir rápidamente problemas de acceso a la máquina virtual y de red, así como a revertir la máquina virtual a un estado correcto.

La característica administrada *actualizada* Ejecutar comando usa el mismo canal de agente de máquina virtual para ejecutar scripts y proporciona las siguientes mejoras con respecto a la característica [Ejecutar comando original orientada a la acción](run-command.md): 
- Compatibilidad con la característica actualizada Ejecutar comando mediante una plantilla de implementación de ARM 
- Ejecución en paralelo de varios scripts 
- Ejecución secuencial de scripts 
- Posible cancelación del script RunCommand  
- Tiempo de espera del script especificado por el usuario 
- Compatibilidad con scripts de larga duración (horas o días) 
- Entrega de secretos (parámetros, contraseñas) de forma segura


## <a name="register-for-preview"></a>Registro en la versión preliminar

Debe registrar la suscripción para poder usar la característica administrada Ejecutar comando durante la versión preliminar pública. Vaya a [Configuración de características en vista previa en una suscripción de Azure](../../azure-resource-manager/management/preview-features.md) para obtener instrucciones de registro y use el nombre de característica `RunCommandPreview`.

## <a name="azure-cli"></a>la CLI de Azure 

En los siguientes ejemplos use [az vm run-command](/cli/azure/vm/run-command) para ejecutar un script de shell en una máquina virtual Windows de Azure.

### <a name="execute-a-script-with-the-vm"></a>Ejecución de un script con la máquina virtual
Este comando entrega el script a la máquina virtual, lo ejecuta y devuelve la salida capturada.

```azurecli-interactive
az vm run-command create --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" --script "Write-Host Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>Enumeración de todos los recursos RunCommand implementados en una máquina virtual 
Este comando devuelve una lista completa de las instancias de Ejecutar comando implementadas previamente junto con sus propiedades. 

```azurecli-interactive
az vm run-command list --name "myVM" --resource-group "myRG"
```

### <a name="get-execution-status-and-results"></a>Obtención del estado de ejecución y los resultados 
Este comando recupera el progreso actual de la ejecución, incluida la salida más reciente, la hora de inicio y finalización, el código de salida y el estado final de la ejecución.

```azurecli-interactive
az vm run-command show --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" –expand
```

### <a name="delete-runcommand-resource-from-the-vm"></a>Eliminación del recurso RunCommand de la máquina virtual
Quite el recurso RunCommand implementado anteriormente en la máquina virtual. Si la ejecución del script sigue en curso, finaliza. 

```azurecli-interactive
az vm run-command delete --name "myRunCommand" --vm-name "myVM" --resource-group "myRG"
```


## <a name="powershell"></a>PowerShell 

### <a name="execute-a-script-with-the-vm"></a>Ejecución de un script con la máquina virtual
Este comando entrega el script a la máquina virtual, lo ejecuta y devuelve la salida capturada.

```powershell-interactive
Set-AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" – Script "Write-Host Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>Enumeración de todos los recursos RunCommand implementados en una máquina virtual 
Este comando devuelve una lista completa de las instancias de Ejecutar comando implementadas previamente junto con sus propiedades.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM"
```

### <a name="get-execution-status-and-results"></a>Obtención del estado de ejecución y los resultados 
Este comando recupera el progreso actual de la ejecución, incluida la salida más reciente, la hora de inicio y finalización, el código de salida y el estado final de la ejecución.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" -Status
```

### <a name="delete-runcommand-resource-from-the-vm"></a>Eliminación del recurso RunCommand de la máquina virtual
Quite el recurso RunCommand implementado anteriormente en la máquina virtual. Si la ejecución del script sigue en curso, finaliza. 

```powershell-interactive
Remove-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName"
```
 

## <a name="rest-api"></a>API DE REST 

Para implementar una nueva instancia de Ejecutar comando, ejecute PUT directamente en la máquina virtual y especifique un nombre único para la instancia de Ejecutar comando. 

```rest
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01
```

```json
{ 
"location": "<location>", 
"properties": { 
    "source": { 
        "script": "Write-Host Hello World!", 
        "scriptUri": "<URI>",  
        "commandId": "<Id>"  
        }, 
    "parameters": [ 
        { 
            "name": "param1",
            "value": "value1" 
            }, 
        { 
            "name": "param2", 
            "value": "value2" 
            } 
        ], 
    "protectedParameters": [ 
        { 
            "name": "secret1", 
            "value": "value1" 
            }, 
        { 
            "name": "secret2", 
            "value": "value2" 
            } 
        ], 
    "runAsUser": "userName",
    "runAsPassword": "userPassword", 
    "timeoutInSeconds": 3600, 
    "outputBlobUri": "<URI>", 
    "errorBlobUri": "<URI>"  
    }
}
```

### <a name="notes"></a>Notas
 
- Puede proporcionar un script insertado, un URI de script o un [id. de comando](run-command.md#available-commands) de script integrado como origen de entrada
- Solo se admite un tipo de entrada de origen en una ejecución de comando 
- Ejecutar comando admite la salida en blobs de Storage, que se pueden usar para almacenar salidas de script grandes
- Ejecutar comando admite la salida de errores en blobs de Storage 


### <a name="list-running-instances-of-run-command-on-a-vm"></a>Enumeración de instancias en ejecución de Ejecutar comando en una máquina virtual 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands?api-version=2019-12-01
``` 

### <a name="get-output-details-for-a-specific-run-command-deployment"></a>Obtención de detalles de salida de una implementación específica de Ejecutar comando 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?$expand=instanceView&api-version=2019-12-01 
```

### <a name="cancel-a-specific-run-command-deployment"></a>Cancelación de una implementación específica de Ejecutar comando 

Para cancelar una implementación en ejecución, puede usar PUT o PATCH en la instancia en ejecución de Ejecutar comando y especificar un script en blanco en el cuerpo de la solicitud. Esto cancela la ejecución en curso. 

También puede eliminar la instancia de Ejecutar comando.  

```rest
DELETE /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01 
```

### <a name="deploy-scripts-in-an-ordered-sequence"></a>Implementación de scripts en una secuencia ordenada 

Para implementar scripts secuencialmente, use una plantilla de implementación y especifique una relación `dependsOn` entre scripts secuenciales. 

```json
{ 
    "type": "Microsoft.Compute/virtualMachines/runCommands", 
    "name": "secondRunCommand", 
    "apiVersion": "2019-12-01", 
    "location": "[parameters('location')]", 
    "dependsOn": <full resourceID of the previous other Run Command>, 
    "properties": { 
        "source": {  
            "script": "Write-Host Hello World!"  
        }, 
        "timeoutInSeconds": 60  
    }
} 
```

### <a name="execute-multiple-run-commands-sequentially"></a>Ejecución secuencial de varias instancias de Ejecutar comando 

De manera predeterminada, si implementa varios recursos RunCommand mediante una plantilla de implementación, se ejecutan simultáneamente en la máquina virtual. Si tiene una dependencia en los scripts y un orden de ejecución preferido, puede usar la propiedad `dependsOn` para que se ejecuten secuencialmente. 

En este ejemplo, **secondRunCommand** se ejecuta después de **firstRunCommand**. 

```json
{
   "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "resources":[
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/firstRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
         ],
         "properties":{
            "source":{
               "script":"Write-Host First: Hello World!"
            },
            "parameters":[
               {
                  "name":"param1",
                  "value":"value1"
               },
               {
                  "name":"param2",
                  "value":"value2"
               }
            ],
            "timeoutInSeconds":20
         }
      },
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/secondRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'),'runcommands/firstRunCommand')]"
         ],
         "properties":{
            "source":{
               "scriptUrl":"http://github.com/myscript.ps1"
            },
            "timeoutInSeconds":60
         }
      }
   ]
}
```


## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre otras maneras de ejecutar comandos y scripts de forma remota en la máquina virtual, consulte [Ejecución de scripts en una máquina virtual Windows](run-scripts-in-vm.md).
