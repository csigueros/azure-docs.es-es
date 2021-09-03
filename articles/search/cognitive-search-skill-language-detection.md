---
title: Aptitud cognitiva para la detección de idiomas
titleSuffix: Azure Cognitive Search
description: Evalúa el texto no estructurado y devuelve para cada registro un identificador de idioma con una puntuación que indica la solidez del análisis en una canalización de enriquecimiento de inteligencia artificial de Azure Cognitive Search.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 01b6449baa7c5d13b041f886074425d78e037579
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862106"
---
#   <a name="language-detection-cognitive-skill"></a>Aptitud cognitiva para la detección de idiomas

La aptitud **Detección de idioma** detecta el idioma del texto de entrada e informa de un único código de idioma para cada documento enviado en la solicitud. El código de idioma se empareja con una puntuación que indica la intensidad del análisis. Esta aptitud utiliza los modelos de aprendizaje automático proporcionados por [Text Analytics](../cognitive-services/text-analytics/overview.md) en Cognitive Services.

Esta funcionalidad es especialmente útil cuando necesita proporcionar el idioma del texto como entrada para otras aptitudes (por ejemplo, la [aptitud de análisis de opiniones](cognitive-search-skill-sentiment-v3.md) o la [aptitud de división de texto](cognitive-search-skill-textsplit.md)).

La detección de idioma aprovecha las bibliotecas de procesamiento de lenguaje natural de Bing, lo que supera el número de [idiomas y regiones admitidos](../cognitive-services/text-analytics/language-support.md) enumerados para Text Analytics. La lista exacta de idiomas no está publicada, pero incluye todos los idiomas ampliamente hablados, además de variantes, dialectos y algunos idiomas regionales y culturales. Si tiene contenido que se expresa en un idioma que se usa con menos frecuencia, puede [probar Language Detection API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) para ver si devuelve un código. La respuesta para los idiomas que no se pueden detectar es `(Unknown)`.

> [!NOTE]
> Esta aptitud está enlazada a Cognitive Services y requiere [un recurso facturable](cognitive-search-attach-cognitive-services.md) para las transacciones que superan los 20 documentos por indizador al día. La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe tener menos de 50 000 caracteres según la medición de [`String.Length`](/dotnet/api/system.string.length). Si tiene que dividir los datos antes de enviarlos a la aptitud de detección de idioma, puede usar la [aptitud de división de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Entradas | Descripción |
|---------------------|-------------|
| `defaultCountryHint` | (Opcional) Se puede proporcionar un código de país de dos letras ISO 3166-1 alpha-2 para usarlo como sugerencia para el modelo de detección de idioma, si no puede eliminar la ambigüedad del idioma. Consulte [la documentación de Text Analytics](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) de este tema para obtener más detalles. En concreto, el parámetro `defaultCountryHint` se utiliza con documentos que no especifican la entrada `countryHint` explícitamente.  |
| `modelVersion`   | (Opcional) La versión del modelo que se va a usar al llamar al servicio de Text Analytics. Si no se especifica, el valor predeterminado será el más reciente disponible. Se recomienda no especificar este valor a menos que sea absolutamente necesario. Vea [Control de versiones de modelos en Text Analytics API](../cognitive-services/text-analytics/concepts/model-versioning.md) para obtener más información. |

## <a name="skill-inputs"></a>Entradas de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Entradas     | Descripción |
|--------------------|-------------|
| `text` | Texto que se va a analizar.|
| `countryHint` | Un código de país de dos letras ISO 3166-1 alpha-2 para usarlo como sugerencia para el modelo de detección de idioma, si no puede eliminar la ambigüedad del idioma. Consulte [la documentación de Text Analytics](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) de este tema para obtener más detalles. |

## <a name="skill-outputs"></a>Salidas de la aptitud

| Nombre de salida    | Descripción |
|--------------------|-------------|
| `languageCode` | El código de idioma ISO 6391 para el idioma identificado. Por ejemplo, "en". |
| `languageName` | El nombre del idioma. Por ejemplo, "inglés". |
| `score` | Un valor entre 0 y 1. La probabilidad de que el lenguaje esté correctamente identificado. La puntuación puede ser inferior a 1 si la oración tiene distintos idiomas.  |

##  <a name="sample-definition"></a>Definición de ejemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Salida de ejemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Consulte también

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)