---
title: Modificación de una reserva de capacidad en Azure (versión preliminar)
description: Aprenda a modificar una reserva de capacidad.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 8b6e2ba3c65b5fd521bdb6326069ce5d8be05599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564892"
---
# <a name="modify-a-capacity-reservation-preview"></a>Modificación de una reserva de capacidad (versión preliminar)

Después de crear un grupo de reserva de capacidad y una reserva de capacidad, puede que quiera modificar las reservas. En este artículo se explica cómo hacer lo siguiente mediante la API, Azure Portal y PowerShell. 

> [!div class="checklist"]
> * Actualizar el número de instancias reservadas en una reserva de capacidad 
> * Cambiar el tamaño de las VM asociadas a un grupo de reserva de capacidad
> * Eliminar el grupo de reserva de capacidad y la reserva de capacidad

> [!IMPORTANT]
> La reserva de capacidad está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="update-the-number-of-instances-reserved"></a>Actualización del número de instancias reservadas 

Actualice el número de instancias de máquina virtual reservadas en una reserva de capacidad.  

> [!IMPORTANT]
> En ocasiones excepcionales, cuando Azure no puede satisfacer la solicitud para aumentar la cantidad reservada para las reservas de capacidad existentes, es posible que una reserva entre en estado de *Error* y deje de estar disponible hasta que [la cantidad se restaure a la cantidad original](#restore-instance-quantity).

### <a name="api"></a>[API](#tab/api1)

```rest
    PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
``` 
    
En el cuerpo de la solicitud, actualice la propiedad `capacity` al nuevo recuento que desea reservar: 
    
```json
{
    "sku":
    {
        "capacity": 5
    }
} 
```

Tenga en cuenta que la propiedad `capacity` se ha establecido en 5 en este ejemplo.


### <a name="portal"></a>[Portal](#tab/portal1) 

1. Abra [Azure Portal](https://portal.azure.com).
1. Vaya al grupo de reserva de capacidad.
1. Seleccione **Información general**. 
1. Seleccione **Reservas**. 
1. Seleccione **Administrar reservas** en la parte superior. 
1. En la página *Administrar reservas*, escriba en el campo **Instancias** la cantidad que se va a reservar. 
1. Seleccione **Guardar**. 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

Para actualizar la cantidad reservada, use `New-AzCapacityReservation` con la propiedad `capacityToReserve` actualizada.

```powershell-interactive
Update-AzCapacityReservation
-ResourceGroupName "myResourceGroup"
-ReservationGroupName "myCapacityReservationGroup"
-Name "myCapacityReservation"
-CapacityToReserve 5
```

Para obtener más información, vaya al comando [Update-AzCapacityReservation](/powershell/module/az.compute/update-azcapacityreservation) de Azure PowerShell.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="resize-vms-associated-with-a-capacity-reservation-group"></a>Cambio del tamaño de las VM asociadas a un grupo de reserva de capacidad 

Si la máquina virtual a la que se va a cambiar el tamaño está asociada actualmente a un grupo de reservas de capacidad, y ese grupo no tiene una reserva para el tamaño de destino, cree una nueva reserva para ese tamaño o quite la máquina virtual del grupo de reservas antes de cambiar el tamaño. 

Compruebe si el tamaño de destino forma parte del grupo de reservas: 

### <a name="api"></a>[API](#tab/api2)

1. Obtenga los nombres de todas las reservas de capacidad dentro del grupo.
 
    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "<CapacityReservationGroupName>", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
        "type": "Microsoft.Compute/capacityReservationGroups", 
        "location": "eastUS", 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "capacityReservations": [ 
                { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}" 
                }, 
    { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName2}" 
                } 
            ] 
        } 
    } 
    ```

1. Averigüe el tamaño de VM reservado para cada reserva. El ejemplo siguiente corresponde a `capacityReservationName1`, pero puede repetir este paso para otras reservas.

    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "capacityReservationName1", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}", 
        "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
        "location": "eastUS", 
        "sku": { 
            "name": "Standard_D2s_v3", 
            "capacity": 3 
        }, 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "reservationId": "<reservationId>", 
            "provisioningTime": "<provisioningTime>", 
            "provisioningState": "Succeeded" 
        } 
    }  
    ```

1. Tenga en cuenta lo siguiente. 
    1. Si el tamaño de VM de destino no forma parte del grupo, [cree una nueva reserva de capacidad](capacity-reservation-create.md) para la VM de destino. 
    1. Si el tamaño de VM de destino ya existe en el grupo, [cambie el tamaño de la máquina virtual](resize-vm.md). 

### <a name="portal"></a>[Portal](#tab/portal2)

1. Abra [Azure Portal](https://portal.azure.com).
1. Vaya al grupo de reserva de capacidad.
1. Seleccione **Información general**. 
1. Seleccione **Reservas**. 
1. Observe el *tamaño de VM* reservado para cada reserva. 
    1. Si el tamaño de VM de destino no forma parte del grupo, [cree una nueva reserva de capacidad](capacity-reservation-create.md) para la VM de destino. 
    1. Si el tamaño de VM de destino ya existe en el grupo, [cambie el tamaño de la máquina virtual](resize-vm.md). 

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Obtenga los nombres de todas las reservas de capacidad dentro del grupo con `Get-AzCapacityReservationGroup`.

    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. En la respuesta, busque los nombres de todas las reservas de capacidad.

1. Ejecute los siguientes comandos para averiguar los tamaños de VM reservados para cada reserva.

    ```powershell-interactive
    $CapRes =
    Get-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "mycapacityReservation"
    
    $CapRes.Sku
    ```

1. Tenga en cuenta lo siguiente. 
    1. Si el tamaño de VM de destino no forma parte del grupo, [cree una nueva reserva de capacidad](capacity-reservation-create.md) para la VM de destino. 
    1. Si el tamaño de VM de destino ya existe en el grupo, [cambie el tamaño de la máquina virtual](resize-vm.md). 


Para más información, consulte los comandos de Azure PowerShell [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup) y [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="delete-a-capacity-reservation-group-and-capacity-reservation"></a>Eliminación de un grupo de reserva de capacidad y la reserva de capacidad 

Azure permite eliminar un grupo de reserva de capacidad cuando se han eliminado todas las reservas de capacidad miembro y no hay máquinas virtuales asociadas al grupo.  

Para eliminar una reserva de capacidad, primero averigüe todas las máquinas virtuales asociadas a ella. La lista de máquinas virtuales está disponible en la propiedad `virtualMachinesAssociated`. 

### <a name="api"></a>[API](#tab/api3)

En primer lugar, busque todas las máquinas virtuales asociadas al grupo de reserva de capacidad y desasócielas.
 
```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01
``` 

```json
{ 
    "name": "<capacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}/capacityReservations/{capacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
            }, 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName2}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "{capacityReservationName}", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
                            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
}  
```
En la respuesta anterior, busque los nombres de todas las máquinas virtuales en la propiedad `virtualMachinesAssociated` y quítelas del grupo de reserva de capacidad mediante los pasos descritos en [Eliminación de una asociación de VM a una reserva de capacidad](capacity-reservation-remove-vm.md). 

Una vez que se quiten todas las máquinas virtuales del grupo de reserva de capacidad, elimine las reservas de capacidad miembro:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```

Por último, elimine el grupo de reserva de capacidad primario.

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
```


### <a name="portal"></a>[Portal](#tab/portal3)

1. Abra [Azure Portal](https://portal.azure.com).
1. Vaya al grupo de reserva de capacidad.
1. Seleccione **Recursos**. 
1. Averigüe todas las máquinas virtuales asociadas al grupo.
1. [Desasocie todas las máquinas virtuales.](capacity-reservation-remove-vm.md)
1. Elimine todas las reservas de capacidad del grupo.
    1. Vaya a **Reservas**.
    1. Seleccione cada reserva. 
    1. Seleccionar **Eliminar**
1. Elimine el grupo de reserva de capacidad.
    1. Vaya al grupo de reserva de capacidad.
    1. Seleccione **Eliminar** en la parte superior de la página.


### <a name="powershell"></a>[PowerShell](#tab/powershell3)

Busque todas las máquinas virtuales asociadas al grupo de reserva de capacidad y desasócielas.

1. Ejecute lo siguiente: 
    
    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. En la respuesta anterior, busque los nombres de todas las máquinas virtuales en la propiedad `VirtualMachinesAssociated` y quítelas del grupo de reserva de capacidad mediante los pasos descritos en [Eliminación de una asociación de máquina virtual a una reserva de capacidad](capacity-reservation-remove-vm.md).

1. Una vez que se hayan quitado todas las máquinas virtuales del grupo, continúe con los pasos siguientes. 

1. Elimine la reserva de capacidad:

    ```powershell-interactive
    Remove-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    ```

1. Elimine el grupo de reserva de capacidad:

    ```powershell-interactive
    Remove-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

Para más información, consulte los comandos de Azure PowerShell [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup), [Remove-AzCapacityReservation](/powershell/module/az.compute/remove-azcapacityreservation) y [Remove-AzCapacityReservationGroup](/powershell/module/az.compute/remove-azcapacityreservationgroup).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="restore-instance-quantity"></a>Restauración de la cantidad de la instancia 

Una solicitud bien formada para reducir la cantidad reservada siempre debe completarse de manera correcta, independientemente del número de máquinas virtuales asociadas a la reserva. Sin embargo, aumentar la cantidad reservada puede requerir más cuota para que Azure cumpla la solicitud de capacidad adicional. En escenarios excepcionales, cuando Azure no pueda satisfacer la solicitud para aumentar la cantidad reservada para las reservas existentes, es posible que una reserva entre en estado de *Error* y deje de estar disponible hasta que la cantidad reservada se restaure a la cantidad original.  

> [!NOTE]
> Si una reserva está en estado de *Error*, todas las VM asociadas a la reserva seguirán funcionando con normalidad.  

Por ejemplo, supongamos que `myCapacityReservation` tiene una cantidad reservada de 5. Se solicitan 5 instancias adicionales, lo que hace que la cantidad total reservada sea igual a 10. Sin embargo, debido a una situación de capacidad restringida en la región, Azure no puede satisfacer la cantidad adicional de 5 solicitada. En este caso, `myCapacityReservation` no podrá cumplir su estado previsto de cantidades reservadas de 10, y pasará a un estado de *Error*. 

Para resolver este error, siga estos pasos para buscar el valor reservado de cantidad anterior:  

1. En Azure Portal, diríjase a [Application Change Analysis](https://ms.portal.azure.com/#blade/Microsoft_Azure_ChangeAnalysis/ChangeAnalysisBaseBlade). 
1. Seleccione la **Suscripción**, el **Grupo de recursos** y el **Intervalo de tiempo** correspondientes en los filtros.
    - Solo puede volver hasta 14 días atrás en el filtro **Intervalo de tiempo**. 
1. Busque el nombre de la reserva de capacidad.
1. Busque el cambio en la propiedad `sku.capacity` para esa reserva. 
    - La cantidad antigua reservada será el valor de la columna **Valor anterior**. 

Actualice `myCapacityReservation` a la cantidad anterior reservada. Una vez actualizada, la reserva estará disponible de inmediato para su uso con las máquinas virtuales. 


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtenga información sobre cómo quitar máquinas virtuales de una reserva de capacidad](capacity-reservation-remove-vm.md)
