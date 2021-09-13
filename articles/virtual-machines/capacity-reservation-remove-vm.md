---
title: Eliminación de una asociación de máquina virtual de un grupo de reserva de capacidad (versión preliminar)
description: Aprenda a quitar una máquina virtual de un grupo de reserva de capacidad.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 2f5537ec3ad34e3f0ad7eff32d32762ed6fedef3
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273313"
---
# <a name="remove-a-vm-association-from-a-capacity-reservation-group-preview"></a>Eliminación de una asociación de máquina virtual de un grupo de reserva de capacidad (versión preliminar)

Este artículo proporciona los pasos necesarios para quitar una asociación de máquina virtual de un grupo de reserva de capacidad. Para obtener más información sobre las reservas de capacidad, vea el [artículo de información general](capacity-reservation-overview.md). 

Dado que la máquina virtual y la reserva de capacidad subyacente lógicamente ocupan capacidad, Azure impone algunas restricciones en este proceso para evitar estados de asignación ambiguos y errores inesperados.  

Hay dos maneras de cambiar una asociación: 
- Opción 1: desasignar la máquina virtual, cambiar la propiedad Grupo de reserva de capacidad y, opcionalmente, reiniciar la máquina virtual
- Opción 2: actualizar la cantidad reservada a cero y cambiar la propiedad Grupo de reserva de capacidad

> [!IMPORTANT]
> La reserva de capacidad está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-for-capacity-reservation"></a>Registro en la reserva de capacidad 

Para usar la característica de reserva de capacidad, debe [registrar la suscripción en la versión preliminar](capacity-reservation-overview.md#register-for-capacity-reservation). El registro puede tardar varios minutos en terminar. Puede usar la CLI de Azure o PowerShell para realizar el registro de la característica.


## <a name="deallocate-the-vm"></a>Desasignación de la máquina virtual

La primera opción es desasignar la máquina virtual, cambiar la propiedad Grupo de reserva de capacidad y, opcionalmente, reiniciar la máquina virtual. 

### <a name="api"></a>[API](#tab/api1)

1. Desasignación de la máquina virtual

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. Actualice la máquina virtual para quitar la asociación con el grupo de reserva de capacidad
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/update?api-version=2021-04-01
    ```
    En el cuerpo de la solicitud, establezca la propiedad `capacityReservationGroup` en vacía para quitar la asociación de la máquina virtual al grupo:

    ```json
     {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":""
            }
        }
    }
    }
    ```

### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Abra [Azure Portal](https://portal.azure.com)
1. Vaya a la máquina virtual y seleccione **Información general**
1. Seleccione **Detener** 
    1. Sabrá que la máquina virtual se ha desasignado cuando el estado cambie a *Detenido (desasignado)*
    1. En este punto del proceso, la máquina virtual todavía está asociada al grupo de reserva de capacidad, lo que se refleja en la propiedad `virtualMachinesAssociated` de la reserva de capacidad 
1. Seleccione **Configuración**.
1. Establezca el valor **Grupo de reserva de capacidad** en *Ninguno*
    - La máquina virtual ya no está asociada al grupo de reserva de capacidad 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Desasigne la máquina virtual

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

    Cuando el estado cambie a **Detenido (desasignado)** , la máquina virtual se ha desasignado.

1. Actualice la máquina virtual para quitar la asociación con el grupo de reserva de capacidad; para ello, establezca la propiedad `CapacityReservationGroupId` en vacía:

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId " "
    ```

Para obtener más información, vaya a los comandos de Azure PowerShell [Stop-AzVM](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm) y [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>Actualización de la cantidad reservada a cero 

La segunda opción conlleva actualizar la cantidad reservada a cero y luego cambiar la propiedad Grupo de reserva de capacidad.

Esta opción funciona bien cuando no se puede desasignar la máquina virtual y cuando ya no se necesita una reserva. Por ejemplo, puede crear una reserva de capacidad para garantizar temporalmente la capacidad durante una implementación a gran escala. Una vez completada, la reserva ya no es necesaria. 

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

1. Actualice la máquina virtual para quitar la asociación con el grupo de reserva de capacidad

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/update?api-version=2021-04-01
    ```

    En el cuerpo de la solicitud, establezca la propiedad `capacityReservationGroup` en vacía para quitar la asociación:
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":""
            }
        }
    }
    } 
    ```

### <a name="portal"></a>[Portal](#tab/portal2)

<!-- no images necessary if steps are straightforward --> 

1. Abra [Azure Portal](https://portal.azure.com)
1. Vaya al grupo de reserva de capacidad y seleccione **Información general**
1. Seleccione **Reservas** 
1. Seleccione **Administrar reserva** en la parte superior de la página 
1. En la hoja *Administrar reservas*:
    1. Escriba `0` en el campo **Instancias**
    1. Seleccione **Guardar**. 
1. Vaya a la máquina virtual y seleccione **Configuración**
1. Establezca el valor **Grupo de reserva de capacidad** en *Ninguno*
    - Observe que la máquina virtual ya no está asociada al grupo de reserva de capacidad

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

>[!NOTE]
> El comando `Update-AzCapacityReservation` no está disponible durante la versión preliminar. Use `New-AzCapacityReservation` para modificar una reserva de capacidad existente.

1. Actualice la cantidad reservada a cero

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 0
    ```

1. Actualice la máquina virtual para quitar la asociación con el grupo de reserva de capacidad; para ello, establezca la propiedad `CapacityReservationGroupId` en vacía:

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId " "
    ```

Para obtener más información, vaya a los comandos de Azure PowerShell [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVM](/powershell/module/az.compute/get-azvm) y [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a asociar un conjunto de escalado a un grupo de reserva de capacidad](capacity-reservation-associate-virtual-machine-scale-set.md)