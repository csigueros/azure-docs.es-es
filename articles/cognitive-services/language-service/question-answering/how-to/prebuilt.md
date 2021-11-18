---
title: 'API precompilada: Respuesta a preguntas'
titleSuffix: Azure Cognitive Services
description: Use la API precompilada de respuesta a preguntas para formular preguntas y recibir respuestas a estas sin tener que crear un proyecto o knowledge base.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: 9fe4b80220ccb94e2b89bcdeaacf8cacac624ed0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159429"
---
# <a name="prebuilt-api"></a>API precompilada

La **API precompilada** de respuesta a preguntas ofrece al usuario la posibilidad de responder preguntas de un fragmento de texto sin tener que crear proyectos o knowledge bases, mantener pares de preguntas y respuestas ni incurrir en los costos de una infraestructura infrautilizada. Esta funcionalidad se proporciona como una API y se puede usar para satisfacer las necesidades de preguntas y respuestas sin tener que aprender los detalles sobre la respuesta a preguntas.

Dada una consulta de usuario y un bloque de texto o fragmento, la API devolverá una respuesta y una respuesta precisa (si está disponible).

## <a name="example-api-usage"></a>Ejemplo de uso de API

Imagine que tiene uno o varios bloques de texto de los que le gustaría obtener las respuestas a una pregunta determinada. Normalmente, habría tenido que crear tantos orígenes como número de bloques de texto. Sin embargo, ahora con la API precompilada puede consultar los bloques de texto sin tener que definir orígenes de contenido en un proyecto o knowledge base.

A continuación se indican otros escenarios en los que se puede usar esta API:

* Está desarrollando una aplicación de lector de libros electrónicos para usuarios finales que les permite resaltar texto, escribir una pregunta y encontrar respuestas en un pasaje de texto resaltado.
* Una extensión del navegador permite a los usuarios hacer una pregunta sobre el contenido que se muestra actualmente en la página del navegador.
* Un bot de mantenimiento recibe las consultas de los usuarios y proporciona respuestas basadas en el contenido médico que el bot identifica como más pertinente para la consulta del usuario.

A continuación se muestra un ejemplo de una solicitud:

## <a name="sample-request"></a>Solicitud de ejemplo

```
POST https://{Unique-to-your-endpoint}.api.cognitive.microsoft.com/language/:query-text
```

### <a name="sample-query-over-a-single-block-of-text"></a>Consulta de ejemplo sobre un único bloque de texto

Cuerpo de la solicitud

```json
{
  "parameters": {
    "Endpoint": "{Endpoint}",
    "Ocp-Apim-Subscription-Key": "{API key}",
    "Content-Type": "application/json",
    "api-version": "2021-10-01",
    "stringIndexType": "TextElements_v8",
    "textQueryOptions": {
      "question": "how long it takes to charge surface?",
      "records": [
        {
          "id": "1",
          "text": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it."
        },
        {
          "id": "2",
          "text": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."
        }
      ],
      "language": "en"
    }
  }
}
```

## <a name="sample-response"></a>Respuesta de muestra

En el cuerpo de la solicitud anterior, se consulta sobre un único bloque de texto. A continuación se muestra una respuesta de ejemplo recibida para la consulta anterior.

```json
{
"responses": {
    "200": {
      "headers": {},
      "body": {
        "answers": [
          {
            "answer": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.93,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 28,
              "length": 45
            },
            "offset": 0,
            "length": 224
          },
          {
            "answer": "It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.92,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 8,
              "length": 25
            },
            "offset": 20,
            "length": 224
          },
          {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.05,
            "id": "1",
            "answerSpan": null,
            "offset": 110,
            "length": 244
          }
        ]
      }
    }
  }
```

Vemos que se reciben varias respuestas como parte de la respuesta global de la API. Cada respuesta tiene una puntuación de confianza específica que ayuda a comprender su relevancia general. El intervalo de respuestas representa si también se detectó una posible respuesta corta. Los usuarios pueden usar esta puntuación de confianza para determinar qué respuestas proporcionar en respuesta a la consulta.

## <a name="prebuilt-api-limits"></a>Límites de la API precompilada

Si necesita usar documentos con un tamaño superior al límite permitido, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. En este contexto, un documento es una cadena única definida de caracteres de texto.

Estos números representan los **límites de cada llamada API individual**:

* Número de documentos: 5.
* Tamaño máximo de un solo documento: 5120 caracteres.
* Máximo de tres respuestas por documento.

## <a name="prebuilt-api-reference"></a>Referencia de la API precompilada

Visite la documentación completa de [ejemplos de la API precompilada](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/Language/stable/2021-10-01/examples/questionanswering/SuccessfulQueryText.json) para conocer los parámetros de entrada y salida necesarios para llamar a la API.