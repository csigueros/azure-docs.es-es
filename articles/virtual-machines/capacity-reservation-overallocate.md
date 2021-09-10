---
title: Asignación excesiva de reserva de capacidad en Azure (versión preliminar)
description: Obtenga información sobre cómo funciona la sobreasignación en lo que respecta a la reserva de capacidad.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 0486263551246a794f90867621be0e87d42747c6
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273310"
---
# <a name="overallocating-capacity-reservation-preview"></a>Asignación excesiva de reserva de capacidad (versión preliminar)

Azure permite la asociación de máquinas virtuales adicionales más allá del recuento reservado de una reserva de capacidad para facilitar la ráfaga y otros escenarios de escalado horizontal, sin la sobrecarga de la administración en torno a los límites de la capacidad reservada. La única diferencia es que el número de máquinas virtuales más allá de la cantidad reservada no recibe la ventaja del Acuerdo de Nivel de Servicio de disponibilidad de capacidad. Siempre que Azure tenga capacidad disponible que cumpla los requisitos de la máquina virtual, las asignaciones adicionales se realizarán correctamente. 

La vista de instancia de un grupo de reserva de capacidad proporciona una instantánea del uso de cada reserva de capacidad de miembro. Puede usar la vista de instancia para ver cómo funciona la sobreasignación. 

En este artículo se da por supuesto que ha creado un grupo de reserva de capacidad (`myCapacityReservationGroup`), una reserva de capacidad de miembro (`myCapacityReservation`) y una máquina virtual (*myVM1*) que está asociada al grupo. Vaya a los artículos sobre cómo [crear una reserva de capacidad](capacity-reservation-create.md) y cómo [asociar una máquina virtual a una reserva de capacidad](capacity-reservation-associate-vm.md) para obtener más detalles.

> [!IMPORTANT]
> La reserva de capacidad está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="register-for-capacity-reservation"></a>Registro para la reserva de capacidad 

Antes de poder usar la característica Reserva de capacidad, debe [registrar la suscripción para la versión preliminar](capacity-reservation-overview.md#register-for-capacity-reservation). El registro puede tardar varios minutos en terminar. Puede usar la CLI de Azure o PowerShell para completar el registro de la característica.

## <a name="instance-view-for-capacity-reservation-group"></a>Vista de instancia del grupo de reserva de capacidad 

La vista de instancia de un grupo de reserva de capacidad tendrá el siguiente aspecto: 

```rest
GET 
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/myCapacityReservationGroup?$expand=instanceview&api-version=2021-04-01
```

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
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

Digamos que creamos otra máquina virtual denominada *myVM2* y la asociamos con el grupo de reserva de capacidad anterior. 

La vista de instancia del grupo de reserva de capacidad ahora tendrá el siguiente aspecto: 

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            }, 
 { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
                            }, 
{ 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
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

Observe que la longitud de `virtualMachinesAllocated` (2) es mayor que `capacity` (1). Este estado válido se conoce como *sobreasignado*. 

> [!IMPORTANT]
> Azure no detendrá las asignaciones solo porque una reserva de capacidad se consuma por completo. Las reglas de escalado automático, el escalado horizontal temporal y los requisitos relacionados funcionarán más allá de la cantidad de capacidad reservada, siempre y cuando Azure tenga capacidad disponible.  


## <a name="states-and-considerations"></a>Estados y consideraciones  

Hay tres estados válidos para una reserva de capacidad determinada: 

| Estado  | Estado  | Consideraciones  |
|---|---|---|
| Capacidad reservada disponible  | Longitud de `virtualMachinesAllocated` < `capacity`  | ¿Se necesita toda la capacidad reservada? Opcionalmente, reduzca la capacidad para reducir los costos.  |
| Reserva consumida  | Longitud de `virtualMachinesAllocated` == `capacity`  | Las máquinas virtuales adicionales no recibirán el Acuerdo de Nivel de Servicio de capacidad a menos que se desasignen algunas máquinas virtuales existentes. Opcionalmente, intente aumentar la capacidad para que las máquinas virtuales planeadas adicionales reciban un Acuerdo de Nivel de Servicio.  |
| Reserva sobreasignada  | Longitud de `virtualMachinesAllocated` > `capacity`  | Las máquinas virtuales adicionales no recibirán el Acuerdo de Nivel de Servicio de capacidad. Además, la cantidad de máquinas virtuales (longitud de `virtualMachinesAllocated` – `capacity`) no recibirá un Acuerdo de Nivel de Servicio de capacidad si se desasigna. Opcionalmente, aumente la capacidad para agregar el Acuerdo de Nivel de Servicio de capacidad a más de las máquinas virtuales existentes.  |


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtenga información sobre cómo quitar máquinas virtuales de una reserva de capacidad](capacity-reservation-remove-vm.md)