---
title: Solución de errores de las extensiones de máquina virtual de Windows
description: Más información sobre la solución de problemas de la extensión de máquina virtual de Microsoft Azure
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49e0edf880efbf07e541d935e9b111b037a1f08d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292789"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Solución de problemas de la extensión de máquina virtual de Microsoft Azure.
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Consulta del estado de la extensión
Las plantillas de Azure Resource Manager se pueden ejecutar desde Azure PowerShell. Cuando se ejecute la plantilla, el estado de extensión se puede ver desde el Explorador de recursos de Azure o las herramientas de la línea de comandos.

Este es un ejemplo:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Este es la salida de ejemplo:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Solución de problemas de errores de extensión

### <a name="verify-that-the-vm-agent-is-running-and-ready"></a>Comprobación de que el agente de máquina virtual está en ejecución y listo
El agente de máquina virtual es necesario para administrar, instalar y ejecutar extensiones. Si el agente de máquina virtual no está en ejecución o no informa de un estado Listo a la plataforma Azure, las extensiones no funcionarán correctamente.

Consulte las páginas siguientes para solucionar problemas del agente de máquina virtual:
- [Solución de problemas con el agente invitado de Windows Azure](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent) para una máquina virtual de Windows.
- [Solución de problemas del agente de Azure Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) para una máquina virtual de Linux.

### <a name="check-for-your-specific-extension-troubleshooting-guide"></a>Búsqueda de la guía de solución de problemas de la extensión específica
Algunas extensiones tienen una página específica que describe cómo solucionar problemas. Puede encontrar la lista de estas extensiones y páginas en [Extensiones de solución de problemas](./overview.md#troubleshoot-extensions).

### <a name="view-the-extensions-status"></a>Visualización del estado de la extensión
Como se explicó anteriormente, el estado de la extensión se puede encontrar mediante la ejecución del cmdlet de PowerShell:
```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

o el comando de la CLI:
```azurecli
az vm extension show -g <RG Name> --vm-name <VM Name>  --name <Extension Name>
```

o en Azure Portal, para ello, se examina la hoja de la máquina virtual/Configuración/Extensiones. A continuación, puede hacer clic en la extensión y comprobar su estado y mensaje.


### <a name="rerun-the-extension-on-the-vm"></a>Volver a ejecutar la extensión en la máquina virtual
Si está ejecutando los scripts en la máquina virtual mediante la extensión de script personalizada, a veces podría recibir un error en el que la máquina virtual se creó correctamente, pero en el que el script ha generado un error. En estas condiciones, la manera que se recomienda para recuperarse de este error es quitar la extensión y volver a ejecutar la plantilla.
Nota: En el futuro, esta funcionalidad se mejoraría para eliminar la necesidad de desinstalar la extensión.

#### <a name="remove-the-extension-from-azure-powershell"></a>Eliminación de la extensión de Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Cuando se ha quitado la extensión, la plantilla puede volver a ejecutarse para ejecutar los scripts en la máquina virtual.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Desencadenamiento de un nuevo GoalState en la máquina virtual
Es posible que observe que una extensión no se ha ejecutado o que no se puede ejecutar porque falta el "generador de certificados CRP de Windows Azure" (ese certificado se utiliza para proteger el transporte de la configuración protegida de la extensión).
Ese certificado se volverá a generar automáticamente al reiniciar el agente invitado de Windows desde dentro de la máquina virtual:
- Abra el Administrador de tareas.
- Vaya a la pestaña Detalles.
- Busque el proceso WindowsAzureGuestAgent.exe.
- Haga clic con el botón derecho y seleccione "Finalizar tarea". El proceso se reiniciará de forma automática.


También puede desencadenar un nuevo objeto GoalState en la máquina virtual mediante la ejecución de "Volver a aplicar la VM". [Volver a aplicar](/rest/api/compute/virtualmachines/reapply) la VM es una API que se introdujo en 2020 para volver a aplicar el estado de una máquina virtual. Se recomienda hacerlo cuando se pueda permitir un breve periodo de inactividad en la máquina virtual. Aunque Volver a aplicar no provoca un reinicio de la máquina virtual y la inmensa mayoría de las veces las llamadas a Volver a aplicar no reiniciarán la máquina virtual, hay un riesgo muy pequeño de que se aplique alguna otra actualización pendiente al modelo de máquina virtual cuando Volver a aplicar desencadene un nuevo estado de objetivo y que el otro cambio podría requerir un reinicio. 

Azure Portal:

En el portal, seleccione la máquina virtual y, en el panel izquierdo, debajo de **Soporte y solución de problemas**, seleccione **Volver a implementar y volver a aplicar** y, a continuación, seleccione **Volver a aplicar**.


Azure PowerShell *(reemplace el nombre del grupo de recursos y el nombre de la máquina virtual por sus valores)* :

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

CLI de Azure *(reemplace el nombre del grupo de recursos y el nombre de la máquina virtual por sus valores)* :

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Si no funcionó "Volver a aplicar la VM", puede agregar un nuevo disco de datos vacío a la máquina virtual desde el portal de administración de Azure y, después, quitarlo cuando el certificado se haya agregado de nuevo.


### <a name="look-at-the-extension-logs-inside-the-vm"></a>Consulta los registros de extensión dentro de la máquina virtual

Si los pasos anteriores no funcionaron y la extensión sigue en estado de error, el siguiente paso es ver sus registros dentro de la máquina virtual.

En una máquina virtual de **Windows**, los registros de extensión normalmente residirán en 
```
C:\WindowsAzure\Logs\Plugins
```
Y los archivos de configuración y estado de la extensión estarán en 
```
C:\Packages\Plugins
```
<br/>

En una máquina virtual de **Linux**, los registros de extensión normalmente residirán en 
```
/var/log/azure/
```
Y los archivos de configuración y estado de la extensión estarán en 
```
/var/lib/waagent/
```


Cada extensión es diferente, pero normalmente siguen principios similares:

Los paquetes de extensión y los archivos binarios se descargan en la máquina virtual (por ejemplo, _"/var/lib/waagent/custom-script/download/1"_ para Linux o _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Downloads\0"_ para Windows). 

Su configuración y valores se pasan desde la plataforma Azure al controlador de extensiones a través del agente de máquina virtual (por ejemplo, _"/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/config"_ para Linux o _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\RuntimeSettings"_ para Windows)

Los controladores de extensión dentro de la máquina virtual escriben en un archivo de estado (por ejemplo, _"/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/status/1.status"_ para Linux o _"C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Status"_ para Windows) que luego se notificarán a la plataforma Azure. Ese estado es el que se notifica a través de PowerShell, la CLI o en la hoja de extensión de la máquina virtual de Azure Portal.

También escriben registros detallados de su ejecución (por ejemplo, _"/var/log/azure/custom-script/handler.log"_ para Linux o _"C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\CustomScriptHandler.log"_ para Windows).


### <a name="if-the-vm-is-recreated-from-an-existing-vm"></a>Si la máquina virtual se vuelve a crear a partir de una máquina virtual existente

Podría ocurrir que está creando una máquina virtual de Azure basada en un disco especializado procedente de otra máquina virtual de Azure. En ese caso, es posible que la máquina virtual anterior contenga extensiones, por lo que sobrarán archivos binarios, registros y archivos de estado. El nuevo modelo de máquina virtual no conocerá los estados de las extensiones de la máquina virtual anterior y podría notificar un estado incorrecto para estas extensiones. Se recomienda encarecidamente quitar las extensiones de la máquina virtual anterior antes de crear la nueva y, después, volver a instalar estas extensiones una vez creada la nueva máquina virtual.
Lo mismo puede ocurrir cuando se crea una imagen generalizada a partir de una máquina virtual de Azure existente. Le invitamos a quitar extensiones para evitar el estado incoherente de las extensiones.