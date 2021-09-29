---
title: Aptitud cognitiva Opinión (V3)
titleSuffix: Azure Cognitive Search
description: Proporciona etiquetas de opinión para texto en una canalización de enriquecimiento con IA en Azure Cognitive Search
manager: jennmar
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: d42eb099091840ed5dbab61abbad47e087504ab3
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214529"
---
# <a name="sentiment-cognitive-skill-v3"></a>Aptitud cognitiva Opinión (V3)

La aptitud **Opinión** V3 evalúa texto no estructurado y, para cada registro, proporciona etiquetas de opinión (como "negativa", "neutra" y "positiva") en función de la puntuación de confianza más alta que encontró el servicio a nivel de la oración y del documento. Esta aptitud utiliza los modelos de Machine Learning proporcionados por la versión 3 de [Text Analytics](../cognitive-services/text-analytics/overview.md) en Cognitive Services. También expone las funcionalidades de minería de opiniones de [Text Analytics API](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining), que proporciona información más pormenorización sobre las opiniones relacionadas con atributos de productos o servicios en texto.

> [!NOTE]
> Esta aptitud está enlazada a Cognitive Services y necesita [un recurso facturable](cognitive-search-attach-cognitive-services.md) para las transacciones que superan los 20 documentos por indizador al día. La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.SentimentSkill

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe tener 5000 caracteres, medido por [`String.Length`](/dotnet/api/system.string.length). Si tiene que desglosar los datos antes de enviarlos a la aptitud de opiniones, use la [aptitud División de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro | Descripción |
|----------------|----------------------|
| `defaultLanguageCode` | (Opcional) Es el código de idioma que se aplicará a los documentos que no especifiquen el lenguaje de forma explícita. <br/> Vea [Full list of supported languages](../cognitive-services/text-analytics/language-support.md) (Lista completa de idiomas admitidos). |
| `modelVersion`   | (Opcional) La versión del modelo que se va a usar al llamar al servicio de Text Analytics. Si no se especifica, el valor predeterminado será la versión más reciente disponible. Se recomienda no especificar este valor a menos que sea absolutamente necesario. Vea [Control de versiones de modelos en Text Analytics API](../cognitive-services/text-analytics/concepts/model-versioning.md) para obtener más información. |
| `includeOpinionMining` | Si se establece en `true`, habilita [la característica de minería de opiniones de TextAnalytics](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining), que permite incluir en los resultados de salida el análisis de sentimiento basado en aspectos. Su valor predeterminado es `false`. |

## <a name="skill-inputs"></a>Entradas de la aptitud 

| Nombre de entrada | Descripción |
|--------------------|-------------|
| `text` | Texto que se va a analizar.|
| `languageCode`    |  (Opcional) Cadena que indica el idioma de los registros. Si esta no se especifica este parámetro, se usa el valor predeterminado "en". <br/>Vea [Full list of supported languages](../cognitive-services/text-analytics/language-support.md) (Lista completa de idiomas admitidos).|

## <a name="skill-outputs"></a>Salidas de la aptitud

| Nombre de salida | Descripción |
|--------------------|-------------|
| `sentiment` | Valor de cadena que representa la etiqueta de opinión de todo el texto analizado (positiva, neutra o negativa). |
| `confidenceScores` | Un tipo complejo con tres valores dobles, uno para la calificación positiva, otro para la calificación neutra y otro para la calificación negativa. Los valores oscilan entre 0 y 1,00, donde 1,00 representa la mayor confianza posible en una asignación de etiqueta determinada. |
| `sentences` | Colección de tipos complejos que desglosa la opinión del texto frase por frase. Aquí también es donde se devuelven los resultados de minería de opiniones en forma de destinos y evaluaciones si `includeOpinionMining` se establece en `true`.|


##  <a name="sample-definition"></a>Definición de ejemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
    "context": "/document",
    "includeOpinionMining": true,
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languageCode"
        }
    ],
    "outputs": [
        {
            "name": "sentiment",
            "targetName": "sentiment"
        },
        {
            "name": "confidenceScores",
            "targetName": "confidenceScores"
        },
        {
            "name": "sentences",
            "targetName": "sentences"
        }
    ]
}
```

##  <a name="sample-input"></a>Entrada de ejemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Salida de ejemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "sentiment": "negative",
                "confidenceScores": {
                    "positive": 0.0,
                    "neutral": 0.0,
                    "negative": 1.0
                },
                "sentences": [
                    {
                        "text": "I had a terrible time at the hotel.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset": 0,
                        "length": 35,
                        "targets": [],
                        "assessments": [],
                    },
                    {
                        "text": "The staff was rude and the food was awful.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset":36,
                        "length": 42,
                        "targets": [
                            {
                                "text": "staff",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 40,
                                "length": 5,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/0",
                                    }
                                ]
                            },
                            {
                                "text": "food",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 63,
                                "length": 4,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/1",
                                    }
                                ]
                            }
                        ],
                        "assessments": [
                            {
                                "text": "rude",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 50,
                                "length": 4,
                                "isNegated": false
                            },
                            {
                                "text": "awful",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 72,
                                "length": 5,
                                "isNegated": false
                            }
                        ],
                    }
                ]
            }
        }
    ]
}
```

## <a name="warning-cases"></a>Casos de advertencia
Si el texto está vacío, se genera una advertencia y no se devuelven resultados de opinión.
Si no se admite un idioma, se genera una advertencia y no se devuelven resultados de opinión.

## <a name="see-also"></a>Consulte también

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
