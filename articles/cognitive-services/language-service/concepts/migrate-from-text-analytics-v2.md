---
title: Guía de migración para la versión 2 de la API Text Analytics
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo migrar sus aplicaciones para que usen la versión 3 de la API Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/06/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 396cac23548eb951e47c4ec3a602aadcdfa304f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090829"
---
# <a name="migrate-from-version-2-of-the-text-analytics-api"></a>Migración desde la versión 2 de la API Text Analytics

Si las aplicaciones usan la versión 2.1 de la API Text Analytics, este artículo le ayudará a actualizar las aplicaciones para usar la versión más reciente de las características, que ahora forman parte de [Azure Cognitive Service for Language](../overview.md).

## <a name="features"></a>Características

Seleccione una de las características siguientes para ver la información que puede usar para actualizar la aplicación.

## <a name="sentiment-analysis"></a>[Análisis de opiniones](#tab/sentiment-analysis)

> [!TIP]
> ¿Desea usar la versión más reciente de la API en la aplicación? Para obtener información sobre la versión actual de la API, consulte el artículo de procedimientos para el [análisis de sentimiento](../sentiment-opinion-mining/how-to/call-api.md) y el [inicio rápido](../sentiment-opinion-mining/quickstart.md). 

## <a name="feature-changes"></a>Cambios de características 

Análisis de sentimiento en la versión 2.1 devuelve las puntuaciones de opinión entre 0 y 1 para cada documento enviado a la API, con puntuaciones más próximas a 1 que indican una opinión más positiva. La versión actual de esta característica devuelve etiquetas de opinión (como "positivo" o "negativo") para las oraciones y el documento en su totalidad, así como sus puntuaciones de confianza asociadas. 

## <a name="steps-to-migrate"></a>Pasos para la migración

### <a name="rest-api"></a>API DE REST

Si la aplicación usa la API REST, actualice su punto de conexión de solicitud para usar el [punto de conexión actual](../sentiment-opinion-mining/quickstart.md?pivots=rest-api) para el análisis de sentimiento. Por ejemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment`. También necesitará actualizar la aplicación para usar las etiquetas de opinión devueltas en la [respuesta de la API](../sentiment-opinion-mining/how-to/call-api.md). 

Consulte la documentación de referencia para obtener ejemplos de la respuesta JSON.
* [Versión 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Versión 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

### <a name="client-libraries"></a>Bibliotecas de clientes

Para usar la versión más reciente de la biblioteca cliente de análisis de sentimiento, tendrá que descargar el paquete de software más reciente en el espacio de nombres `Azure.AI.TextAnalytics`. En el [artículo de inicio rápido](../sentiment-opinion-mining/quickstart.md) se muestran los comandos que puede usar para su lenguaje preferido, con código de ejemplo.

## <a name="ner-and-entity-linking"></a>[NER y vinculación de entidad](#tab/named-entity-recognition)

> [!TIP]
> ¿Desea usar la versión más reciente de la API en la aplicación? Consulte los artículos siguientes para obtener información sobre la versión actual de las API: NER:
> * [Guía de inicio rápido](../named-entity-recognition/quickstart.md)
> * [llamada a la API](../named-entity-recognition/how-to-call.md) Vinculación de entidad
> * [Guía de inicio rápido](../entity-linking/quickstart.md)
> * [llamada a la API](../entity-linking/how-to/call-api.md)

## <a name="feature-changes"></a>Cambios de características

En la versión 2.1, la API Text Analytics utiliza un punto de conexión para Reconocimiento de entidades con nombre (NER) y la vinculación de entidad. La versión actual de esta característica proporciona detección de entidades con nombre expandida y usa puntos de conexión independientes para las solicitudes de vinculación de entidad y NER. Además, puede usar otra característica ofrecida en el servicio de lenguaje que le permite detectar [información personal (pii) y médica (phi)](../personally-identifiable-information/overview.md). 

## <a name="steps-to-migrate"></a>Pasos para la migración

### <a name="rest-api"></a>API DE REST

Si la aplicación usa la API REST, actualice su punto de conexión de solicitud a los [puntos de conexión actuales](../named-entity-recognition/quickstart.md?pivots=rest-api) para NER o la vinculación de entidad. Por ejemplo:

Entity Linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/general`

También necesitará actualizar la aplicación para usar las [categorías de entidad](../named-entity-recognition/concepts/named-entity-categories.md) devueltas en la [respuesta de la API](../named-entity-recognition/how-to-call.md).

Consulte la documentación de referencia para obtener ejemplos de la respuesta JSON.
* [Versión 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Versión 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/EntitiesRecognitionGeneral)

### <a name="client-libraries"></a>Bibliotecas de clientes

Para usar la versión más reciente de las bibliotecas cliente de vinculación de entidad y NER, tendrá que descargar el paquete de software más reciente en el espacio de nombres `Azure.AI.TextAnalytics`. En el artículo de inicio rápido de [Reconocimiento de entidades con nombre](../named-entity-recognition/quickstart.md) y [vinculación de entidad](../entity-linking/quickstart.md) se muestran los comandos que puede usar para su lenguaje preferido, con código de ejemplo.

#### <a name="version-21-entity-categories"></a>Categorías de entidad de la versión 2.1

En la tabla siguiente se enumeran las categorías de entidad devueltas para NER v2.1.

| Category   | Descripción                          |
|------------|--------------------------------------|
| Person   |   Nombres de personas.  |
|Location    | Puntos de referencia naturales y humanos, estructuras, características geográficas y entidades geopolíticas. |
|Organización | Empresas, grupos políticos, bandas musicales, clubs deportivos, organismos gubernamentales y organizaciones públicas. Las nacionalidades y las religiones no se incluyen en este tipo de entidad. |
| PhoneNumber | Números de teléfono (solo números de teléfono de EE. UU y la UE). |
| Email | Direcciones de correo. |
| URL | Direcciones URL de sitios web. |
| IP | Direcciones IP de red. |
| DateTime | Fechas y horas del día.| 
| Date | Fechas calendario. |
| Time | Horas del día |
| DateRange | Intervalos de fechas. |
| TimeRange | Intervalos de horas. |
| Duration | Duraciones. |
| Set | Establecer varias veces repetidas. |
| Cantidad | Números y cantidades numéricas. |
| Number | Números. |
| Porcentaje | Porcentajes.|
| Ordinal | Números ordinales. |
| Age | Edades. |
| Moneda | Monedas. |
| Dimensión | Dimensiones y medidas. |
| Temperatura | Temperaturas. |

## <a name="language-detection"></a>[Detección de idioma](#tab/language-detection)

> [!TIP]
> ¿Desea usar la versión más reciente de la API en la aplicación? Para obtener información sobre la versión actual de la API, consulte el artículo de procedimientos para la [detección de idioma](../language-detection/how-to/call-api.md) y el [inicio rápido](../language-detection/quickstart.md). 

## <a name="feature-changes"></a>Cambios de características 

La salida de la característica Detección de idioma ha cambiado en la versión actual. La respuesta JSON contendrá `ConfidenceScore` en lugar de `score`. La versión actual también devuelve solo un idioma en un atributo `detectedLanguage` para cada documento.

## <a name="steps-to-migrate"></a>Pasos para la migración

### <a name="rest-api"></a>API DE REST

Si la aplicación usa la API REST, actualice su punto de conexión de solicitud al [punto de conexión actual](../language-detection/quickstart.md?pivots=rest-api) para la detección de idioma. Por ejemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/languages`. También necesitará actualizar la aplicación para usar `ConfidenceScore` en lugar de `score` en la [respuesta de la API](../language-detection/how-to/call-api.md). 

Consulte la documentación de referencia para obtener ejemplos de la respuesta JSON.
* [Versión 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Versión 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Languages)

#### <a name="client-libraries"></a>Bibliotecas de clientes

Para usar la versión más reciente de la biblioteca cliente de análisis de sentimiento, tendrá que descargar el paquete de software más reciente en el espacio de nombres `Azure.AI.TextAnalytics`. En el [artículo de inicio rápido](../language-detection/quickstart.md) se muestran los comandos que puede usar para su lenguaje preferido, con código de ejemplo.

## <a name="key-phrase-extraction"></a>[Extracción de frases clave](#tab/key-phrase-extraction)

> [!TIP]
> ¿Desea usar la versión más reciente de la API en la aplicación? Para obtener información sobre la versión actual de la API, consulte el artículo de procedimientos para la [extracción de frases clave](../key-phrase-extraction/how-to/call-api.md) y el [inicio rápido](../key-phrase-extraction/quickstart.md). 

## <a name="feature-changes"></a>Cambios de características 

La característica de extracción de frases clave no ha cambiado actualmente fuera de la versión del punto de conexión.

## <a name="steps-to-migrate"></a>Pasos para la migración

### <a name="rest-api"></a>API DE REST

Si la aplicación usa la API REST, actualice su punto de conexión de solicitud al [punto de conexión actual](../key-phrase-extraction/quickstart.md?pivots=rest-api) para la extracción de frases clave. Por ejemplo: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.1/keyPhrases`

Consulte la documentación de referencia para obtener ejemplos de la respuesta JSON.
* [Versión 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Versión 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/KeyPhrases)

### <a name="client-libraries"></a>Bibliotecas de clientes

Para usar la versión más reciente de la biblioteca cliente de análisis de sentimiento, tendrá que descargar el paquete de software más reciente en el espacio de nombres `Azure.AI.TextAnalytics`. En el [artículo de inicio rápido](../key-phrase-extraction/quickstart.md) se muestran los comandos que puede usar para su lenguaje preferido, con código de ejemplo.

---

## <a name="see-also"></a>Consulte también

* [¿Qué es Azure Cognitive Service for Language?](../overview.md)
