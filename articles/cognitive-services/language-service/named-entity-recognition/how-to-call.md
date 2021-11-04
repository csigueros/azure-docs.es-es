---
title: Procedimiento para realizar Reconocimiento de entidades con nombre (NER)
titleSuffix: Azure Cognitive Services
description: En este artículo se muestra cómo extraer entidades con nombre del texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: cd6901e4ac1650f6739d24697fab5abafe8409a5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093554"
---
# <a name="how-to-use-named-entity-recognitionner"></a>Procedimiento para usar Reconocimiento de entidades con nombre (NER)

La característica NER puede evaluar texto no estructurado y extraer entidades con nombre del texto en varias categorías predefinidas, por ejemplo: persona, ubicación, evento, producto y organización.  

## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-ner-model"></a>Especificación del modelo de NER

De manera predeterminada, esta característica usará el modelo de IA disponible más reciente en el texto. También puede configurar las solicitudes de API para usar una versión específica del modelo. El modelo que especifique se usará para realizar operaciones de NER.

| Versiones admitidas | Versión más reciente |
|--|--|
| `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`,`2021-06-01`  | `2021-06-01`   |


### <a name="input-languages"></a>Idiomas de entrada

Al enviar documentos para que se procesen, puede especificar en cuál de los [idiomas admitidos](language-support.md) están escritos. Si no especifica el idioma, la extracción de frases clave se establecerá de forma predeterminada en inglés. La API puede devolver desplazamientos en la respuesta para admitir diferentes [codificaciones multilingües y emojis](../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envío de datos

El análisis se realiza tras la recepción de la solicitud. Para información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte a continuación la sección sobre los límites de datos.

El uso de la característica NER de forma sincrónica no tiene estado. No se almacenan datos en la cuenta y los resultados se devuelven inmediatamente en la respuesta.

Cuando se usan estas características de forma asincrónica, los resultados de la API están disponibles durante 24 horas desde el momento en que se ingiere la solicitud y se indica en la respuesta. Después de este período de tiempo, los resultados se purgan y ya no están disponibles para la recuperación.

La API intentará detectar las [categorías de entidad definidas](concepts/named-entity-categories.md) para un idioma de documento determinado. 

## <a name="getting-ner-results"></a>Obtención de los resultados de NER

Cuando obtiene los resultados de NER, puede transmitirlos a una aplicación, o bien guardar la salida en un archivo en el sistema local. La respuesta de la API contendrá [entidades reconocidas](concepts/named-entity-categories.md), incluidas sus categorías y subcategorías, y las puntuaciones de confianza. 

## <a name="data-limits"></a>Límites de datos

> [!NOTE]
> * Si necesita analizar documentos con un tamaño superior al límite, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. 
> * Un documento es una sola cadena de caracteres de texto.  

| Límite | Value |
|------------------------|---------------|
| Tamaño máximo de un solo documento (sincrónico) | 5120 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Número máximo de caracteres por solicitud (asincrónico)  | 125 000 caracteres entre todos los documentos enviados, medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Tamaño máximo de la solicitud completa | 1 MB. |
| Número máximo de documentos por solicitud | 5 |

Si un documento supera el límite de caracteres, la API se comportará de forma diferente en función del punto de conexión que use:

* Asincrónico:
  * La API rechazará toda la solicitud y devolverá un error `400 bad request` si algún documento de la misma supera el tamaño máximo.
* Sincrónico:  
  * La API no procesará un documento que supere el tamaño máximo y devolverá un error de documento no válido. Si una solicitud de API tiene varios documentos, la API seguirá procesándolos si están dentro del límite de caracteres.

Si se supera el número máximo de documentos que se pueden enviar en una única solicitud, se generará un código de error HTTP 400.

### <a name="rate-limits"></a>Límites de frecuencia

El límite de velocidad variará en función del [plan de tarifa](https://aka.ms/unifiedLanguagePricing).

| Nivel          | Solicitudes por segundo | Solicitudes por minuto |
|---------------|---------------------|---------------------|
| S / Varios servicios | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="next-steps"></a>Pasos siguientes

[Introducción al reconocimiento de entidades con nombre](overview.md)
