---
title: Introducción a las API de envío de marketplace comercial en el Centro de partners
description: Obtenga información general sobre las API de envío de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 246f57b09e075baaa17296e0c81c2f756e0f42ac
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129056600"
---
# <a name="commercial-marketplace-submission-api-overview"></a>Introducción a las API de envío de marketplace comercial

Use API para consultar, crear envíos y publicar ofertas mediante programación. La API es útil si su cuenta administra muchas ofertas y desea automatizar y optimizar el proceso de envío de estas ofertas.

## <a name="types-of-apis"></a>Tipos de API

Hay dos conjuntos de API de envío disponibles:

- **API de envío del Centro de partners**: el conjunto común de API que funcionan en productos comerciales y de consumidor para publicar a través del Centro de partners. A este conjunto de API se les agregan nuevas funcionalidades continuamente. Para más información sobre cómo realizar la integración con esta API, consulte [Incorporación de API de envío del Centro de partners](submission-api-onboard.md). 
- **API de Cloud Partner Portal heredada**: las API que se han realizado desde Cloud Partner Portal; se integra con el Centro de partners y sigue funcionando en este. Este conjunto de API solo está en modo de mantenimiento; es posible que las nuevas funcionalidades introducidas en Centro de partners no sean compatibles y solo se debe usar para los productos existentes que ya se integraron antes de la transición al Centro de partners. Para más información sobre cómo seguir usando las API de Cloud Partner Portal, consulte [Referencia de las API de Cloud Partner Portal](cloud-partner-portal-api-overview.md).

Consulte en la tabla siguiente las API de envío que se admiten para cada tipo de oferta.

| Tipo de oferta | Compatibilidad con la API heredada de Cloud Partner Portal | Compatibilidad con la API de envío del Centro de partners |
| --- | :---: | :---: |
| Aplicación de Azure |  | &#x2714; |
| Contenedor de Azure | &#x2714; |  |
| Máquina virtual de Azure | &#x2714; |  |
| Servicio de consultoría | &#x2714; |  |
| Dynamics 365 |  | &#x2714; |
| Módulo IoT Edge | &#x2714; |  |
| Servicio administrado | &#x2714; |  |
| Aplicación Power BI | &#x2714; |  |
| Software como servicio |  | &#x2714; |
|

Los complementos de Microsoft 365 Office, las soluciones de Microsoft 365 SharePoint, las aplicaciones de Microsoft 365 Teams y los objetos visuales de Power BI no admiten las API de envío.

## <a name="next-steps"></a>Pasos siguientes

- Visite el vínculo de la API adecuado para el tipo de oferta según sea necesario.
