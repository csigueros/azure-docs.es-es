---
title: Aptitud cognitiva para la detección de información de identificación personal
titleSuffix: Azure Cognitive Search
description: Extraiga y enmascare información personal de un texto en una canalización de enriquecimiento en Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 71bd45fae729efed6d76ab65fc4ea45944998f45
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862696"
---
# <a name="pii-detection-cognitive-skill"></a>Aptitud cognitiva para la detección de información de identificación personal

La aptitud para la **detección de información de identificación personal** extrae información personal de un texto de entrada y le ofrece la opción de enmascararla. Esta aptitud utiliza los modelos de aprendizaje automático proporcionados por [Text Analytics](../cognitive-services/text-analytics/overview.md) en Cognitive Services.

> [!NOTE]
> Esta aptitud está enlazada a Cognitive Services y necesita [un recurso facturable](cognitive-search-attach-cognitive-services.md) para las transacciones que superan los 20 documentos por indexador al día. La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes.
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Límites de datos

El tamaño máximo de un registro debe tener menos de 50 000 caracteres según la medición de [`String.Length`](/dotnet/api/system.string.length). Si necesita dividir los datos antes de enviarlos a la aptitud, puede usar la [aptitud División de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas y son opcionales.

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| `defaultLanguageCode` | (Opcional) Es el código de idioma que se aplicará a los documentos que no especifiquen el lenguaje de forma explícita.  Si no se especifica el código de idioma predeterminado, se usará el inglés (en) como código de idioma predeterminado. <br/> Vea [Full list of supported languages](../cognitive-services/text-analytics/language-support.md?tabs=pii) (Lista completa de idiomas admitidos). |
| `minimumPrecision` | Un valor entre 0,0 y 1,0. Si la puntuación de confianza (en la salida `piiEntities`) es inferior al valor `minimumPrecision` establecido, la entidad no se devuelve ni se enmascara. El valor predeterminado es 0,0. |
| `maskingMode` | Un parámetro que proporciona varias formas de enmascarar la información personal detectada en el texto especificado. Se admiten las siguientes opciones: <ul><li>`none` (predeterminado): no se produce enmascaramiento y no se devolverá la salida `maskedText`. </li><li> `replace`: reemplaza las entidades detectadas por el carácter que se especifica en el parámetro `maskingCharacter`. El carácter se repetirá hasta completar la longitud de la entidad detectada, con el fin de que los desplazamientos se correspondan correctamente tanto con el texto introducido como la salida `maskedText`.</li></ul> <br/> Durante la versión preliminar de PIIDetectionSkill, también se admitía la opción `maskingMode` `redact`, que permitía quitar completamente las entidades detectadas sin reemplazo. La opción `redact` ha quedado en desuso y ya no se admite en la aptitud. |
| `maskingCharacter` | El carácter utilizado para enmascarar el texto si el parámetro `maskingMode` está establecido en `replace`. Se admite la siguiente opción: `*` (predeterminada). Este parámetro solo puede ser `null` si `maskingMode` no está establecido en `replace`. <br/><br/> Durante la versión preliminar de PIIDetectionSkill, se admitían las opciones `maskingCharacter` adicionales `X` y `#`. Las opciones `X` y `#` han quedado en desuso y ya no se admiten en la aptitud. |
| `domain`   | (Opcional) Un valor de cadena, si se especifica, establecerá el dominio PII para incluir solo un subconjunto de las categorías de entidad. Entre los valores posibles se incluyen: `phi` (solo se detecta información de mantenimiento confidencial), o `none`. |
| `piiCategories`   | (Opcional) Si quiere especificar qué entidades se detectarán y devolverán, use el parámetro opcional `piiCategories` (se define como una lista de cadenas) con las categorías de entidad adecuadas. Este parámetro también puede permitirle detectar entidades que no están habilitadas de forma predeterminada para el lenguaje del documento. Consulte [la documentación de TextAnalytics](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) para obtener una lista de categorías disponibles.  |
| `modelVersion`   | (Opcional) La versión del modelo que se va a usar al llamar al servicio de Text Analytics. Si no se especifica, el valor predeterminado será la versión más reciente disponible. Se recomienda no especificar este valor a menos que sea absolutamente necesario. Vea [Control de versiones de modelos en Text Analytics API](../cognitive-services/text-analytics/concepts/model-versioning.md) para obtener más información. |

## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de entrada      | Descripción                   |
|---------------|-------------------------------|
| `languageCode`    | Cadena que indica el idioma de los registros. Si no se especifica este parámetro, el código de idioma predeterminado se utilizará para analizar los registros. <br/>Vea [Full list of supported languages](../cognitive-services/text-analytics/language-support.md?tabs=pii) (Lista completa de idiomas admitidos).  |
| `text`          | Texto que se analizará.          |

## <a name="skill-outputs"></a>Salidas de la aptitud

| Nombre de salida      | Descripción                   |
|---------------|-------------------------------|
| `piiEntities` | Una matriz de tipos complejos, que contiene los siguientes campos: <ul><li>text (la información de identificación personal real que se ha extraído)</li> <li>type</li><li>subType</li><li>score (cuanto más alto sea el valor, más probable será que la entidad sea real)</li><li>offset (en el texto introducido)</li><li>length</li></ul> </br> [Aquí se pueden encontrar los valores posibles de type y subType.](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | Si `maskingMode` se establece en un valor distinto de `none`, esta salida será el resultado de la cadena del enmascaramiento realizado en el texto introducido, como se describe en el `maskingMode`seleccionado.  Si `maskingMode` se establece en `none`, esta salida no estará presente. |

## <a name="sample-definition"></a>Definición de ejemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
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
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
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
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Los desplazamientos devueltos para las entidades en la salida de esta aptitud se devuelven directamente desde la [API de Text Analytics](../cognitive-services/text-analytics/overview.md), lo que significa que si los usa para indexar en la cadena original, debe usar la clase [StringInfo](/dotnet/api/system.globalization.stringinfo) en .NET para extraer el contenido correcto.  [Se pueden encontrar más detalles aquí](../cognitive-services/text-analytics/concepts/text-offsets.md).

## <a name="errors-and-warnings"></a>Errores y advertencias

Si el código de idioma del documento no se admite, se devuelve una advertencia y no se extrae ninguna entidad.
Si el texto está vacío, se devuelve una advertencia.
Si el texto tiene más de 50 000 caracteres, solo se analizarán los primeros 50 000 caracteres y se emitirá una advertencia.

Si la aptitud devuelve una advertencia, el valor `maskedText` de salida puede estar vacío, lo que podría afectar a las aptitudes de nivel inferior que esperasen la salida. Por esta razón, asegúrese de investigar todas las advertencias relacionadas con una salida que falta al escribir la definición del conjunto de aptitudes.

## <a name="see-also"></a>Consulte también

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)