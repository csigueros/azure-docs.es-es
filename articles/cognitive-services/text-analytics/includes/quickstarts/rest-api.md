---
title: 'Inicio rápido: Uso de cURL para llamar a la API REST Text Analytics'
titleSuffix: Azure Cognitive Services
description: En este inicio rápido se muestra cómo empezar a usar rápidamente la API Text Analytics en Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/25/2021
ms.author: aahi
ms.openlocfilehash: 93c71179de0f932a583b481e14bc89dc316f5235
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864872"
---
#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

[Documentación de referencia de la versión 3.1](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-Preview-1)

#### <a name="version-31"></a>[Versión 3.1](#tab/version-3-1)

[Documentación de referencia de la versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1) 

---

## <a name="prerequisites"></a>Prerrequisitos

* La versión actual de [cURL](https://curl.haxx.se/).
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Una vez que tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="cree un recurso de Text Analytics"  target="_blank">cree un recurso de Text Analytics </a> en Azure Portal para obtener la clave y el punto de conexión. Una vez que se implemente, haga clic en **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a API Text Analytics. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    * Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

> [!NOTE]
> * En los siguientes ejemplos de BASH se usa el carácter de continuación de línea `\`. Si la consola o el terminal usan un carácter de continuación de línea diferente, use ese carácter.
> * Puede encontrar ejemplos específicos del lenguaje en [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
> * Vaya a Azure Portal y busque la clave y el punto de conexión para el recurso de Text Analytics que creó en los requisitos previos. Los encontrará en la página de **clave y punto de conexión** del recurso, en **Administración de recursos**. A continuación, reemplace las cadenas del código siguiente por su clave y punto de conexión.
Para llamar a la API Text Analytics, necesita la siguiente información:


|parámetro  |Descripción  |
|---------|---------|
|`-X POST <endpoint>`     | Especifica el punto de conexión para acceder a la API.        |
|`-H Content-Type: application/json`     | Tipo de contenido para enviar datos JSON.          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | Especifica la clave para acceder a la API.        |
|`-d <documents>`     | JSON que contiene los documentos que desea enviar.         |

Los siguientes comandos de cURL se ejecutan desde un shell de BASH. Edite estos comandos con sus propios valores de nombre de recurso, clave de recurso y JSON.

## <a name="sentiment-analysis-and-opinion-mining"></a>Análisis de sentimiento y minería de opiniones

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

> [!NOTE]
> En los ejemplos siguientes se incluye una solicitud para la característica de minería de opiniones de Análisis de sentimiento mediante el parámetro `opinionMining=true`, que proporciona información detallada sobre las valoraciones (adjetivos) relacionadas con los destinos (nombres) del texto.

#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.2-preview.1/sentiment?opinionMining=true \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "The customer service here is really good."}]}'
```

#### <a name="version-31"></a>[versión 3.1](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1/sentiment?opinionMining=true \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "The customer service here is really good."}]}'
```

---

### <a name="json-response"></a>Respuesta JSON

[!INCLUDE [REST API response is the same for both versions](../rest-api-response-same.md)]

```json
{
   "documents":[
      {
         "id":"1",
         "sentiment":"positive",
         "confidenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
         },
         "sentences":[
            {
               "sentiment":"positive",
               "confidenceScores":{
                  "positive":1.0,
                  "neutral":0.0,
                  "negative":0.0
               },
               "offset":0,
               "length":41,
               "text":"The customer service here is really good.",
               "targets":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":4,
                     "length":16,
                     "text":"customer service",
                     "relations":[
                        {
                           "relationType":"assessment",
                           "ref":"#/documents/0/sentences/0/assessments/0"
                        }
                     ]
                  }
               ],
               "assessments":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":36,
                     "length":4,
                     "text":"good",
                     "isNegated":false
                  }
               ]
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
``` 

## <a name="language-detection"></a>Detección de idiomas

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.2-preview.1/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

#### <a name="version-31"></a>[versión 3.1](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

---

### <a name="json-response"></a>Respuesta JSON

[!INCLUDE [REST API response is the same for both versions](../rest-api-response-same.md)]

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":1.0
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-05"
}
```

## <a name="named-entity-recognition-ner"></a>Reconocimiento de entidades con nombre (NER)

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.2-preview.1/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

#### <a name="version-31"></a>[versión 3.1](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

---

### <a name="json-response"></a>Respuesta JSON

[!INCLUDE [REST API response is the same for both versions](../rest-api-response-same.md)]

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.99
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-15"
}

```

## <a name="personally-identifiable-information-pii-recognition"></a>Reconocimiento de la información de identificación personal

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```bash
curl -X POST https://your-text-analytics-endpoint-here>/text/analytics/v3.2-preview.1/entities/recognition/pii \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Call our office at 312-555-1234, or send an email to support@contoso.com"}]}'
```

#### <a name="version-31"></a>[versión 3.1](#tab/version-3-1)

```bash
curl -X POST https://your-text-analytics-endpoint-here>/text/analytics/v3.1/entities/recognition/pii \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Call our office at 312-555-1234, or send an email to support@contoso.com"}]}'
```

---

### <a name="json-response"></a>Respuesta JSON

[!INCLUDE [REST API response is the same for both versions](../rest-api-response-same.md)]

```json
{
   "documents":[
      {
         "redactedText":"Call our office at ************, or send an email to *******************",
         "id":"1",
         "entities":[
            {
               "text":"312-555-1234",
               "category":"PhoneNumber",
               "offset":19,
               "length":12,
               "confidenceScore":0.8
            },
            {
               "text":"support@contoso.com",
               "category":"Email",
               "offset":53,
               "length":19,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-15"
}
```

## <a name="entity-linking"></a>Vinculación de entidad

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.2-preview.1/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```

#### <a name="version-31"></a>[versión 3.1](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```

---

### <a name="json-response"></a>Respuesta JSON

[!INCLUDE [REST API response is the same for both versions](../rest-api-response-same.md)]

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"0d47c987-0042-5576-15e8-97af601614fa",
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"52535f87-235e-b513-54fe-c03e4233ac6e",
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```

## <a name="key-phrase-extraction"></a>Extracción de la frase clave

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.2-preview.1/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

#### <a name="version-31"></a>[versión 3.1](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

---

### <a name="json-response"></a>Respuesta JSON

[!INCLUDE [REST API response is the same for both versions](../rest-api-response-same.md)]


```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-06-01"
}
```

## <a name="text-analytics-for-health"></a>Text Analytics for Health


[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```bash
curl -i -X POST https://your-text-analytics-endpoint-here/text/analytics/v3.2-preview.1/entities/health/jobs \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here" \
-d '{ documents: [{ id: "1", language:"en", text: "Subject was administered 100mg remdesivir intravenously over a period of 120 min"}]}'
```

Obtenga el valor de `operation-location` del encabezado de respuesta. El valor será similar a la dirección URL siguiente:

```rest
https://your-resource.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/entities/health/jobs/12345678-1234-1234-1234-12345678
```

Para obtener los resultados de la solicitud, use el siguiente comando cURL. Asegúrese de reemplazar `my-job-id` por el valor de identificador numérico que recibió del encabezado de respuesta `operation-location` anterior:

```bash
curl -X GET  https://your-text-analytics-endpoint-here/text/analytics/v3.2-preview.1/entities/health/jobs/my-job-id \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here"
```

#### <a name="version-31"></a>[versión 3.1](#tab/version-3-1)

```bash
curl -i -X POST https://your-text-analytics-endpoint-here/text/analytics/v3.1/entities/health/jobs \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here" \
-d '{ documents: [{ id: "1", language:"en", text: "Subject was administered 100mg remdesivir intravenously over a period of 120 min"}]}'
```

Obtenga el valor de `operation-location` del encabezado de respuesta. El valor será similar a la dirección URL siguiente:

```rest
https://your-resource.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs/12345678-1234-1234-1234-12345678
```

Para obtener los resultados de la solicitud, use el siguiente comando cURL. Asegúrese de reemplazar `my-job-id` por el valor de identificador numérico que recibió del encabezado de respuesta `operation-location` anterior:

```bash
curl -X GET  https://your-text-analytics-endpoint-here/text/analytics/v3.1/entities/health/jobs/my-job-id \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here"
```

---

### <a name="json-response"></a>Respuesta JSON

[!INCLUDE [REST API response is the same for both versions](../rest-api-response-same.md)]

```json
{
   "jobId":"12345678-1234-1234-1234-12345678",
   "lastUpdateDateTime":"2021-08-02T21:32:43Z",
   "createdDateTime":"2021-08-02T21:32:04Z",
   "expirationDateTime":"2021-08-03T21:32:04Z",
   "status":"succeeded",
   "errors":[
      
   ],
   "results":{
      "documents":[
         {
            "id":"1",
            "entities":[
               {
                  "offset":25,
                  "length":5,
                  "text":"100mg",
                  "category":"Dosage",
                  "confidenceScore":1.0
               },
               {
                  "offset":31,
                  "length":10,
                  "text":"remdesivir",
                  "category":"MedicationName",
                  "confidenceScore":1.0,
                  "name":"remdesivir",
                  "links":[
                     {
                        "dataSource":"UMLS",
                        "id":"C4726677"
                     },
                     {
                        "dataSource":"DRUGBANK",
                        "id":"DB14761"
                     },
                     {
                        "dataSource":"GS",
                        "id":"6192"
                     },
                     {
                        "dataSource":"MEDCIN",
                        "id":"398132"
                     },
                     {
                        "dataSource":"MMSL",
                        "id":"d09540"
                     },
                     {
                        "dataSource":"MSH",
                        "id":"C000606551"
                     },
                     {
                        "dataSource":"MTHSPL",
                        "id":"3QKI37EEHE"
                     },
                     {
                        "dataSource":"NCI",
                        "id":"C152185"
                     },
                     {
                        "dataSource":"NCI_FDA",
                        "id":"3QKI37EEHE"
                     },
                     {
                        "dataSource":"NDDF",
                        "id":"018308"
                     },
                     {
                        "dataSource":"RXNORM",
                        "id":"2284718"
                     },
                     {
                        "dataSource":"SNOMEDCT_US",
                        "id":"870592005"
                     },
                     {
                        "dataSource":"VANDF",
                        "id":"4039395"
                     }
                  ]
               },
               {
                  "offset":42,
                  "length":13,
                  "text":"intravenously",
                  "category":"MedicationRoute",
                  "confidenceScore":0.99
               },
               {
                  "offset":73,
                  "length":7,
                  "text":"120 min",
                  "category":"Time",
                  "confidenceScore":0.98
               }
            ],
            "relations":[
               {
                  "relationType":"DosageOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/0",
                        "role":"Dosage"
                     },
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     }
                  ]
               },
               {
                  "relationType":"RouteOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     },
                     {
                        "ref":"#/results/documents/0/entities/2",
                        "role":"Route"
                     }
                  ]
               },
               {
                  "relationType":"TimeOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     },
                     {
                        "ref":"#/results/documents/0/entities/3",
                        "role":"Time"
                     }
                  ]
               }
            ],
            "warnings":[
               
            ]
         }
      ],
      "errors":[
         
      ],
      "modelVersion":"2021-05-15"
   }
}
```
---

## <a name="text-summarization"></a>Resumen de texto

#### <a name="version-32-preview1"></a>[Versión 3.2-preview.1](#tab/version-3-2)

```bash
curl -i -X POST https://your-text-analytics-endpoint-here/text/analytics/v3.2-preview.1/analyze \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here" \
-d \
' 
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
'
```

Obtenga el valor de `operation-location` del encabezado de respuesta. El valor será similar a la dirección URL siguiente:

```rest
https://your-resource.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/12345678-1234-1234-1234-12345678
```

Para obtener los resultados de la solicitud, use el siguiente comando cURL. Asegúrese de reemplazar `my-job-id` por el valor de identificador numérico que recibió del encabezado de respuesta `operation-location` anterior:

```bash
curl -X GET    https://your-text-analytics-endpoint-here/text/analytics/v3.2-preview.1/analyze/jobs/my-job-id \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here"
```


### <a name="output"></a>Output

```json
{
   "jobId":"da3a2f68-eb90-4410-b28b-76960d010ec6",
   "lastUpdateDateTime":"2021-08-24T19:15:47Z",
   "createdDateTime":"2021-08-24T19:15:28Z",
   "expirationDateTime":"2021-08-25T19:15:28Z",
   "status":"succeeded",
   "errors":[
      
   ],
   "displayName":"NA",
   "tasks":{
      "completed":1,
      "failed":0,
      "inProgress":0,
      "total":1,
      "extractiveSummarizationTasks":[
         {
            "lastUpdateDateTime":"2021-08-24T19:15:48.0011189Z",
            "taskName":"ExtractiveSummarization_latest",
            "state":"succeeded",
            "results":{
               "documents":[
                  {
                     "id":"1",
                     "sentences":[
                        {
                           "text":"At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding.",
                           "rankScore":1.0,
                           "offset":0,
                           "length":160
                        },
                        {
                           "text":"In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z).",
                           "rankScore":0.9582327572675664,
                           "offset":324,
                           "length":192
                        },
                        {
                           "text":"At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better.",
                           "rankScore":0.9294747193132348,
                           "offset":517,
                           "length":203
                        }
                     ],
                     "warnings":[
                        
                     ]
                  }
               ],
               "errors":[
                  
               ],
               "modelVersion":"2021-08-01"
            }
         }
      ]
   }
}
```

#### <a name="version-31"></a>[versión 3.1](#tab/version-3-1)

Esta característica no está disponible en la versión 3.1.

---