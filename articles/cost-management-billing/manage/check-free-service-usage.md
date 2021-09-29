---
title: Supervisión y seguimiento del uso de los servicios gratuitos de Azure
description: Aprenda a comprobar el uso de los servicios gratuitos en Azure Portal. No se aplica ningún cargo por los servicios incluidos en una cuenta gratuita, salvo que se superen los límites de servicio.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: c6a316d11f4d6d43561daab97e2b7b8d4f197a77
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610434"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Comprobación del uso de los servicios gratuitos incluidos con la cuenta gratuita de Azure

No se le cobrarán los servicios que se incluyen de forma gratuita en la cuenta gratuita de Azure, a menos que supere los límites de los mismos. Para no superarlos, puede usar Azure Portal para realizar el seguimiento del uso de los servicios gratuitos.

## <a name="check-usage-in-the-azure-portal"></a>Comprobación del uso en Azure Portal

1.  Inicie sesión en [Azure Portal](https://portal.azure.com).
1.  Busque **Suscripciones**.  
    ![Captura de pantalla que muestra la búsqueda de suscripciones en el portal](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  Seleccione la suscripción que se creó cuando se registró para obtener la cuenta gratuita de Azure.
1.  Desplácese hacia abajo para encontrar la tabla que muestra el uso de los servicios gratuitos.  
    ![Captura de pantalla que muestra el uso de los servicios gratuitos](./media/check-free-service-usage/subscription-usage-free-services.png)

La tabla tiene las columnas siguientes.

* **Medidor:** identifica la unidad de medida del recurso que se consume.
* **Uso y límite:** uso y límite del mes actual para el medidor.
* **Estado:** estado de uso del servicio. En función del uso, puede tener uno de los siguientes estados:
  * **No está en uso:** no ha usado el medidor o el uso de este no llega al sistema de facturación.
  * **Lo ha superado el \<Date>:** ha superado el límite del medidor el \<Date>.
  * **Superación improbable:** es poco probable que supere el límite del medidor.
  * **Lo superará el \<Date>:** Es probable que supere el límite del medidor el \<Date>.

> [!IMPORTANT]
>
> Los servicios gratuitos solo están disponibles para la suscripción que se creó cuando se registró para obtener una cuenta gratuita de Azure. Si no puede ver la tabla de servicios gratuitos en la página de información general de la suscripción, no estarán disponibles para la suscripción.

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes
- [Actualización de una cuenta gratuita de Azure](upgrade-azure-subscription.md)
