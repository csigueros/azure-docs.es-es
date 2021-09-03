---
title: Aptitud cognitiva de reconocimiento de entidades (V3)
titleSuffix: Azure Cognitive Search
description: Extraiga diferentes tipos de entidades del texto con Text Analytics (V3) en una canalización de enriquecimiento en Azure Cognitive Search.
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 14811591feea9df735bf41e23a81e3a96faa2662
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862670"
---
# <a name="entity-recognition-cognitive-skill-v3"></a>Aptitud cognitiva de reconocimiento de entidades (V3)

La aptitud **Reconocimiento de entidades** extrae entidades de distintos tipos del texto. Estas entidades se incluyen en 14 categorías distintas, desde personas y organizaciones hasta direcciones URL y números de teléfono. Esta aptitud utiliza los modelos de aprendizaje automático proporcionados por [Text Analytics](../cognitive-services/text-analytics/overview.md) en Cognitive Services.

> [!NOTE]
> Esta aptitud está enlazada a Cognitive Services y necesita [un recurso facturable](cognitive-search-attach-cognitive-services.md) para las transacciones que superan los 20 documentos por indizador al día. La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.EntityRecognitionSkill

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe tener menos de 50 000 caracteres según la medición de [`String.Length`](/dotnet/api/system.string.length). Si necesita desglosar los datos antes de enviarlos a la aptitud EntityRecognition, puede usar la [aptitud División de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas y son opcionales.

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| `categories`    | Matriz de categorías que se deben extraer.  Tipos de categorías posibles: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"DateTime"`, `"URL"`, `"Email"`, `"PersonType"`, `"Event"`, `"Product"`, `"Skill"`, `"Address"`, `"Phone Number"` y `"IP Address"`. Si no se proporciona ninguna categoría, se devuelven todos los tipos.|
| `defaultLanguageCode` |    Código de idioma del texto de entrada. Si no se especifica el código de idioma predeterminado, se usará el inglés (en) como código de idioma predeterminado. <br/> Vea [Full list of supported languages](../cognitive-services/text-analytics/language-support.md) (Lista completa de idiomas admitidos). No todas las categorías de entidad son compatibles con todos los idiomas. Consulte la nota que aparece a continuación.|
| `minimumPrecision` | Un valor entre 0 y 1. Si la puntuación de confianza (en el resultado `namedEntities`) es inferior a este valor, no se devuelve la entidad. El valor predeterminado es 0. |
| `modelVersion` | (Opcional) La versión del modelo que se va a usar al llamar al servicio de Text Analytics. Si no se especifica, el valor predeterminado será el más reciente disponible. Se recomienda no especificar este valor a menos que sea absolutamente necesario. Vea [Control de versiones de modelos en Text Analytics API](../cognitive-services/text-analytics/concepts/model-versioning.md) para obtener más información.|


## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de entrada      | Descripción                   |
|---------------|-------------------------------|
| `languageCode`    | Cadena que indica el idioma de los registros. Si no se especifica este parámetro, el código de idioma predeterminado se utilizará para analizar los registros. <br/>Vea [Full list of supported languages](../cognitive-services/text-analytics/language-support.md) (Lista completa de idiomas admitidos). |
| `text`          | Texto que se analizará.          |

## <a name="skill-outputs"></a>Salidas de la aptitud

> [!NOTE]
>No todas las categorías de entidad son compatibles con todos los idiomas. Consulte [Categorías de entidad admitidas en Text Analytics API v3](../cognitive-services/text-analytics/named-entity-types.md) para saber qué categorías de entidad se admiten para el lenguaje que va a usar.

| Nombre de salida      | Descripción                   |
|---------------|-------------------------------|
| `persons`       | Una matriz de cadenas donde cada cadena representa el nombre de una persona. |
| `locations`  | Una matriz de cadenas donde cada cadena representa una ubicación. |
| `organizations`  | Una matriz de cadenas donde cada cadena representa una organización. |
| `quantities`  | Una matriz de cadenas donde cada cadena representa una cantidad. |
| `dateTimes`  | Una matriz de cadenas donde cada cadena representa un valor de fecha y hora (como aparece en el texto). |
| `urls` | Una matriz de cadenas donde cada cadena representa una dirección URL. |
| `emails` | Una matriz de cadenas donde cada cadena representa un correo electrónico. |
| `personTypes` | Matriz de cadenas donde cada cadena representa un valor PersonType |
| `events` | Matriz de cadenas donde cada cadena representa un evento |
| `products` | Matriz de cadenas donde cada cadena representa un producto |
| `skills` | Matriz de cadenas donde cada cadena representa una aptitud |
| `addresses` | Matriz de cadenas donde cada cadena representa una dirección |
| `phoneNumbers` | Matriz de cadenas donde cada cadena representa un número de teléfono |
| `ipAddresses` | Matriz de cadenas donde cada cadena representa una dirección IP |
| `namedEntities` | Una matriz de tipos complejos, que contiene los siguientes campos: <ul><li>category</li> <li>subcategory</li> <li>confidenceScore (cuanto más alto sea el valor, más real será una entidad)</li> <li>length (longitud [número de caracteres] de esta entidad)</li> <li>desplazamiento (la ubicación donde se encontró en el texto)</li> <li>text (nombre de la entidad real tal como aparece en el texto)</li></ul> |


##    <a name="sample-definition"></a>Definición de ejemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Person", "Email"],
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
            "name": "persons", 
            "targetName": "people"
        },
        {
            "name": "emails", 
            "targetName": "emails"
        },
        {
            "name": "namedEntities", 
            "targetName": "namedEntities"
        }
    ]
  }
```
##    <a name="sample-input"></a>Entrada de ejemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso Corporation was founded by Jean Martin. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Salida de ejemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "people": [ "Jean Martin"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category": "Person",
            "subcategory": null,
            "length": 11,
            "offset": 35,
            "confidenceScore": 0.98,
            "text": "Jean Martin"
          },
          {
            "category": "Email",
            "subcategory": null,
            "length": 19,
            "offset": 71,
            "confidenceScore": 0.8,
            "text": "contact@contoso.com"
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