---
title: Aumento de las cuotas de vCPU regionales
description: Aprenda a solicitar un aumento del límite de cuota de vCPU para una región en Azure Portal.
ms.date: 11/15/2021
ms.topic: how-to
ms.custom: references-regions
ms.openlocfilehash: 45337a6ce029fe3bf8442ac5343ce9145faf904f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717099"
---
# <a name="increase-regional-vcpu-quotas"></a>Aumento de las cuotas de vCPU regionales

Azure Resource Manager admite dos tipos de cuotas de vCPU para las máquinas virtuales:

- cuotas de vCPU estándar
- cuotas de vCPU de acceso puntual

Las cuotas de vCPU estándar se aplican a las VM de pago por uso y a las instancias de VM reservadas. Se aplican en dos niveles para cada suscripción, en cada región:

- El primer nivel es la cuota total de vCPU regional.
- El segundo nivel es la cuota de vCPU de la familia de VM, como las vCPU de la serie D.

En este artículo se muestra cómo solicitar aumentos de cuota de vCPU regionales para todas las VM de una región determinada. También puede solicitar aumentos para las [cuotas de vCPU de la familia de VM ](per-vm-quota-requests.md) o las [cuotas de vCPU de acceso puntual](spot-quota.md).

## <a name="special-considerations"></a>Consideraciones especiales

Al considerar las necesidades de vCPU entre regiones, tenga en cuenta lo siguiente:

- Las cuotas de vCPU regionales se aplican en todas las series de VM de una región determinada. Como resultado, decida cuántas vCPU necesita en cada región de la suscripción. Si no tiene suficiente cuota de vCPU en cada región, envíe una solicitud para aumentar la cuota de vCPU en esa región. Por ejemplo, si necesita 30 vCPU en el Oeste de Europa y no tiene suficiente cuota, solicite específicamente una cuota para 30 vCPU en el Oeste de Europa. Al hacerlo, las cuotas de vCPU de la suscripción en otras regiones no aumentan. Solo el límite de cuota de vCPU del Oeste de Europa se incrementa a 30 vCPU.

- Cuando se solicita un aumento en la cuota de vCPU para una serie de VM, Azure aumenta el límite de cuota de vCPU regional en la misma cantidad.

- Cuando crea una nueva suscripción, es posible que el valor predeterminado para la cantidad total de vCPU en una región no sea igual a la cuota de vCPU predeterminada total para todas las series de VM individuales. Esta discrepancia puede dar lugar a una suscripción con suficiente cuota para cada serie individual de máquinas virtuales que quiera implementar. Sin embargo, es posible que no haya cuota suficiente para el total de vCPU regionales de todas las implementaciones. En este caso, debe enviar una solicitud para aumentar explícitamente el límite de cuota de las cuotas de vCPU regionales.

## <a name="increase-a-regional-vcpu-quota"></a>Aumento de las cuotas de vCPU regionales

Para solicitar una cuota de vCPU regional de **Uso y cuotas**:

1. En Azure Portal, seleccione **Suscripciones**.

1. Seleccione la suscripción cuya cuota quiere aumentar.

1. En el panel izquierdo, seleccione **Uso y cuotas**. Use los filtros para ver la cuota por uso.

1. En el panel principal, seleccione **Total Regional vCPUs** (Total de vCPU regionales) y, a continuación, seleccione el icono de lápiz. En el ejemplo siguiente se muestra la cuota de vCPU regional para la región Noroeste de EE. UU.

   :::image type="content" source="media/resource-manager-core-quotas-request/regional-quota-total.png" alt-text="Captura de pantalla de la pantalla Uso y cuotas en la que se muestran las vCPU regionales totales en Azure Portal." lightbox="media/resource-manager-core-quotas-request/regional-quota-total.png":::

1. En **Detalles de la cuota**, escriba el nuevo límite de cuota y, a continuación, seleccione **Guardar y continuar**.

   Se revisará la solicitud y se le notificará si esta se ha aprobado o rechazado. Esto suele ocurrir en cuestión de minutos. Si se rechaza la solicitud, verá un vínculo donde puede abrir una solicitud de soporte técnico para que un ingeniero pueda ayudarle con el aumento.

> [!TIP]
> También puede solicitar varios aumentos al mismo tiempo. Para obtener más información, consulte [Aumento de varias cuotas de CPU de la familia de máquinas virtuales en una solicitud](per-vm-quota-requests.md#increase-multiple-vm-family-cpu-quotas-in-one-request).

## <a name="increase-a-regional-quota-from-help--support"></a>Aumento de una cuota regional de Ayuda y soporte técnico

Para solicitar un aumento de la cuota de vCPU estándar por familia de VM desde **Ayuda y soporte técnico**, cree una nueva solicitud de soporte técnico en Azure Portal.

1. En **Tipo de problema**, seleccione **Límites de servicio y suscripción (cuotas)** .
1. En **Suscripción**, seleccione la suscripción cuya cuota quiere aumentar.
1. Seleccione **Tipo de cuota** y, luego, **Compute-VM (cores-vCPUs) subscription limit increases** (Aumento del límite de suscripción para VM de proceso [núcleos/vCPU]).

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Captura de pantalla que muestra una solicitud de soporte técnico para aumentar una cuota de vCPU de la familia de VM en Azure Portal.":::

Desde allí, siga los pasos descritos anteriormente para completar la solicitud de aumento de cuota regional.

## <a name="next-steps"></a>Pasos siguientes

- Revise la [lista de regiones de Azure y sus ubicaciones](https://azure.microsoft.com/regions/).
- Obtenga información general sobre las [regiones de Azure para máquinas virtuales](../../virtual-machines/regions.md) y cómo maximizar el rendimiento, la disponibilidad y la redundancia de una VM en una región determinada.
