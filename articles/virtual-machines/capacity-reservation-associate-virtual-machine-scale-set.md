---
title: Asociación de un conjunto de escalado de máquinas virtuales a un grupo de reserva de capacidad (versión preliminar)
description: Aprenda a asociar un conjunto de escalado de máquinas virtuales nuevo o existente a un grupo de reserva de capacidad.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: fe9b445a1eccb6c897a1fd7f383e487714c23966
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575941"
---
# <a name="associate-a-virtual-machine-scale-set-to-a-capacity-reservation-group-preview"></a>Asociación de un conjunto de escalado de máquinas virtuales a un grupo de reserva de capacidad (versión preliminar)

Virtual Machine Scale Sets tiene dos modos: 

- **Modo de orquestación uniforme:** en este modo, los conjuntos de escalado de máquinas virtuales usan un perfil de máquina virtual o una plantilla para escalar verticalmente hasta la capacidad deseada. Aunque existe cierta capacidad para administrar o personalizar instancias de máquina virtual individuales, el modo de orquestación uniforme usa instancias de máquina virtual idénticas. Estas instancias se exponen a través de las API de máquina virtual de conjuntos de escalado de máquinas virtuales y no son compatibles con los comandos estándar de la API de máquina virtual de IaaS de Azure. Dado que el conjunto de escalado realiza todas las operaciones de máquina virtual reales, las reservas se asocian directamente con el conjunto de escalado de máquinas virtuales. Una vez que el conjunto de escalado está asociado con la reserva, todas las asignaciones de máquinas virtuales posteriores se realizarán en la reserva. 
- **Modo de orquestación flexible:** en este modo, obtiene más flexibilidad al administrar las instancias individuales de máquina virtual del conjunto de escalado de máquinas virtuales, ya que pueden usar las API de máquina virtual de IaaS de Azure estándar en lugar de usar la interfaz del conjunto de escalado. Este modo no funcionará con Reserva de capacidad durante la versión preliminar pública.

Para obtener más información sobre estos modos, vaya a [Modos de orquestación de Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md). En el resto de este artículo se explica cómo asociar un conjunto de escalado de máquinas virtuales uniforme a un grupo de reserva de capacidad. 

> [!IMPORTANT]
> Reserva de capacidad está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-for-capacity-reservation"></a>Registro en la reserva de capacidad 

Para usar la característica de reserva de capacidad, debe [registrar la suscripción en la versión preliminar](capacity-reservation-overview.md#register-for-capacity-reservation). El registro puede tardar varios minutos en terminar. Puede usar la CLI de Azure o PowerShell para completar el registro de la característica.

## <a name="limitations-of-scale-sets-in-uniform-orchestration"></a>Limitaciones de los conjuntos de escalado en la orquestación uniforme 

- Para que Virtual Machine Scale Sets de la orquestación uniforme sea compatible con Reserva de capacidad, la propiedad `singlePlacementGroup` debe establecerse en *False*. 
- La opción de disponibilidad **Propagación fija estática** de los conjuntos de escalado uniformes de varias zonas no se admite con Reserva de capacidad. Esta opción requiere el uso de 5 dominios de error, mientras que las reservas solo admiten hasta 3 dominios de error para tamaños de uso general. El enfoque recomendado consiste en usar la opción **Propagación máxima**, que distribuye máquinas virtuales entre tantos dominios de error como sea posible dentro de cada zona. Si es necesario, establezca una configuración de dominio de error personalizada de 3 o menos. 

Hay otras restricciones al usar Reserva de capacidad. Para obtener la lista completa, consulte la [información general de las reservas de capacidad](capacity-reservation-overview.md).  

## <a name="associate-a-new-virtual-machine-scale-set-to-a-capacity-reservation-group"></a>Asociación de un nuevo conjunto de escalado de máquinas virtuales a un grupo de reserva de capacidad


### <a name="api"></a>[API](#tab/api1)  

Para asociar un nuevo conjunto de escalado de máquinas virtuales uniforme a un grupo de reserva de capacidad, cree la siguiente solicitud PUT para el proveedor *Microsoft.Compute*:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
```

Agregue la propiedad `capacityReservationGroup` en `virtualMachineProfile`, como se muestra a continuación: 

```json
{ 
    "name": "<VMScaleSetName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}", 
    "type": "Microsoft.Compute/virtualMachineScaleSets", 
    "location": "eastus", 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "tier": "Standard", 
        "capacity": 3 
}, 
"properties": { 
    "virtualMachineProfile": { 
        "capacityReservation": { 
            "capacityReservationGroup":{ 
                "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroup/{CapacityReservationGroupName}" 
            } 
         }, 
        "osProfile": { 
            … 
        }, 
        "storageProfile": { 
            … 
        }, 
        "networkProfile": { 
            …,
            "extensionProfile": { 
                … 
            } 
        } 
    } 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell1) 

Use `New-AzVmss` para crear un nuevo conjunto de escalado de máquinas virtuales y agregue la propiedad `CapacityReservationGroupId` para asociar el conjunto de escalado a un grupo de reserva de capacidad existente. En el ejemplo siguiente se crea un conjunto de escalado uniforme para una máquina virtual Standard_Ds1_v2 en la ubicación Este de EE. UU. y se asocia el conjunto de escalado a un grupo de reserva de capacidad.

```powershell-interactive
$vmssName = <"VMSSNAME">
$vmPassword = ConvertTo-SecureString <"PASSWORD"> -AsPlainText -Force
$vmCred = New-Object System.Management.Automation.PSCredential(<"USERNAME">, $vmPassword)
New-AzVmss
–Credential $vmCred
-VMScaleSetName $vmssName
-ResourceGroupName "myResourceGroup"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
-PlatformFaultDomainCount 2
```

Para obtener más información, vaya al comando de Azure PowerShell [New-AzVmss](/powershell/module/az.compute/new-azvmss).

### <a name="arm-template"></a>[Plantilla ARM](#tab/arm1)

Una  [plantilla de ARM](../azure-resource-manager/templates/overview.md) es un archivo de notación de objetos JavaScript (JSON) que define la infraestructura y la configuración del proyecto. La plantilla usa sintaxis declarativa. En la sintaxis declarativa, se describe la implementación deseada sin escribir la secuencia de comandos de programación para crearla. 

Las plantillas de Resource Manager permiten implementar grupos de recursos relacionados. En una sola plantilla, puede crear grupos de reserva de capacidad y reservas de capacidad. Puede implementar plantillas mediante Azure Portal, la CLI de Azure o Azure PowerShell, o bien desde las canalizaciones de integración continua o entrega continua (CI/CD). 

Si su entorno cumple los requisitos previos y ya está familiarizado con el uso de plantillas de ARM, use esta plantilla [Create Virtual Machine Scale Sets with Capacity Reservation](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json) (Crear Virtual Machine Scale Sets con Reserva de capacidad). 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-virtual-machine-scale-set-to-capacity-reservation-group"></a>Asociación de un conjunto de escalado de máquinas virtuales existente a un grupo de reserva de capacidad 

Para la versión preliminar pública, a fin de asociar un conjunto de escalado de máquinas virtuales uniforme existente al grupo de reserva de capacidad, es necesario desasignar primero el conjunto de escalado y, a continuación, realizar la asociación en el momento de la reasignación. Esto garantiza que todas las máquinas virtuales del conjunto de escalado consuman la reserva de capacidad en el momento de la reasignación.

### <a name="important-notes-on-upgrade-policies"></a>Notas importantes sobre las directivas de actualización 

- **Actualización automática**: en este modo, las instancias de máquina virtual del conjunto de escalado se asocian automáticamente con el grupo de reserva de capacidad sin que sea necesaria ninguna acción adicional por su parte. Cuando las máquinas virtuales del conjunto de escalado se reasignan, comienzan a consumir la capacidad reservada.
- **Actualización gradual**: en este modo, las instancias de máquina virtual del conjunto de escalado se asocian con el grupo de reserva de capacidad sin que sea necesaria ninguna acción adicional por su parte. Sin embargo, se actualizan en lotes con un tiempo de pausa opcional entre ellas. Cuando las máquinas virtuales del conjunto de escalado se reasignan, comienzan a consumir la capacidad reservada.
- **Actualización manual**: en este modo, no les sucede nada a las instancias de máquina virtual del conjunto de escalado cuando el conjunto de escalado de máquinas virtuales se asocia a un grupo de reserva de capacidad. Tendrá que realizar actualizaciones individuales a cada máquina virtual del conjunto de escalado [actualizándola con el modelo del conjunto de escalado más reciente](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="api"></a>[API](#tab/api2)

1. Desasigne el conjunto de escalado de máquinas virtuales. 

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. Agregue la propiedad `capacityReservationGroup` al modelo del conjunto de escalado. Cree la siguiente solicitud PUT para el proveedor *Microsoft.Compute*:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
    ```

    En el cuerpo de la solicitud, incluya la propiedad `capacityReservationGroup`:

    ```json
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
             "capacityReservation": {
                      "capacityReservationGroup": {
                            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
                      }
                }
        }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2) 

1. Desasigne el conjunto de escalado de máquinas virtuales. 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup”
    -VMScaleSetName "myVmss”
    ```

1. Asocie el conjunto de escalado al grupo de reserva de capacidad. 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
    ```

Para obtener más información, vaya a los comandos de Azure PowerShell [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), [Get-AzVmss](/powershell/module/az.compute/get-azvmss) y [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="view-virtual-machine-scale-set-association-with-instance-view"></a>Visualización de la asociación del conjunto de escalado de máquinas virtuales con la vista de instancia 

Una vez que el conjunto de escalado de máquinas virtuales uniforme se asocie al grupo de reserva de capacidad, todas las asignaciones de máquinas virtuales posteriores se producirán en la reserva de capacidad. Azure busca automáticamente la reserva de capacidad correspondiente en el grupo y consume una ranura reservada. 

### <a name="api"></a>[API](#tab/api3) 

La *vista de instancia* del grupo de reserva de capacidad reflejará las nuevas máquinas virtuales del conjunto de escalado en las propiedades `virtualMachinesAssociated` & `virtualMachinesAllocated` como se muestra a continuación: 

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceview&api-version=2021-04-01 
```

```json
{ 
    "name": "<CapacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus" 
}, 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "<CapacityReservationName>", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
                            } 
                        ] 
                    },
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "2021-05-25T15:12:10.4165243+00:00" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell3) 

Vea el conjunto de escalado de máquinas virtuales y la asociación del grupo de reserva de capacidad con la vista de instancia mediante PowerShell. 

```powershell-interactive
$CapRes=
Get-AzCapacityReservationGroup
-ResourceGroupName <"ResourceGroupName">
-Name <"CapacityReservationGroupName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
``` 

Para obtener más información, vaya al comando de Azure PowerShell [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationGroup).

### <a name="portal"></a>[Portal](#tab/portal3)

1. Abra [Azure Portal](https://portal.azure.com)
1. Vaya al grupo de reserva de capacidad
1. Seleccione **Recursos** en **Configuración** a la izquierda
1. En la tabla, podrá ver todas las máquinas virtuales del conjunto de escalado asociadas con el grupo de reserva de capacidad
--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="region-and-availability-zones-considerations"></a>Consideraciones sobre las regiones y Availability Zones 

Los conjuntos de escalado de máquinas virtuales se pueden crear de forma regional o en una o varias zonas de Availability Zones para protegerlos frente a errores de nivel de centro de datos. Obtenga más información sobre los conjuntos de escalado de máquinas virtuales de varias zonas, consulte [Virtual Machine Scale Sets que usan Availability Zones](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md).  

 
>[!IMPORTANT]
> La ubicación (región y Availability Zones) del conjunto de escalado de máquinas virtuales y el grupo de reserva de capacidad deben coincidir para que la asociación se lleve a cabo correctamente. Para un conjunto de escalado regional, la región debe coincidir entre el conjunto de escalado y el grupo de reserva de capacidad. Para un conjunto de escalado zonal, las regiones y las zonas deben coincidir entre el conjunto de escalado y el grupo de reserva de capacidad. 


Cuando un conjunto de escalado se distribuye entre varias zonas, siempre intenta implementarse uniformemente en las zonas de Availability Zones incluidas. Debido a esa implementación uniforme, un grupo de reserva de capacidad siempre debe tener la misma cantidad de máquinas virtuales reservadas en cada zona. Para ilustrar por qué esto es importante, tenga en cuenta el ejemplo siguiente.   

En este ejemplo, cada zona tiene reservada una cantidad diferente. Supongamos que el conjunto de escalado de máquinas virtuales se escala horizontalmente hasta 75 instancias. Puesto que el conjunto de escalado siempre intentará implementarse uniformemente entre zonas, la distribución de VM debe tener el siguiente aspecto: 

| Zona  | Cantidad reservada  | No. de máquinas virtuales del conjunto de escalado en cada zona  | Cantidad reservada sin usar  | Con exceso de asignación   |
|---|---|---|---|---|
| 1  | 40  | 25  | 15  | 0  |
| 2  | 20  | 25  | 0  | 5  |
| 3  | 15  | 25  | 0  | 10  |

En este caso, el conjunto de escalado incurre en un costo adicional para 15 instancias no usadas en Zona 1. El escalado horizontal también se basa en 5 máquinas virtuales en Zona 2 y 10 máquinas virtuales en Zona 3 que no protege la reserva de capacidad. Si cada zona tuviera 25 instancias de capacidad reservadas, Reserva de capacidad protegería las 75 máquinas virtuales y la implementación no incurriría en ningún costo adicional para las instancias no usadas.  

Puesto que las reservas se pueden sobreasignar, el conjunto de escalado puede seguir realizando el escalado con normalidad más allá de los límites de la reserva. La única diferencia es que las máquinas virtuales asignadas por encima de la cantidad reservada no están cubiertas por el Acuerdo de Nivel de Servicio de reserva de capacidad. Para obtener más información, vaya a [Sobreasignación de la reserva de capacidad](capacity-reservation-overallocate.md).


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtenga información sobre cómo quitar una asociación del conjunto de escalado de una reserva de capacidad](capacity-reservation-remove-virtual-machine-scale-set.md)
