---
title: Cómo realizar la detección de idioma
titleSuffix: Azure Cognitive Services
description: En este artículo se muestra cómo detectar el idioma del texto escrito mediante la detección de idioma.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 55584c58ebba3faf3d9517cf65827fd784d331a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092809"
---
# <a name="how-to-use-language-detection"></a>Uso de la detección de idioma

La característica de la Detección de idioma puede evaluar texto y devolver un identificador de idioma que indica el idioma en el que se escribió un documento.

La detección de idioma es útil para las tiendas de contenido que recopilan texto arbitrario, donde se desconoce el idioma. Puede analizar los resultados del análisis para determinar el idioma que se usa en el documento de entrada. La respuesta también devuelve una puntuación entre 0 y 1 que refleja la confianza del modelo.

La característica Detección de idioma puede detectar una amplia gama de idiomas, variantes, dialectos y algunos idiomas regionales o culturales.

> [!TIP]
> Si quiere empezar a usar esta característica, puede seguir el [artículo de inicio rápido](../quickstart.md). También puede realizar solicitudes de ejemplo mediante [Language Studio](../../language-studio.md) sin necesidad de escribir código.

## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-language-detection-model"></a>Especificación del modelo de detección de idioma

De manera predeterminada, la detección de idioma usará el modelo de IA más reciente disponible en el texto. También puede configurar las solicitudes de API para usar una versión específica del modelo. El modelo que especifique se usará para realizar operaciones de detección de idioma.

| Versiones admitidas | Versión más reciente |
|--|--|
|  `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |

### <a name="input-languages"></a>Idiomas de entrada

Al enviar documentos que se van a evaluar, la detección de idioma intentará determinar si el texto se escribió en cualquiera de [los idiomas admitidos](../language-support.md).  

Si tiene contenido que se expresa en un idioma que se usa con menos frecuencia, puede probar la característica Detección de idioma para ver si devuelve un código. La respuesta para los idiomas que no se pueden detectar es `unknown`.

## <a name="submitting-data"></a>Envío de datos

> [!TIP]
> Puede usar un [contenedor de Docker](use-containers.md) para la detección de idioma, por lo que puede usar la API local.

El análisis se realiza tras la recepción de la solicitud. Para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte los límites de datos a continuación.

El uso de la característica de detección de idioma forma asincrónica no tiene estado. No se almacenan datos en la cuenta y los resultados se devuelven inmediatamente en la respuesta.

Cuando se usa esta característica de forma asincrónica, los resultados de la API están disponibles durante 24 horas desde el momento en que se ingiere la solicitud y se indican en la respuesta. Después de este período de tiempo, los resultados se purgan y ya no están disponibles para la recuperación.


## <a name="getting-language-detection-results"></a>Obtención de los resultados de la detección de idioma

Cuando obtiene los resultados de la detección de idioma, puede transmitir los resultados a una aplicación o guardar la salida en un archivo en el sistema local.

La detección de idioma devolverá un idioma predominante para cada documento que envíe, junto con su nombre [ISO 639-1](https://www.iso.org/standard/22109.html), un nombre legible y una puntuación de confianza. Una puntuación positiva de 1 indica el mayor nivel de confianza posible del análisis.

### <a name="ambiguous-content"></a>Contenido ambiguo

En algunos casos, puede ser difícil eliminar la ambigüedad de los idiomas en función de la entrada. Puede usar el parámetro `countryHint` para especificar un código de país o región [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). De forma predeterminada, la API usa "US" como sugerencia de país predeterminada. Para quitar este comportamiento, puede restablecer este parámetro estableciendo este valor en una cadena `countryHint = ""` vacía.

Por ejemplo, "impossible" es igual en inglés que en francés, y si se proporciona con un contexto limitado, la respuesta se basará en la sugerencia de país o región de "US". Si se sabe que el origen del texto procede de Francia, eso se puede proporcionar como sugerencia.

**Entrada**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "impossible"
        },
        {
            "id": "2",
            "text": "impossible",
            "countryHint": "fr"
        }
    ]
}
```

El modelo de detección de idioma ahora tiene contexto adicional para hacer un mejor criterio: 

**Salida**

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

Si el analizador no puede analizar la entrada, devuelve `(Unknown)`. Un ejemplo es si envía una cadena de texto que consta únicamente de números.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": 0.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

### <a name="mixed-language-content"></a>Contenido en varios idiomas

El contenido en varios idiomas dentro del mismo documento devuelve el idioma con mayor representación en el contenido, pero con una clasificación positiva inferior. La clasificación refleja la fuerza marginal de la evaluación. En el ejemplo siguiente, la entrada es una combinación de inglés, español y francés. El analizador cuenta los caracteres de cada segmento para determinar el idioma predominante.

**Entrada**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
    ]
}
```

**Salida**

La salida resultante está formada por el idioma predominante, con una puntuación de menos de 1,0, que indica un nivel de confianza más débil.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 0.88
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

## <a name="data-limits"></a>Límites de datos

> [!NOTE]
> * Si necesita analizar documentos con un tamaño superior al límite, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. 
> * Un documento es una sola cadena de caracteres de texto.  

| Límite | Value |
|------------------------|---------------|
| Tamaño máximo de un solo documento (sincrónico) | 5120 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Número máximo de caracteres por solicitud (asincrónico)  | 125 000 caracteres entre todos los documentos enviados, medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Tamaño máximo de la solicitud completa | 1 MB.  |
| Número máximo de documentos por solicitud | 1000 |

Si un documento supera el límite de caracteres, la API se comportará de forma diferente en función del punto de conexión que esté usando:

* Asincrónico: la API rechazará toda la solicitud y devolverá un error `400 bad request` si algún documento de la misma supera el tamaño máximo.
* Sincrónico: la API no procesará un documento que supere el tamaño máximo y devolverá un error de documento no válido. Si una solicitud de API tiene varios documentos, la API seguirá procesándolos si están dentro del límite de caracteres.

### <a name="rate-limits"></a>Límites de frecuencia

El límite de velocidad variará en función del [plan de tarifa](https://aka.ms/unifiedLanguagePricing).

| Nivel          | Solicitudes por segundo | Solicitudes por minuto |
|---------------|---------------------|---------------------|
| S / Varios servicios | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>Consulte también

* [Introducción a la detección de idiomas](../overview.md)
