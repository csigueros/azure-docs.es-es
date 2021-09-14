---
title: Cambio de la oferta de suscripción de Azure
description: Aprenda a cambiar su suscripción de Azure y a pasar a una oferta diferente.
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/27/2021
ms.author: banders
ms.openlocfilehash: 7388ee42195d93a9b7d9a64567f25be91b0eb38d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111960"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Cambio de la suscripción de Azure a una oferta distinta

Como cliente con una [suscripción de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) puede cambiar la suscripción de Azure a otra oferta en Azure Portal. Por ejemplo, esta característica se puede utilizar para aprovechar los [créditos mensuales de que disfrutan los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Si tiene una suscripción a Visual Studio expirada, puede cambiar a una [suscripción de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/).

**¿Solo desea actualizar desde la evaluación gratuita?** Consulte [Actualización de la suscripción](upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Lo que se admite

Puede cambiar de una suscripción de pago por uso a:

- [Desarrollo/pruebas - Pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plataformas de MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

También puede cambiar de una [suscripción de Visual Studio Enterprise expirada](https://azure.microsoft.com/offers/ms-azr-0063p/) a una [suscripción de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). 

> [!NOTE]
> Para cambiar a otra oferta, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="switch-from-a-pay-as-you-go-subscription"></a>Cambio desde una suscripción de pago por uso

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Vaya a **Suscripciones** y, después, seleccione su suscripción de pago por uso.
1. En la parte superior de la página, seleccione **Cambiar oferta**. La opción solo está disponible si tiene una suscripción de pago por uso y ya completó el primer período de facturación.  
    :::image type="content" source="./media/switch-azure-offer/switch-offer.png" alt-text="Captura de pantalla que muestra los detalles de la suscripción con la opción Cambiar oferta." lightbox="./media/switch-azure-offer/switch-offer.png" :::
1. Seleccione la oferta que desee en la lista de suscripciones a las que puede cambiar. Dicha lista varía en función de los grupos a los que la cuenta está asociada. Si no hay ninguna disponible, compruebe la [lista de ofertas disponibles a las que puede cambiar](#whats-supported) y asegúrese de que pertenece a los grupos apropiados. A continuación, seleccione **Siguiente**.
    :::image type="content" source="./media/switch-azure-offer/select-offer.png" alt-text="Captura de pantalla que muestra dónde seleccionar una oferta a la que desea cambiar." lightbox="./media/switch-azure-offer/select-offer.png" :::
    Según la oferta a la que cambie, es posible que vea una nota sobre la consecuencia del cambio. Antes de continuar, recorra la lista detenidamente y siga las instrucciones. Es posible que también tenga que comprobar el número de teléfono.
1. Después de revisar las notas o comprobar el número de teléfono, seleccione **Cambiar oferta**.
1. Ya se ha cambiado la suscripción a la nueva oferta.

## <a name="convert-a-visual-studio-enterprise-subscription"></a>Conversión de una suscripción de Visual Studio Enterprise

Cuando expira la suscripción a Visual Studio Enterprise suscripción, se deshabilita. Para seguir usando los servicios de Azure con la suscripción, debe convertirla en una suscripción de pago por uso.

>[!NOTE]
> - Al actualizar a una suscripción de pago por uso, deja de recibir créditos mensuales y el método de pago comienza a cobrarse mensualmente en función del uso en ese período.
> - Al cambiar a una suscripción de pago por uso, pierde la información de uso y facturación. Se recomienda guardar las [facturas y los datos de uso anteriores](download-azure-invoice-daily-usage-date.md) antes de cambiar la oferta.

Para convertir la suscripción:

1. Vaya a **Suscripciones** y seleccione su suscripción a Visual Studio Enterprise.
1. Verá un mensaje emergente en la parte superior de la página que dice `Your Visual Studio subscription is expired and has been disabled. To continue using this subscription, please click here to convert to a pay-as-you-go subscription`. Seleccione el banner.  
    :::image type="content" source="./media/switch-azure-offer/visual-studio-expired-banner.png" alt-text="Captura de pantalla que muestra una suscripción expirada con el mensaje emergente para convertirla." lightbox="./media/switch-azure-offer/visual-studio-expired-banner.png" :::
1. En la página Cambiar oferta, seleccione una pestaña de oferta, seleccione la opción de pago por uso y, luego, elija **Siguiente**.  
    :::image type="content" source="./media/switch-azure-offer/select-offer-pay-as-you-go.png" alt-text="Captura de pantalla que muestra la opción Cambiar oferta." lightbox="./media/switch-azure-offer/select-offer-pay-as-you-go.png" :::
1. En la pestaña Seleccionar método de pago, elija **Seleccionar un método de pago**, escriba la información del método de pago y, luego, seleccione **Siguiente**.  
1. En la página Revisar y convertir, tiene la opción de cambiar el nombre de la suscripción. Revise la información del contrato, seleccione **Acepto** y, luego, en la parte inferior de la página, elija **Cambiar oferta**.  
    :::image type="content" source="./media/switch-azure-offer/switch-offer-visual-studio-agreement.png" alt-text="Captura de pantalla que muestra la pestaña Revisar y convertir con el nombre de la suscripción y la aceptación del contrato." lightbox="./media/switch-azure-offer/switch-offer-visual-studio-agreement.png" :::

Para ver la nueva suscripción convertida, vaya a **Suscripciones**.


## <a name="frequently-asked-questions-for-pay-as-you-go-subscriptions"></a>Preguntas frecuentes sobre suscripciones de pago por uso

En las secciones siguientes se responde a las preguntas frecuentes sobre cómo cambiar desde una suscripción de pago por uso.

### <a name="what-is-an-azure-offer"></a>¿Qué es una oferta de Azure?

Una oferta de Azure es el *tipo* de la suscripción a Azure que tiene. Algunos ejemplos de ofertas de Azure son una suscripción con [tarifas de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure bajo licencia Open](https://azure.microsoft.com/offers/ms-azr-0111p/) y [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/). Cada oferta tiene distintos [términos](https://azure.microsoft.com/support/legal/offer-details/) y algunos tienen ventajas especiales. La oferta de la suscripción se muestra en la página de detalles de la suscripción.

:::image type="content" source="./media/switch-azure-offer/subscription-details.png" alt-text="Página de detalles de la suscripción que muestra el tipo de oferta" lightbox="./media/switch-azure-offer/subscription-details.png" :::

### <a name="why-dont-i-see-the-switch-offer-option"></a>¿Por qué no veo la opción Cambiar oferta?

Puede que no vea la opción **Cambiar oferta** en los siguientes casos:

* No tiene una [suscripción con tarifas de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Actualmente, solo las suscripciones con tarifas de pago por uso se pueden convertir en otra oferta.
  * Si tiene una [evaluación gratuita](https://azure.microsoft.com/free/), aprenda a [actualizar a la modalidad de pago por uso](upgrade-azure-subscription.md).
  * Para cambiar de oferta desde otra suscripción [póngase en contacto con el equipo de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Aún está en el primer período de facturación; para poder cambiar de oferta debe esperar a que este finalice.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>¿Por qué no se ve el mensaje "No hay ninguna oferta disponible en su región o país en este momento"?

* Podría no reunir los requisitos para cambiar de oferta. Compruebe la [lista de ofertas disponibles a las que puede cambiar](#whats-supported) y asegúrese de que ha activado las ventajas adecuadas con Visual Studio o Bizspark.
* Es posible que algunas ofertas no estén disponibles en todos los países y regiones.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>¿Cómo afecta el cambio de ofertas de Azure ofrece a mi servicio y facturación?

Estos son los detalles de lo que ocurre cuando se cambian las ofertas de Azure.

#### <a name="no-service-downtime"></a>No hay ningún tiempo de inactividad del servicio

No hay tiempo de inactividad del servicio para los usuarios asociados a la suscripción. Pero es posible que la oferta a la que cambie tenga restricciones. Por ejemplo, algunas ofertas no permiten el uso de producción, por lo que tendría que mover los recursos de producción a otra suscripción.

#### <a name="quota-increases-are-reset"></a>Los aumentos de cuota se restablecen

Cuando cambia las ofertas, se restablece cualquier [aumento de límite o cuota por sobre el límite predeterminado](../../azure-portal/supportability/resource-manager-core-quotas-request.md). No hay ningún tiempo de inactividad del servicio, incluso si tiene más recursos más allá del límite predeterminado. Por ejemplo, si usa 200 núcleos en la suscripción y luego cambia las ofertas, la cuota de núcleos se restablece al valor predeterminado de 20 núcleos. Las máquinas virtuales que usan los 200 núcleos no se ven afectadas y podrían seguir en ejecución. Sin embargo, si no hace otra solicitud de aumento de cuota, no podrá aprovisionar más núcleos.

#### <a name="billing"></a>Facturación

El mismo día que se realiza el cambio se genera una factura por todos los cargos pendientes. Posteriormente, la suscripción se factura según los términos de precios de la nueva oferta. La fecha de facturación de la suscripción pasa a ser la fecha del cambio de oferta. Los datos de uso y facturación anteriores al cambio de oferta no se conservan, por lo que se recomienda descargar una copia de los mismos antes de realizar el cambio.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>¿Puedo migrar desde una suscripción con tarifas de pago por uso a Proveedor de soluciones en la nube (CSP) o Contrato Enterprise (EA)?

* Para migrar a CSP, consulte [Transferencia de suscripciones de Azure entre suscriptores y CSP](transfer-subscriptions-subscribers-csp.md).
* Si tiene una suscripción de pago por uso (id. de oferta de Azure MS-AZR-0003P) o un plan de Azure con tarifas de pago por uso (id. de oferta de Azure MS-AZR-0017G) y quiere migrar a una inscripción de EA, indique al administrador de inscripción que agregue su cuenta al EA. Siga las instrucciones que aparecen en el correo electrónico de invitación para mover las suscripciones bajo la inscripción EA. Para más información, consulte [Cambio de la propiedad de la cuenta o de la suscripción de Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>¿Puedo migrar datos y servicios a nueva una suscripción?

* Puede migrar los recursos directamente a la nueva suscripción; consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Para transferir la propiedad de una suscripción a Azure y todo lo que implica a otra persona, consulte [Transferring ownership of an Azure subscription](billing-subscription-transfer.md) (Transferencia de la propiedad de una suscripción a Azure).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

- [Inicio del análisis de costos](../costs/quick-acm-cost-analysis.md)
