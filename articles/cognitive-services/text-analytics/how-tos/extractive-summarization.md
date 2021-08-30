---
title: Resumen de texto con la API de resumen extractivo de Text Analytics
titleSuffix: Azure Cognitive Services
description: En este artículo se muestra cómo resumir texto con la API de resumen extractivo de Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: aahi
ms.openlocfilehash: a55c281899495819ad8a537995867485b02c0af5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780151"
---
# <a name="how-to-summarize-text-with-text-analytics-preview"></a>Resumen de texto con Text Analytics (versión preliminar)

> [!IMPORTANT] 
> El resumen extractivo de Text Analytics es una funcionalidad de versión preliminar que se proporciona "tal cual" y "con todos los errores". Como tal, no se debe implementar en ningún uso de producción. El cliente es el único responsable de cualquier uso del resumen extractivo de Text Analytics. 

En general, hay dos enfoques de resumen automático de texto: extractivo y abstractivo. La API de Text Analytics proporciona resumen extractivo a partir de la versión `3.2-preview.1`.

El resumen extractivo es una característica de Azure Text Analytics que genera un resumen mediante la extracción de oraciones que representan colectivamente la información más importante o pertinente dentro del contenido original.

Esta característica está diseñada para abreviar el contenido que los usuarios consideran demasiado largo para leer. El resumen extractivo condensa artículos, trabajos o documentos en oraciones clave.

El servicio proporciona los modelos de AI que usa la API, el usuario solo tiene que enviar contenido para su análisis.

## <a name="extractive-summarization-and-features"></a>Resumen extractivo y características

La característica de resumen extractivo de Text Analytics usa técnicas de procesamiento del lenguaje natural para buscar oraciones clave en un documento de texto no estructurado. Estas oraciones transmiten colectivamente la idea principal del documento.

El resumen extractivo devuelve una puntuación de clasificación como parte de la respuesta del sistema, junto con las oraciones extraídas y su posición en los documentos originales. Una puntuación de clasificación es un indicador de lo pertinente que se determina que es una oración para la idea principal de un documento. El modelo proporciona una puntuación de entre 0 y 1 (ambos inclusive) a cada oración y devuelve las oraciones con mayor puntuación por solicitud. Por ejemplo, si solicita un resumen de tres oraciones, el servicio devuelve las tres oraciones con mayor puntuación.

Text Analytics incluye otra característica, [la extracción de frases clave](./text-analytics-how-to-keyword-extraction.md), con la que es posible extraer información. A la hora de decidir si usar la extracción de frases clave o el resumen extractivo, tenga presente lo siguiente:
* La extracción de frases clave devuelve frases, mientras que el resumen extractivo devuelve oraciones.
* El resumen extractivo devuelve oraciones junto con una puntuación de clasificación. Se devolverán las oraciones con mayor puntuación.

## <a name="sending-a-rest-api-request"></a>Envío de una solicitud de API REST 

> [!TIP]
> También puede usar la versión preliminar más reciente de la biblioteca cliente para usar el resumen extractivo. Consulte los siguientes ejemplos en GitHub: 
> * [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample8_ExtractSummary.md)
> * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java)
> * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_extract_summary.py)

### <a name="preparation"></a>Preparación

A diferencia de otras características de Text Analytics, la característica de resumen extractivo es una operación exclusivamente asincrónica a la que se puede acceder a través del punto de conexión /analyze. Los datos de solicitud JSON deben seguir el formato que se describe en [Solicitudes asincrónicas al punto de conexión /analyze](./text-analytics-how-to-call-api.md?tabs=asynchronous#api-request-formats).

El resumen extractivo admite una amplia gama de idiomas para la entrada de documentos. Para más información, consulte [Lenguajes admitidos](../language-support.md).

El tamaño del documento debe ser inferior a 125 000 caracteres. Para conocer el número máximo de documentos permitidos en una colección, consulte el artículo sobre [límites de datos](../concepts/data-limits.md?tabs=version-3). La colección se envía en el cuerpo de la solicitud.

### <a name="structure-the-request"></a>Estructurar la solicitud

Cree una solicitud POST. Puede [usar Postman](text-analytics-how-to-call-api.md) o la **consola de pruebas de API** en el siguiente vínculo de referencia para estructurar y enviar rápidamente una. 

[Referencia de resumen de texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-preview-1/operations/Analyze)

### <a name="request-endpoints"></a>Puntos de conexión de solicitud

Para establecer el punto de conexión HTTPS de resumen extractivo, use un recurso de Text Analytics en Azure. Por ejemplo:

> [!NOTE]
> Tanto la clave como el punto de conexión del recurso de Text Analytics se pueden encontrar en Azure Portal. Búsquelos en la página **Clave y punto de conexión** del recurso. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze`

Establezca un encabezado de la solicitud para incluir la clave de la API Text Analytics. En el cuerpo de la solicitud, proporcione la colección de documentos JSON que preparó para este análisis.

### <a name="example-request"></a>Solicitud de ejemplo 

A continuación se muestra un ejemplo de contenido que se podrá enviar a resumir, el cual se extrae usando [una representación holística hacia la inteligencia artificial integrativa](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/) con fines de demostración. La API de resumen extractivo puede aceptar texto de entrada mucho más largo. Consulte [Límites de datos](../Concepts/data-limits.md) para obtener más información sobre los límites de datos de la API de Text Analytics.

Una sola solicitud puede incluir varios documentos. 

Cada documento tiene los siguientes parámetros:
* `id` para identificar un documento 
* `language` para indicar el idioma de origen del documento, siendo `en` el valor predeterminado
* `text` para adjuntar el texto del documento

Todos los documentos de una solicitud comparten los siguientes parámetros. Estos parámetros se pueden especificar en la definición `tasks` de la solicitud:
* `model-version` para especificar qué versión del modelo se va a usar, siendo `latest` el valor predeterminado. Para obtener más información, consulte [Versión del modelo](../concepts/model-versioning.md). 
* `sentenceCount` para especificar cuántas oraciones se devolverán, siendo `3` el valor predeterminado. El rango es de 1 a 20.
* `sortyby` para especificar en qué orden se devolverán las oraciones extraídas. Los valores admitidos para `sortBy` son `Offset` y `Rank`, siendo `Offset` el predeterminado. El valor `Offset` es la posición inicial de una oración en el documento original.    

```json
{
  "analysisInput": {
    "documents": [
      {
        "language": "en",
        "id": "1",
        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."
      }
    ]
  },
  "tasks": {
    "extractiveSummarizationTasks": [
      {
        "parameters": {
          "model-version": "latest",
          "sentenceCount": 3,
          "sortBy": "Offset"
        }
      }
    ]
  }
}
```

### <a name="post-the-request"></a>Publicar la solicitud

La API de resumen extractivo se ejecuta tras la recepción de la solicitud. Para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte la sección de [límites de datos](../overview.md#data-limits) de la introducción.

La API de resumen extractivo de Text Analytics es una API asincrónica, por lo que el objeto de respuesta no incluye texto. Sin embargo, necesita el valor de la clave `operation-location` en los encabezados de respuesta con el fin de realizar una solicitud GET para comprobar el estado del trabajo y la salida.  A continuación se muestra un ejemplo del valor de la KEY de operation-location en el encabezado de respuesta de la solicitud POST:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/<jobID>`

Para comprobar el estado del trabajo, realice una solicitud GET a la dirección URL en el valor del encabezado KEY DE operation-Location de la respuesta POST.  Los siguientes estados se usan para reflejar el estado de un trabajo: `NotStarted`, `running`, `succeeded`, `failed`, o `rejected`.  

Si el trabajo se realiza correctamente, la salida de la API se devolverá en el cuerpo de la solicitud GET. 


### <a name="view-the-results"></a>View the results

El siguiente es un ejemplo de la respuesta de una solicitud GET.  La salida está disponible para su recuperación hasta que transcurre el valor de `expirationDateTime` (24 horas desde el momento en que se creó el trabajo), tras lo cual se purga la salida. Debido a la compatibilidad con varios idiomas y con Emojis, la respuesta puede contener desplazamientos de texto. Consulte [cómo procesar desplazamientos](../concepts/text-offsets.md) para más información.

### <a name="example-response"></a>Respuesta de ejemplo
  
La característica de resumen extractivo devuelve lo siguiente: 

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-06-11T05:43:37Z",
    "createdDateTime": "2021-06-11T05:42:32Z",
    "expirationDateTime": "2021-06-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "sentences": [
                    {
                        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding.",
                        "rankScore": 0.7673416137695312,
                        "Offset": 0,
                        "length": 160
                    },
                    {
                        "text": "In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z).",
                        "rankScore": 0.7644073963165283,
                        "Offset": 324,
                        "length": 192
                    },
                    {
                        "text": "At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better.",
                        "rankScore": 0.7623870968818665,
                        "Offset": 517,
                        "length": 203
                    }    
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-08-01"
    }
}
```

## <a name="summary"></a>Resumen

En este artículo ha conocido los conceptos y el flujo de trabajo del resumen extractivo usando la API de resumen extractivo de Text Analytics. Podría interesarle usar el resumen extractivo para lo siguiente:

* Ayudar en el procesamiento de documentos para mejorar la eficacia.
* Extraer información crítica de documentos largos, informes y otros formatos de texto.
* Resaltar oraciones clave en documentos.
* Hojear rápidamente documentos en una biblioteca.
* Generar contenido de fuentes de noticias.

## <a name="see-also"></a>Consulte también

* [Información general de Text Analytics](../overview.md)
* [Novedades](../whats-new.md)
* [Versiones del modelo](../concepts/model-versioning.md)