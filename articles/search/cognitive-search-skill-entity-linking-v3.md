---
title: Aptitud cognitiva para la vinculación de entidad
titleSuffix: Azure Cognitive Search
description: Extraiga distintas entidades vinculadas del texto en una canalización de enriquecimiento en Azure Cognitive Search.
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 4b052e6ac5746d771e7725b39c13fd57b20facb8
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038469"
---
# <a name="entity-linking-cognitive-skill"></a>Aptitud cognitiva para la vinculación de entidad

La aptitud **Vinculación de entidad** devuelve una lista de entidades reconocidas con vínculos a artículos de una base de conocimientos popular (Wikipedia).

> [!NOTE]
> Esta aptitud está enlazada a [Text Analytics](../cognitive-services/text-analytics/overview.md) de Cognitive Services y necesita un [recurso facturable](cognitive-search-attach-cognitive-services.md) para las transacciones que superan los 20 documentos por indexador al día. La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes.
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.V3.EntityLinkingSkill

## <a name="data-limits"></a>Límites de datos

El tamaño máximo de un registro debe tener menos de 50 000 caracteres según la medición de [`String.Length`](/dotnet/api/system.string.length). Si necesita desglosar los datos antes de enviarlos a la aptitud EntityLinking, puede usar la [aptitud División de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los nombres de los parámetros distinguen mayúsculas de minúsculas y son opcionales.

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| `defaultLanguageCode` |    Código de idioma del texto de entrada. Si no se especifica el código de idioma predeterminado, se usará el inglés (en) como código de idioma predeterminado. <br/> Vea [Full list of supported languages](../cognitive-services/text-analytics/language-support.md) (Lista completa de idiomas admitidos). |
| `minimumPrecision` | Un valor entre 0 y 1. Si la puntuación de confianza (en el resultado `entities`) es inferior a este valor, no se devuelve la entidad. El valor predeterminado es 0. |
| `modelVersion` | (Opcional) La versión del modelo que se va a usar al llamar al servicio de Text Analytics. Si no se especifica, el valor predeterminado será el más reciente disponible. Se recomienda no especificar este valor a menos que sea absolutamente necesario. Vea [Control de versiones de modelos en Text Analytics API](../cognitive-services/text-analytics/concepts/model-versioning.md) para obtener más información.|

## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de entrada      | Descripción                   |
|---------------|-------------------------------|
| `languageCode`    | Cadena que indica el idioma de los registros. Si no se especifica este parámetro, el código de idioma predeterminado se utilizará para analizar los registros. <br/>Vea [Full list of supported languages](../cognitive-services/text-analytics/language-support.md) (Lista completa de idiomas admitidos). |
| `text`          | Texto que se analizará.          |

## <a name="skill-outputs"></a>Salidas de la aptitud

| Nombre de salida      | Descripción                   |
|---------------|-------------------------------|
| `entities` | Una matriz de tipos complejos, que contiene los siguientes campos: <ul><li>name (nombre de la entidad real tal como aparece en el texto)</li> <li>id </li> <li>language (idioma del texto según lo establecido por la aptitud)</li> <li>url (la dirección URL vinculada a esta entidad)</li> <li>bingId (bingId para esta entidad vinculada)</li> <li>dataSource (el origen de datos asociado a la dirección URL) </li> <li>matches (matriz de tipos complejos que contiene: `text`, `offset`, `length` y `confidenceScore`)</li></ul>|

## <a name="sample-definition"></a>Definición de ejemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
    "context": "/document",
    "defaultLanguageCode": "en", 
    "minimumPrecision": 0.5, 
    "inputs": [
        {
            "name": "text", 
            "source": "/document/content"
        },
        {
            "name": "languageCode", 
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "entities", 
            "targetName": "entities" 
        }
    ]
}
```

## <a name="sample-input"></a>Entrada de ejemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft is liked by many.",
             "languageCode": "en"
           }
      }
    ]
}
```

## <a name="sample-output"></a>Salida de ejemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "entities": [
          {
            "name": "Microsoft", 
            "id": "Microsoft",
            "language": "en", 
            "url": "https://en.wikipedia.org/wiki/Microsoft", 
            "bingId": "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85", 
            "dataSource": "Wikipedia", 
            "matches": [
                {
                    "text": "Microsoft", 
                    "offset": 0, 
                    "length": 9, 
                    "confidenceScore": 0.13 
                }
            ]
          }
        ],
      }
    }
  ]
}
```

Tenga en cuenta que los desplazamientos devueltos para las entidades en la salida de esta aptitud se devuelven directamente desde la [API de Text Analytics](../cognitive-services/text-analytics/overview.md), lo que significa que si los usa para indexar en la cadena original, debe usar la clase [StringInfo](/dotnet/api/system.globalization.stringinfo) en .NET para extraer el contenido correcto.  [Se pueden encontrar más detalles aquí](../cognitive-services/text-analytics/concepts/text-offsets.md).

## <a name="warning-cases"></a>Casos de advertencia

Si el código de idioma del documento no se admite, se devuelve una advertencia y no se extrae ninguna entidad.

## <a name="see-also"></a>Consulte también

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)