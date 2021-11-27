---
title: Aumento de las cuotas de vCPU de máquinas virtuales de acceso puntual
description: Obtenga información sobre cómo solicitar aumentos para las cuotas de vCPU de acceso puntual en Azure Portal.
ms.date: 11/15/2021
ms.topic: how-to
ms.openlocfilehash: edbea31db418e3feae640e18be137b3ca09fac1f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725435"
---
# <a name="increase-spot-vcpu-quotas"></a>Aumento de las cuotas de vCPU de máquinas virtuales de acceso puntual

Azure Resource Manager admite dos tipos de cuotas de vCPU para las máquinas virtuales:

- cuotas de vCPU estándar
- cuotas de vCPU de acceso puntual

Las cuotas de vCPU estándar se aplican a las VM de pago por uso y a las instancias reservadas de VM. Se aplican en dos niveles para cada suscripción, en cada región:

- El primer nivel es la cuota total de vCPU regional.
- El segundo nivel es la cuota de vCPU de la familia de VM, como las vCPU de la serie D.

Las cuotas de vCPU de acceso puntual se aplican a [máquinas virtuales (VM) de acceso puntual](../../virtual-machines/spot-vms.md) en todas las familias de máquinas virtuales (SKU).

En este artículo se muestra cómo solicitar aumentos de cuota para vCPU de acceso puntual. También puede solicitar aumentos para las [cuotas de vCPU de la familia de máquinas virtuales](per-vm-quota-requests.md) o las [cuotas de vCPU por región](regional-quota-requests.md).

## <a name="special-considerations"></a>Consideraciones especiales

Al considerar las necesidades de vCPU de acceso puntual, tenga en cuenta lo siguiente:

- Cada vez que se implementa una nueva máquina virtual de acceso puntual, el total de uso de vCPU nuevo y existente para todas las instancias de máquina virtual de acceso puntual no debe superar el límite de cuota de vCPU de acceso puntual aprobado. Si se supera la cuota de acceso puntual, no se puede implementar la máquina virtual de acceso puntual.

- Siempre que Azure necesite recuperar la capacidad, su infraestructura expulsará las máquinas virtuales de acceso puntual.

## <a name="increase-a-spot-vcpu-quota"></a>Aumento de las cuotas de vCPU de acceso puntual

Para solicitar un aumento de cuota para una cuota de vCPU de acceso puntual:

1. En Azure Portal, seleccione **Suscripciones**.
1. Seleccione la suscripción cuya cuota quiere aumentar.
1. En el panel izquierdo, seleccione **Uso y cuotas**.
1. En el panel principal, busque la opción de acceso puntual y seleccione **Total de vCPU de acceso puntual regionales** para la región que desea aumentar.
1. En **Detalles de la cuota**, escriba el nuevo límite de cuota.

   En el ejemplo siguiente se solicita un nuevo límite de cuota de 103 para las vCPU de acceso puntual en todas las vCPU de la familia de máquinas virtuales de la región Oeste de EE. UU.

   :::image type="content" source="media/resource-manager-core-quotas-request/spot-quota.png" alt-text="Captura de pantalla de una solicitud de aumento de cuota de vCPU de acceso puntual en Azure Portal." lightbox="media/resource-manager-core-quotas-request/spot-quota.png":::

1. Selecciona **Guardar y continuar**.

Se revisará la solicitud y se le notificará si esta se ha aprobado o rechazado. Esto suele ocurrir en cuestión de minutos. Si se rechaza la solicitud, verá un vínculo donde puede abrir una solicitud de soporte técnico para que un ingeniero pueda ayudarle con el aumento.

## <a name="increase-a-spot-quota-from-help--support"></a>Aumento de una cuota de acceso puntual desde Ayuda y soporte técnico

Para solicitar un aumento de la cuota de vCPU de acceso puntual desde **Ayuda y soporte técnico**, cree una nueva solicitud de soporte técnico en Azure Portal.

1. En **Tipo de problema**, seleccione **Límites de servicio y suscripción (cuotas)** .
1. En **Suscripción**, seleccione la suscripción cuya cuota quiere aumentar.
1. Seleccione **Tipo de cuota** y, luego, **Compute-VM (cores-vCPUs) subscription limit increases** (Aumento del límite de suscripción para VM de proceso [núcleos/vCPU]).

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Captura de pantalla que muestra una solicitud de soporte técnico para aumentar una cuota de vCPU de la familia de VM en Azure Portal.":::

Desde allí, siga los pasos descritos anteriormente para completar la solicitud de aumento de cuota de acceso puntual.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [Máquinas virtuales de acceso puntual de Azure](../../virtual-machines/spot-vms.md).
- Conozca los [límites, las cuotas y las restricciones de suscripción y servicios de Azure](/azure/azure-resource-manager/management/azure-subscription-service-limits).
