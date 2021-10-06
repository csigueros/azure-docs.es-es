---
title: Eliminación de una asociación de conjunto de escalado de máquinas virtuales de un grupo de reserva de capacidad (versión preliminar)
description: Aprenda a quitar un conjunto de escalado de máquinas virtuales de un grupo de reserva de capacidad.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 2d8f9c7c73b4cb5d0f617893a7d981b94d30b344
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553104"
---
# <a name="remove-a-virtual-machine-scale-set-association-from-a-capacity-reservation-group"></a>Eliminación de una asociación de conjunto de escalado de máquinas virtuales de un grupo de reserva de capacidad 

Este artículo proporciona los pasos necesarios para quitar una asociación de conjunto de escalado de máquinas virtuales de un grupo de reserva de capacidad. Para obtener más información sobre las reservas de capacidad, vea el [artículo de información general](capacity-reservation-overview.md). 

Dado que la máquina virtual y la reserva de capacidad subyacente lógicamente ocupan capacidad, Azure impone algunas restricciones en este proceso para evitar estados de asignación ambiguos y errores inesperados.  

Hay dos maneras de cambiar una asociación: 
- Opción 1: desasignar el conjunto de escalado de máquinas virtuales, cambiar la propiedad Grupo de reserva de capacidad en el nivel de conjunto de escalado y luego actualizar las máquinas virtuales subyacentes
- Opción 2: actualizar la cantidad reservada a cero y cambiar la propiedad Grupo de reserva de capacidad

> [!IMPORTANT]
> La reserva de capacidad está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-for-capacity-reservation"></a>Registro en la reserva de capacidad 

Para usar la característica de reserva de capacidad, debe [registrar la suscripción en la versión preliminar](capacity-reservation-overview.md#register-for-capacity-reservation). El registro puede tardar varios minutos en terminar. Puede usar la CLI de Azure o PowerShell para completar el registro de la característica.

> [!NOTE]
> La reserva de capacidad a petición está disponible para conjuntos de escalado de máquinas virtuales en modo de orquestación uniforme solo en regiones seleccionadas. Para comprobar si la región es compatible, vaya a [Seguimiento de implementación de conjuntos de escalado de máquinas virtuales uniforme](https://aka.ms/vmssuniformdeploymenttracker).


## <a name="deallocate-the-virtual-machine-scale-set"></a>Desasignación del conjunto de escalado de máquinas virtuales

La primera opción es desasignar el conjunto de escalado de máquinas virtuales, cambiar la propiedad Grupo de reserva de capacidad en el nivel de conjunto de escalado y luego actualizar las máquinas virtuales subyacentes. 

Vaya a [Directivas de actualización](#upgrade-policies) para obtener más información sobre las actualizaciones automáticas, graduales y manuales. 

### <a name="api"></a>[API](#tab/api1)

1. Desasignación del conjunto de escalado de máquinas virtuales

    ```rest
    POST  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. Actualice el conjunto de escalado de máquinas virtuales para quitar la asociación con el grupo de reserva de capacidad
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```
    En el cuerpo de la solicitud, establezca la propiedad `capacityReservationGroup` en NULL para quitar la asociación del conjunto de escalado de máquinas virtuales con el grupo:

    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null    
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Desasigne el conjunto de escalado de máquinas virtuales. El siguiente código desasigna todas las máquinas virtuales del conjunto de escalado: 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    ```

1. Actualice el conjunto de escalado para quitar la asociación con el grupo de reserva de capacidad. Al establecer la propiedad `CapacityReservationGroupId` en NULL, se quita la asociación del conjunto de escalado al grupo de reserva de capacidad: 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

Para obtener más información, vaya a los comandos de Azure PowerShell [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), [Get-AzVmss](/powershell/module/az.compute/get-azvmss) y [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>Actualización de la cantidad reservada a cero 

La segunda opción conlleva actualizar la cantidad reservada a cero y luego cambiar la propiedad Grupo de reserva de capacidad.

Esta opción funciona bien cuando no se puede desasignar el conjunto de escalado de máquinas virtuales y cuando ya no se necesita una reserva. Por ejemplo, puede crear una reserva de capacidad para garantizar temporalmente la capacidad durante una implementación a gran escala. Una vez completada, la reserva ya no es necesaria. 

Vaya a [Directivas de actualización](#upgrade-policies) para obtener más información sobre las actualizaciones automáticas, graduales y manuales. 

### <a name="api"></a>[API](#tab/api2)

1. Actualización de la cantidad reservada a cero 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/CapacityReservations/{CapacityReservationName}?api-version=2021-04-01
    ```

    En el cuerpo de la solicitud, incluya lo siguiente:
    
    ```json
    {
    "sku": 
        {
        "capacity": 0
        }
    } 
    ```
    
    Observe que la propiedad `capacity` está establecida en 0 o superior.

1. Actualice el conjunto de escalado de máquinas virtuales para quitar la asociación con el grupo de reserva de capacidad

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```

    En el cuerpo de la solicitud, establezca la propiedad `capacityReservationGroup` en NULL para quitar la asociación:
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Actualice la cantidad reservada a cero:

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

2. Actualice el conjunto de escalado para quitar la asociación con el grupo de reserva de capacidad; para ello, establezca la propiedad `CapacityReservationGroupId` en NULL: 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

Para obtener más información, vaya a los comandos de Azure PowerShell [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVmss](/powershell/module/az.compute/get-azvmss) y [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="upgrade-policies"></a>Directivas de actualización

- **Actualización automática**: en este modo, las instancias de máquina virtual del conjunto de escalado se desasocian automáticamente del grupo de reserva de capacidad sin que sea necesaria ninguna acción adicional por su parte.
- **Actualización gradual**: en este modo, las instancias de máquina virtual del conjunto de escalado se desasocian del grupo de reserva de capacidad sin que sea necesaria ninguna acción adicional por su parte. Pero se actualizan en lotes con un tiempo de pausa opcional entre ellas.
- **Actualización manual**: en este modo, no les sucede nada a las instancias de máquina virtual del conjunto de escalado cuando este se actualiza. Tiene que quitar cada máquina virtual del conjunto de escalado individualmente mediante su [actualización con el modelo del conjunto de escalado más reciente](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre la asignación excesiva de reserva de capacidad](capacity-reservation-overallocate.md)
