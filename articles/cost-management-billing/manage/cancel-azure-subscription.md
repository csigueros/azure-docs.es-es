---
title: Cancelación de su suscripción de Azure
description: Describe cómo cancelar la suscripción de Azure, como la suscripción a la evaluación gratuita
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: banders
ms.openlocfilehash: 71070f56eaa8b9e1d0a4bef42d609b10ac28b7e5
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321637"
---
# <a name="cancel-your-azure-subscription"></a>Cancelación de su suscripción de Azure

Puede cancelar la suscripción de Azure en Azure Portal si ya no la necesita.

Aunque no es obligatorio, Microsoft *recomienda* realizar las siguientes acciones antes de cancelar una suscripción:

* Realice una copia de seguridad de los datos. Por ejemplo, si está almacenando datos en Azure Storage o SQL, descargue una copia. Si tiene una máquina virtual, guarde una imagen de la misma localmente.
* Cierre los servicios. Vaya a la [página de recursos en el portal de administración](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) y **detenga** todas las máquinas virtuales, aplicaciones u otros servicios en ejecución.
* Considere la posibilidad de migrar los datos. Consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Elimine todos los recursos y todos los grupos de recursos.
* Si tiene algún rol personalizado que haga referencia a esta suscripción en `AssignableScopes`, debe actualizar este rol personalizado para quitar la suscripción. Si intenta actualizar un rol personalizado después de cancelar una suscripción, es posible que obtenga un error. Para más información, consulte [Solución de problemas con los roles personalizados](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) y [Roles personalizados de Azure](../../role-based-access-control/custom-roles.md).

Si cancela un plan de soporte técnico de Azure de pago, se le facturará por el resto del período de suscripción. Para más información, vea [Soporte técnico de Azure](https://azure.microsoft.com/support/plans/).

## <a name="who-can-cancel-a-subscription"></a>¿Quién puede cancelar una suscripción?

En la tabla siguiente se describe el permiso necesario para cancelar una suscripción.

|Tipo de suscripción     |Quién puede cancelar  |
|---------|---------|
|Suscripciones creadas al registrarse en Azure a través del sitio web de Azure. Por ejemplo, cuando se registra para obtener una [cuenta gratuita de Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), como una [cuenta con tarifas de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) o como un [suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrador del servicio y propietario de la suscripción  |
|[Contrato Enterprise de Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/) y [Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Administrador del servicio y propietario de la suscripción       |
|[Plan de Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) y [Azure Plan for DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Propietarios de la suscripción      |

El administrador de cuenta sin el rol de administrador de servicios o propietario de la suscripción no puede cancelar una suscripción de Azure. Sin embargo, el administrador de cuenta puede hacerse el administrador de servicios y, a continuación, puede cancelar una suscripción. Para más información, consulte [Cambio del administrador de servicios](../../role-based-access-control/classic-administrators.md#change-the-service-administrator).


## <a name="cancel-subscription-in-the-azure-portal"></a>Cancelación de la suscripción en Azure Portal

1. Seleccione su suscripción en la [página Suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción que quiere cancelar.
1. Seleccione **Introducción** y, luego, seleccione **Cancelar suscripción**.
    ![Captura de pantalla que muestra el botón Cancelar](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Siga las indicaciones y finalice la cancelación.

> [!NOTE]
> Los asociados pueden suspender o cancelar una suscripción si lo solicita un cliente o en caso de impago o fraude. Para más información, consulte el apartado [Suspender o cancelar una suscripción](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="cancel-a-support-plan"></a>Cancelación de un plan de soporte técnico

Si adquirió un plan de soporte técnico a través del sitio web de Azure o Azure Portal, o bien si lo tiene en un Contrato de cliente de Microsoft, puede cancelarlo. Si compró el plan de soporte técnico a través de un asociado o representante de Microsoft, póngase en contacto con él para obtener ayuda. 

1. En Azure Portal, vaya a **Cost Management + Billing**.
1. En **Facturación**, seleccione **Cargos periódicos**.
1. En el lado derecho del elemento de línea del plan de soporte técnico, seleccione el los puntos suspensivos ( **...** ) y, después, **Desactivación de la renovación automática**.

## <a name="what-happens-after-subscription-cancellation"></a>¿Qué ocurre después de la cancelación de una suscripción?

Una vez que se cancela, la facturación se detiene inmediatamente. Sin embargo, pueden transcurrir hasta 10 minutos hasta que la cancelación se muestre en el portal. Si cancela en medio de un período de facturación, enviamos la factura final en la fecha de factura típica una vez finalizado el período.

Después de cancelar, los servicios se deshabilitan. Esto significa que las máquinas virtuales se desasignan, las direcciones IP temporales se liberan y el almacenamiento es de solo lectura.

Tras la cancelación de la suscripción, Microsoft espera entre 30 y 90 días para eliminar de forma permanente los datos, por si el usuario necesitará acceder a ellos o cambiara de opinión. No se le cobrará nada por conservar los datos. Para más información, consulte el [Microsoft Trust Center: How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Centro de confianza de Microsoft: Administración de los datos).

## <a name="delete-free-account-or-pay-as-you-go-subscription"></a>Eliminación de una cuenta gratuita o una suscripción de pago por uso

Si tiene una cuenta gratuita o una suscripción de pago por uso, no tendrá que esperar de 30 a 90 días para que la suscripción se elimine de forma automática. La opción **Eliminar** estará disponible 3 días después de cancelar una suscripción. Después de 3 días, si no tiene ningún recurso en la suscripción cancelada, puede eliminarla.

Siga estos pasos para eliminar una suscripción:

1. Espere 3 días después de la fecha de cancelación de la suscripción.
2. Asegúrese de que no tiene ningún recurso en la suscripción antes de intentar eliminarla.
3. En Azure Portal, vaya a [Suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
4. Seleccione la suscripción que quiere eliminar.
5. Seleccione **Información general** y, luego, **Eliminar**.
6. Escriba el nombre de la suscripción en el mensaje de confirmación y, después, seleccione **Eliminar suscripción**.

## <a name="delete-other-subscriptions"></a>Eliminación de las demás suscripciones

Los únicos tipos de suscripción que puede eliminar de forma manual son una cuenta gratuita o una suscripción de pago por uso. Todos los demás tipos de suscripción solo se eliminan a por medio del proceso de [cancelación de la suscripción](#cancel-subscription-in-the-azure-portal). Es decir, no puede eliminar una suscripción directamente a menos que sea una cuenta gratuita o una suscripción de pago por uso. Sin embargo, después de cancelar una suscripción, puede crear una [solicitud de soporte técnico de Azure](https://go.microsoft.com/fwlink/?linkid=2083458) para solicitar que la suscripción se elimine inmediatamente.

## <a name="reactivate-a-subscription"></a>Reactivación de una suscripción

Si cancela su suscripción de pago por uso accidentalmente, puede [volver a activarla en Azure Portal](subscription-disabled.md).

Si su suscripción no es de pago por uso, para reactivar la suscripción, póngase en contacto con el soporte técnico en los 90 días posteriores a la cancelación.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>¿Por qué no veo la opción Cancelar suscripción en Azure Portal? 

Es posible que no tenga los permisos necesarios para cancelar una suscripción. Consulte [¿Quién puede cancelar una suscripción?](#who-can-cancel-a-subscription) para ver quién puede cancelar distintos tipos de suscripciones.

## <a name="how-do-i-delete-my-azure-account"></a>¿Cómo elimino mi cuenta de Azure?

*Necesito eliminar mi cuenta, incluida toda mi información personal. Ya he cancelado mis suscripciones activas (evaluación gratuita). No tengo ninguna suscripción activa y me gustaría eliminar definitivamente mi cuenta*.

* Si dispone de una cuenta de Azure Active Directory a través de su organización, el administrador de Azure AD puede eliminar la cuenta. Después, los servicios se deshabilitan. Esto significa que las máquinas virtuales se desasignan, las direcciones IP temporales se liberan y el almacenamiento es de solo lectura. En resumen, una vez que cancele, la facturación se interrumpe inmediatamente.

* Si no dispone de una cuenta de Azure AD a través de su organización, puede cancelar y eliminar las suscripciones de Azure, y después eliminar la tarjeta de crédito de la cuenta. Aunque esta acción no elimina la cuenta, la deja inutilizable. Además, puede eliminar también la cuenta de Microsoft asociada si no se usa con ningún otro fin.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>¿Cómo cancelo una cuenta de Visual Studio Professional?

Consulte el artículo [Renovación y cancelación](/visualstudio/subscriptions/faq/admin/renewal-cancellation). Si tiene suscripciones de Azure en Visual Studio, deberán cancelarse y eliminarse también.

## <a name="next-steps"></a>Pasos siguientes

- Si es necesario, las suscripciones de pago por uso se pueden reactivar en [Azure Portal](subscription-disabled.md).
