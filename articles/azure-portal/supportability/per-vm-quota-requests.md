---
title: Aumento de las cuotas de vCPU de la familia de máquinas virtuales
description: Aprenda a solicitar un aumento del límite de cuota de vCPU de una familia de VM en Azure Portal, que aumenta el límite de vCPU regional total en la misma cantidad.
ms.date: 11/15/2021
ms.topic: how-to
ms.openlocfilehash: 7941c19de7143bc794ffd6d197d31c6db6f0a0d5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714391"
---
# <a name="increase-vm-family-vcpu-quotas"></a>Aumento de las cuotas de vCPU de la familia de máquinas virtuales

Azure Resource Manager admite dos tipos de cuotas de vCPU para las máquinas virtuales:

- cuotas de vCPU estándar
- cuotas de vCPU de acceso puntual

Las cuotas de vCPU estándar se aplican a las VM de pago por uso y a las instancias de VM reservadas. Se aplican en dos niveles para cada suscripción, en cada región:

- El primer nivel es la cuota total de vCPU regional.
- El segundo nivel es la cuota de vCPU de la familia de VM, como las vCPU de la serie D.

En este artículo se muestra cómo solicitar aumentos para las cuotas de vCPU de la familia de VM. También puede solicitar aumentos para las [cuotas de vCPU por región](regional-quota-requests.md) o las [cuotas de vCPU de acceso puntual](spot-quota.md).

## <a name="increase-a-vm-family-vcpu-quota"></a>Aumento de las cuotas de vCPU de la familia de VM

Para solicitar un aumento de la cuota de vCPU estándar por familia de VM de **Uso y cuotas**:

1. En Azure Portal, seleccione **Suscripciones**.
1. Seleccione la suscripción cuya cuota quiere aumentar.
1. En el panel izquierdo, seleccione **Uso y cuotas**.
1. En el panel principal, busque la cuota de vCPU de la familia de VM que quiera aumentar y, a continuación, seleccione el icono del lápiz. En el ejemplo siguiente se muestran las vCPU de la familia DSv3 estándar implementadas en la región Este de EE. UU. La columna **Uso** muestra el uso y el límite de cuota actual.
1. En **Detalles de la cuota**, escriba el nuevo límite de cuota y, a continuación, seleccione **Guardar y continuar**.

   :::image type="content" source="media/resource-manager-core-quotas-request/quota-increase-example.png" alt-text="Captura de pantalla del panel Uso y cuotas." lightbox="media/resource-manager-core-quotas-request/quota-increase-example.png":::

Se revisará la solicitud y se le notificará si esta se ha aprobado o rechazado. Esto suele ocurrir en cuestión de minutos. Si se rechaza la solicitud, verá un vínculo donde puede abrir una solicitud de soporte técnico para que un ingeniero pueda ayudarle con el aumento.

> [!NOTE]
> Si se aprueba la solicitud para aumentar la cuota de la familia de VM, Azure aumentará automáticamente la cuota de vCPU regional para la región donde se implementa la VM.

> [!TIP]
> Al crear o cambiar el tamaño de una máquina virtual y seleccionar el tamaño de esta, es posible que vea algunas opciones enumeradas en **Cuota insuficiente: límite de familia**. Si es así, puede solicitar un aumento de cuota directamente desde la página de creación de la VM seleccionando el vínculo **Solicitar cuota**.

## <a name="increase-a-vm-family-vcpu-quota-from-help--support"></a>Aumento de una cuota de vCPU de la familia de VM desde Ayuda y soporte técnico

Para solicitar un aumento de la cuota de vCPU estándar por familia de VM desde **Ayuda y soporte técnico**, cree una nueva solicitud de soporte técnico en Azure Portal.

1. En **Tipo de problema**, seleccione **Límites de servicio y suscripción (cuotas)** .
1. En **Suscripción**, seleccione la suscripción cuya cuota quiere aumentar.
1. Seleccione **Tipo de cuota** y, luego, **Compute-VM (cores-vCPUs) subscription limit increases** (Aumento del límite de suscripción para VM de proceso [núcleos/vCPU]).

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Captura de pantalla que muestra una solicitud de soporte técnico para aumentar una cuota de vCPU de la familia de VM en Azure Portal.":::

Desde allí, siga los pasos descritos anteriormente para completar la solicitud de aumento de cuota.

## <a name="increase-multiple-vm-family-cpu-quotas-in-one-request"></a>Aumento de varias cuotas de CPU de la familia de VM en una solicitud

También puede solicitar varios aumentos al mismo tiempo (solicitud masiva). Tenga en cuenta que realizar un aumento de la cuota de solicitudes masivas puede tardar más que solicitar aumentar una sola cuota.

Para solicitar varios aumentos juntos, vaya primero a la página **Uso y cuotas**, tal como se ha descrito anteriormente. A continuación, haga lo siguiente:

1. Seleccione la pestaña **Solicitar aumento** situada junto a la parte superior de la pantalla.
1. Seleccione **Tipo de cuota** y, luego, **Compute-VM (cores-vCPUs) subscription limit increases** (Aumento del límite de suscripción para VM de proceso [núcleos/vCPU]).
1. Seleccione **Siguiente** para ir a la pantalla **Detalles adicionales** y, a continuación, seleccione **Escribir detalles**.
1. En la pantalla **Detalles de la cuota**:

   :::image type="content" source="media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png" alt-text="Captura de pantalla que muestra la pantalla detalles de la cuota y las selecciones.":::

   1. En el **Modelo de implementación**, asegúrese de que el **Administrador de recursos** esté seleccionado.
   1. En **Ubicaciones**, seleccione todas las regiones en las que quiera aumentar las cuotas.
   1. Para cada región seleccionada, seleccione una o varias series de VM en la lista desplegable **Cuotas**.
   1. Para cada **serie de VM** que haya seleccionado, escriba el nuevo límite de vCPU que desee para esta suscripción.
   1. Cuando haya terminado, seleccione **Guardar y continuar**.
1. Escriba o confirme los detalles de contacto y, a continuación, seleccione **Siguiente**.
1. Por último, asegúrese de que todo esté correcto en la página **Revisar y crear** y, a continuación, seleccione **Crear** para enviar la solicitud.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de las [cuotas de vCPU](/azure/virtual-machines/windows/quotas).
- Conozca los [límites, las cuotas y las restricciones de suscripción y servicios de Azure](/azure/azure-resource-manager/management/azure-subscription-service-limits).
