---
title: 'Configuración del servicio: QnA Maker'
description: Comprenda cómo y dónde configurar los recursos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f504e1de8c751cb7d97679c8d56c591b0508c808
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071123"
---
# <a name="service-configuration"></a>Configuración del servicio

Cada versión de QnA Maker usa un conjunto diferente de recursos de Azure (servicios). En este artículo se describen las personalizaciones admitidas para estos servicios. 

## <a name="app-service"></a>App Service

QnA Maker usa una instancia de App Service para proporcionar el tiempo de ejecución de la consulta que usa [generateAnswer API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer).

Esta configuración está disponible en Azure Portal para la instancia de App Service. La configuración está disponible seleccionando **Configuración** y **Configuración**.

Puede establecer una configuración individual a través de la lista de configuración de la aplicación o modificar varias opciones de configuración seleccionando **Edición avanzada**.

|Resource|Configuración|
|--|--|
|AzureSearchAdminKey|Cognitive Search: se usa para el almacenamiento de pares de QnA y clasificador n.º 1|
|AzureSearchName|Cognitive Search: se usa para el almacenamiento de pares de QnA y clasificador n.º 1|
|DefaultAnswer|Texto de respuesta cuando no se encuentra ninguna coincidencia|
|UserAppInsightsAppId|Registro de chat y telemetría|
|UserAppInsightsKey|Registro de chat y telemetría|
|UserAppInsightsName|Registro de chat y telemetría|
|QNAMAKER_EXTENSION_VERSION|Siempre se establece en _latest_. Este valor inicializará la extensión de sitio QnA Maker en App Service.|

Debe **reiniciar** el servicio en la página **Información general** de Azure Portal, una vez que haya terminado de realizar los cambios.

## <a name="qna-maker-service"></a>Servicio QnA Maker

El servicio QnA Maker proporciona la configuración para que los siguientes usuarios colaboren en un único servicio QnA Maker y en todas sus bases de conocimiento.

Aprenda a [agregar colaboradores](./index.yml) al servicio.

## <a name="change-azure-cognitive-search"></a>Cambio de Azure Cognitive Search

Aprenda [a cambiar Azure Cognitive Search](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) vinculado a un servicio QnA Maker.

## <a name="change-default-answer"></a>Cambio de la respuesta predeterminada

Aprenda [a cambiar el texto de las respuestas predeterminadas](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Telemetría

Application Insights se usa para supervisar la telemetría con QnA Maker GA. No hay valores de configuración específicos de QnA Maker.

## <a name="app-service-plan"></a>Plan de servicio de aplicación

El plan de App Service no tiene valores de configuración específicos de QnA Maker.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los [formatos](reference-document-format-guidelines.md) para los documentos y las direcciones URL que desea importar en una base de conocimiento.
