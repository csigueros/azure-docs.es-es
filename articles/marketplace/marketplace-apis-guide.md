---
title: Alinee su negocio con nuestra plataforma de comercio electrónico y Azure Marketplace
description: Alinee su negocio con nuestra plataforma de comercio electrónico (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: MarlEva
ms.author: maevan
ms.date: 05/13/2021
ms.openlocfilehash: 118f78c6e7b06ddf77533266581de382afcfe47a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984255"
---
# <a name="align-your-business-with-our-e-commerce-platform"></a>Alinee su negocio con nuestra plataforma de comercio electrónico

En este artículo se describe cómo se combinan la interfaz de usuario (UI) y las interfaces de programación de aplicaciones (API) del marketplace comercial para respaldar los procesos de negocio. Los vínculos debajo de la API apuntan a las interfaces específicas que los desarrolladores pueden usar para integrar su sistema CRM con el marketplace comercial.

## <a name="overview-of-activities"></a>Descripción de las actividades

Las actividades siguientes no son secuenciales. La actividad que use depende de sus necesidades empresariales y procesos de ventas. En esta guía se muestra cómo integrar diferentes API para automatizar cada actividad.

| <center>Actividad | Actividades de ventas de ISV | API de Marketplace correspondiente | Interfaz de usuario de Marketplace correspondiente |
| --- | --- | --- | --- |
| <center>**1. Marketing de productos**<br><img src="media/api-guide/icon-product-marketing.png" alt="The icon for product marketing"> | Creación de mensajería, posicionamiento, promoción o precios de productos | Creación de mensajería, posicionamiento, promoción o precios de productos<br>[API Partner Ingestion](https://apidocs.microsoft.com/services/partneringestion/)<br>[API Azure Apps Onboarding](azure-app-apis.md)</ul> | Creación de mensajería, posicionamiento, promoción o precios de productos<br>Centro de partners (PC) → Creación de ofertas |
| <center>**2. Generación de demanda**<br><img src="media/api-guide/icon-demand-generation.png" alt="The icon for demand generation"> | Promoción de productos<br>Generación de clientes potenciales<br>Evaluación, prueba y PoC | Promoción de productos<br>Generación de clientes potenciales<br>Evaluación, prueba y PoC<br>[Conector CRM de clientes potenciales para D365, SFDC y Marketo](partner-center-portal/commercial-marketplace-get-customer-leads.md)<br>[Conector de Co-Sell para CRM de SalesForce](/partner-center/connector-salesforce)<br>[Conector de Co-Sell para CRM de Dynamics 365](/partner-center/connector-dynamics) | Promoción de productos<br>Generación de clientes potenciales<br>Evaluación, prueba y PoC<br>Azure Marketplace y AppSource<br>Información de Marketplace<br>Oportunidades de Co-Sell del Centro de partners |
| <center>**3. Negociación y creación de ofertas**<br><img src="media/api-guide/icon-negotiation-quote-creation.png" alt="The icon for negotiation and quote creation"> | T&C<br>Precios<br>Aprobaciones de descuentos<br>Oferta final | T&C<br>Precios<br>Aprobaciones de descuentos<br>Oferta final<br>[API CPP para máquinas virtuales](cloud-partner-portal-api-overview.md)<br>[API Microsoft Graph para AAD](../active-directory/reports-monitoring/concept-reporting-api.md)<br>[Familia de API del Centro de partners "7"](https://apidocs.microsoft.com/services/partnercenter) | T&C<br>Precios<br>Aprobaciones de descuentos<br>Oferta final<br>Centro de partners → Planes (públicos o privados) |
| <center>**4. Ventas**<br><img src="media/api-guide/icon-sale.png" alt="The icon for sale"> | Firma de contrato<br>Reconocimiento de ingresos<br>Facturación<br>Facturación | Firma de contrato<br>Reconocimiento de ingresos<br>Facturación<br>Facturación<br>[API de cumplimiento de SaaS v.2](partner-center-portal/pc-saas-fulfillment-api-v2.md)<br>[API de informes](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf) | Firma de contrato<br>Reconocimiento de ingresos<br>Facturación<br>Facturación<br>Azure Portal/Centro de administración<br>Recompensas del marketplace del Centro de partners<br>Informes de pagos del Centro de partners<br>Análisis de Marketplace del Centro de partners<br>Cierre de Co-Sell del Centro de partners |
| <center>**5. Mantenimiento**<br><img src="media/api-guide/icon-maintenance.png" alt="The icon for maintenance"> | Facturación periódica<br>Uso por encima del límite<br>Servicios de soporte técnico | Facturación periódica<br>Uso por encima del límite<br>Servicios de soporte técnico<br>[SaaS/AMA: API de facturación](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf)<br>[API de cumplimiento de SaaS v.2](partner-center-portal/pc-saas-fulfillment-api-v2.md)<br>[API Partner Payouts](https://apidocs.microsoft.com/services/partnerpayouts) <br>[github](https://github.com/microsoft/Partner-Center-Payout-APIs)<br>[API de facturación según uso](marketplace-metering-service-apis.md)<br>[(Cliente de EA) API Azure Consumption](/rest/api/consumption/)<br>[(Cliente de EA) API Azure Charges List](/rest/api/consumption/charges/list) | Facturación periódica<br>Uso por encima del límite<br>Servicios de soporte técnico<br>Informes de pagos del Centro de partners<br>Análisis de Marketplace del Centro de partners |
| <center>**6. Fin de contrato**<br><img src="media/api-guide/icon-contract-end.png" alt="The icon for contract end"> | Renovar o<br>Terminate |Renovar o<br>Terminate <br>[API de cumplimiento de SaaS v.2](partner-center-portal/pc-saas-fulfillment-api-v2.md)<br>AMA/máquinas virtuales: renovación automática | Renovar o<br>Terminate<br>Análisis de Marketplace del Centro de partners |
|

## <a name="next-steps"></a>Pasos siguientes

- Consulte los vínculos anteriores correspondientes a cada API según sea necesario.