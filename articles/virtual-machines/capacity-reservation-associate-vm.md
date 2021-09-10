---
title: Asociación de una máquina virtual a un grupo de reserva de capacidad (versión preliminar)
description: Aprenda a asociar una máquina virtual nueva o existente a un grupo de reserva de capacidad.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 28c1d138cc4a2993caf2b162b9363bc33b6666b7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273309"
---
# <a name="associate-a-vm-to-a-capacity-reservation-group-preview"></a>Asociación de una máquina virtual a un grupo de reserva de capacidad (versión preliminar) 

Este artículo le guía por los pasos necesarios para asociar una máquina virtual nueva o existente a un grupo de reserva de capacidad. Para obtener más información sobre las reservas de capacidad, consulte el [artículo de información general](capacity-reservation-overview.md). 

> [!IMPORTANT]
> La reserva de capacidad está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-for-capacity-reservation"></a>Registro para la reserva de capacidad 

Antes de poder usar la característica Reserva de capacidad, debe [registrar la suscripción para la versión preliminar](capacity-reservation-overview.md#register-for-capacity-reservation). El registro puede tardar varios minutos en terminar. Puede usar la CLI de Azure o PowerShell para completar el registro de la característica.

## <a name="associate-a-new-vm"></a>Asociación de una nueva máquina virtual

Para asociar una nueva máquina virtual al grupo de reserva de capacidad, se debe hacer referencia explícitamente al grupo como una propiedad de la máquina virtual. Esta referencia protege la reserva correspondiente del grupo contra el consumo accidental de aplicaciones y cargas de trabajo menos críticas que no están diseñadas para usarla.  

### <a name="api"></a>[API](#tab/api1)

Para agregar la propiedad `capacityReservationGroup` a una máquina virtual, cree la siguiente solicitud PUT para el proveedor *Microsoft.Compute*:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
```

En el cuerpo de la solicitud, incluya la propiedad `capacityReservationGroup` como se muestra a continuación:

```json
{ 
  "location": "eastus", 
  "properties": { 
    "hardwareProfile": { 
      "vmSize": "Standard_D2s_v3" 
    }, 
    … 
   "CapacityReservation":{ 
    "capacityReservationGroup":{ 
        "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}" 
    } 
    "storageProfile": { 
    … 
    }, 
    "osProfile": { 
    … 
    }, 
    "networkProfile": { 
     …     
    } 
  } 
} 
```

### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Abra [Azure Portal](https://portal.azure.com)
1. En la barra de búsqueda, escriba **máquina virtual**
1. En *Servicios*, seleccione **Máquinas virtuales**
1. En la página *Máquinas virtuales*, seleccione **Agregar** y, después, **Máquina virtual**
1. En la pestaña *Aspectos básicos*, en *Detalles del proyecto*, seleccione la **suscripción** correcta y, a continuación, elija crear un nuevo **grupo de recursos** o usar uno existente
1. En *Detalles de instancia*, escriba el **nombre** de máquina virtual y elija su **región**
1. Elija una **imagen** y el **tamaño de VM**
1. En *Cuenta de administrador*, especifique un **nombre de usuario** y una **contraseña**
    1. La contraseña debe tener al menos 12 caracteres y cumplir los requisitos de complejidad definidos
1. En *Reglas de puerto de entrada*, elija **Permitir los puertos seleccionados** y luego seleccione **RDP** (3389) y **HTTP** (80) en la lista desplegable
1. Vaya a la *sección Avanzado*
1. En la lista desplegable **Reservas de capacidad**, seleccione el grupo de reserva de capacidad con el que desea que se asocie la máquina virtual
1. Seleccione el botón **Revisar y crear** 
1. Después de que se ejecute la validación, seleccione el botón **Crear** 
1. Una vez finalizada la implementación, seleccione **Ir al recurso**


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

Use `New-AzVM` para crear una nueva máquina virtual y agregue la propiedad `CapacityReservationGroupId` para asociarla a un grupo de reserva de capacidad existente. En el ejemplo siguiente se crea una máquina virtual Standard_D2s_v3 en la ubicación Este de EE. UU. y se asocia la máquina virtual a un grupo de reserva de capacidad.

```powershell-interactive
New-AzVm
-ResourceGroupName "myResourceGroup"
-Name "myVM"
-Location "eastus"
-VirtualNetworkName "myVnet"
-SubnetName "mySubnet"
-SecurityGroupName "myNetworkSecurityGroup"
-PublicIpAddressName "myPublicIpAddress"
-OpenPorts 80,3389
-Size "Standard_D2s_v3"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
```

Para obtener más información, vaya al comando de Azure PowerShell [New-AzVM](/powershell/module/az.compute/new-azvm).

### <a name="arm-template"></a>[Plantilla ARM](#tab/arm1)

Una  [plantilla de ARM](/azure/azure-resource-manager/templates/overview) es un archivo de notación de objetos JavaScript (JSON) que define la infraestructura y la configuración del proyecto. La plantilla usa sintaxis declarativa. En la sintaxis declarativa, se describe la implementación deseada sin escribir la secuencia de comandos de programación para crearla. 

Las plantillas de Resource Manager permiten implementar grupos de recursos relacionados. En una sola plantilla, puede crear grupos de reserva de capacidad y reservas de capacidad. Puede implementar plantillas mediante Azure Portal, la CLI de Azure o Azure PowerShell, o bien desde las canalizaciones de integración continua o entrega continua (CI/CD). 

Si su entorno cumple los requisitos previos y ya está familiarizado con el uso de plantillas de ARM, use esta plantilla [Create VM with Capacity Reservation](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json) (Crear VM con Reserva de capacidad). 


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-vm"></a>Asociación de una máquina virtual existente 

Aunque Reserva de capacidad está en versión preliminar, para asociar una máquina virtual existente a un grupo de reserva de capacidad, es necesario desasignar la máquina virtual en primer lugar y, a continuación, llevar a cabo la asociación en el momento de la reasignación. Este proceso garantiza que la máquina virtual consuma uno de los puntos vacíos de la reserva. 

### <a name="api"></a>[API](#tab/api2)

1. Desasigne la máquina virtual. 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. Agregue la propiedad `capacityReservationGroup` a la máquina virtual. Cree la siguiente solicitud PUT para el proveedor *Microsoft.Compute*:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
    ```

    En el cuerpo de la solicitud, incluya la propiedad `capacityReservationGroup`: 
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
            }
        }
    }
    }
    ```


### <a name="portal"></a>[Portal](#tab/portal2)

1. Abra [Azure Portal](https://portal.azure.com)
1. Vaya a la máquina virtual
1. Seleccione **Información general** a la izquierda
1. Seleccione **Detener** en la parte superior de la página para desasignar la máquina virtual 
1. Vaya a **Configuraciones** a la izquierda
1. En la lista desplegable **Grupo de reserva de capacidad**, seleccione el grupo con el que desea que se asocie la máquina virtual 


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Desasignación de la máquina virtual

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

1. Asociación de la máquina virtual a un grupo de reserva de capacidad

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId "subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}"
    ```

Para obtener más información, vaya a los comandos de Azure PowerShell [Stop-AzVM](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm) y [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="view-vm-association-with-instance-view"></a>Visualización de la asociación de VM con la vista de instancia 

Una vez establecida la propiedad `capacityReservationGroup`, ahora existe una asociación entre la máquina virtual y el grupo. Azure busca automáticamente la reserva de capacidad correspondiente en el grupo y consume una ranura reservada. La *vista de instancia* de Reserva de capacidad reflejará la nueva máquina virtual en la propiedad `virtualMachinesAllocated` como se muestra a continuación: 

### <a name="api"></a>[API](#tab/api3)

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{capacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01 
```

```json
{
   "name":"{CapacityReservationGroupName}",
   "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}",
   "type":"Microsoft.Compute/capacityReservationGroups",
   "location":"eastus",
   "properties":{
      "capacityReservations":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/ {CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}"
         }
      ],
      "virtualMachinesAssociated":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
         }
      ],
      "instanceView":{
         "capacityReservations":[
            {
               "name":"{CapacityReservationName}",
               "utilizationInfo":{
                  "virtualMachinesAllocated":[
                     {
                        "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
                     }
                  ]
               },
               "statuses":[
                  {
                     "code":"ProvisioningState/succeeded",
                     "level":"Info",
                     "displayStatus":"Provisioning succeeded",
                     "time":"2021-05-25T15:12:10.4165243+00:00"
                  }
               ]
            }
         ]
      }
   }
} 
``` 
### <a name="powershell"></a>[PowerShell](#tab/powershell3)

```powershell-interactive
$CapRes=
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName] <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
```

Para obtener más información, vaya al comando de Azure PowerShell [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).


### <a name="portal"></a>[Portal](#tab/portal3)

1. Abra [Azure Portal](https://portal.azure.com)
1. Vaya al grupo de reserva de capacidad
1. Seleccione **Recursos** en **Configuración** a la izquierda
1. Consulte la tabla para ver todas las máquinas virtuales asociadas al grupo de reserva de capacidad  

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Eliminación de una asociación de máquinas virtuales a un grupo de reserva de capacidad](capacity-reservation-remove-vm.md)