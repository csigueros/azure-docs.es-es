---
title: Aptitud cognitiva Opinión (V3)
titleSuffix: Azure Cognitive Search
description: Proporciona etiquetas de opinión para texto en una canalización de enriquecimiento con IA en Azure Cognitive Search
manager: jennmar
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: 1c74b716c6a22748a330fb3d325be3202ed89abc
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542773"
---
# <a name="sentiment-cognitive-skill-v3"></a>Aptitud cognitiva Opinión (V3)

> [!IMPORTANT] 
> Esta aptitud está actualmente en versión preliminar pública. La funcionalidad de versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no es aconsejable usarla para cargas de trabajo de producción. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Actualmente no hay compatibilidad con el portal ni con el SDK de .NET.

La aptitud **Opinión** V3 evalúa texto no estructurado y, para cada registro, proporciona etiquetas de opinión (como "negativa", "neutra" y "positiva") en función de la puntuación de confianza más alta que encontró el servicio a nivel de la oración y del documento. Esta aptitud utiliza los modelos de Machine Learning proporcionados por la versión 3 de [Text Analytics](../cognitive-services/text-analytics/overview.md) en Cognitive Services.

> [!NOTE]
> A medida que expanda el ámbito aumentando la frecuencia de procesamiento, agregando más documentos o agregando más algoritmos de IA, tendrá que [asociar un recurso facturable de Cognitive Services](cognitive-search-attach-cognitive-services.md). Los cargos se acumulan cuando se llama a las API de Cognitive Services y por la extracción de imágenes como parte de la fase de descifrado de documentos de Azure Cognitive Search. No hay ningún cargo por la extracción de texto de documentos.
>
> La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. Los precios de la extracción de imágenes se describen en la [página de precios de Búsqueda cognitiva de Azure](https://azure.microsoft.com/pricing/details/search/).


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