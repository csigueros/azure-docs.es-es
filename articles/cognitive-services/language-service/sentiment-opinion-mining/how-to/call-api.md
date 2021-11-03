---
title: Realización del análisis de sentimiento y la minería de opiniones
titleSuffix: Azure Cognitive Services
description: En este artículo se muestra cómo detectar sentimientos y extraer opiniones en el texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: 17ac102653433585105e5a5d3dbe2216e9001cb3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093232"
---
# <a name="how-to-use-sentiment-analysis-and-opinion-mining"></a>Uso del análisis de sentimiento y la minería de opiniones 

El análisis de sentimiento y la minería de opiniones son dos maneras de detectar sentimientos positivos y negativos. Después de enviar una solicitud de análisis de sentimiento, puede obtener etiquetas de sentimiento (como "neutral" y "positivo") y puntuaciones de confianza a nivel de oración y de documento. La minería de opiniones proporciona información detallada sobre las opiniones relacionadas con las palabras (como los atributos de productos o servicios) en el texto.

> [!TIP]
> Si quiere empezar a usar esta característica, puede seguir el [artículo de inicio rápido](../quickstart.md). También puede realizar solicitudes de ejemplo mediante [Language Studio](../../language-studio.md) sin necesidad de escribir código.


## <a name="sentiment-analysis"></a>Análisis de sentimiento

El análisis de sentimiento aplica etiquetas de sentimiento al texto, que se devuelven a nivel de oración y de documento, con una puntuación de confianza para cada una de ellas. 

Las etiquetas son *positive*, *negative* y *neutral*. En el nivel de documento, también se puede devolver la etiqueta de opinión *mixed*. La opinión del documento se determina a continuación:

| Opiniones de frases                                                                            | Etiqueta de documento devuelta |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Hay al menos una oración `positive` en el documento. El resto de las oraciones son `neutral`. | `positive`              |
| Hay al menos una oración `negative` en el documento. El resto de las oraciones son `neutral`. | `negative`              |
| Hay al menos una oración `negative` y al menos una oración `positive` en el documento.    | `mixed`                 |
| Todas las oraciones del documento son `neutral`.                                                  | `neutral`               |

Las puntuaciones de confianza oscilan entre 1 y 0. Las puntuaciones más próximas a 1 indican una mayor confianza en la clasificación de la etiqueta, mientras que las puntuaciones inferiores indican una menor confianza. Para cada documento o frase, las puntuaciones previstas asociadas a las etiquetas (positivo, negativo y neutral) se suman hasta un máximo de 1. Para más información, consulte [Nota de transparencia de inteligencia artificial responsable](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="opinion-mining"></a>Minería de opiniones

La minería de opiniones es una característica de Análisis de sentimiento. Esta característica, también conocida como Análisis de sentimiento basada en aspectos en el procesamiento de lenguaje natural (NLP), proporciona información más detallada sobre las opiniones relacionadas con los atributos de los productos o servicios del texto. La API muestra las opiniones como un destino (con un sustantivo o verbo) y una evaluación (con un adjetivo).

Por ejemplo, si un cliente deja comentarios sobre un hotel, como "La habitación era fantástica, pero el personal era antipático", la minería de opiniones localizará destinos (aspectos) en el texto, así como sus evaluaciones (opiniones) y sentimientos asociados. Análisis de sentimiento solo puede informar de las opiniones negativas.

:::image type="content" source="../media/opinion-mining.png" alt-text="Diagrama del ejemplo de minería de opiniones" lightbox="../media/opinion-mining.png":::

Si va a usar la API REST, para obtener la minería de opiniones en los resultados, debe incluir la marca `opinionMining=true` en una solicitud de análisis de sentimiento. Los resultados de la minería de opiniones se incluirán en la respuesta del análisis de sentimiento. La minería de opiniones es una extensión de Análisis de sentimiento y se incluye en el [plan de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) actual.

## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-sentiment-analysis-model"></a>Especificación del modelo de análisis de sentimiento

De forma predeterminada, el análisis de sentimiento usará el modelo de IA disponible más reciente en el texto. También puede configurar las solicitudes de API para usar una versión específica del modelo. El modelo que especifique se usará para realizar operaciones de análisis de sentimiento.

| Versiones admitidas | Versión más reciente |
|--|--|
| `2019-10-01`, `2020-04-01`, `2021-10-01` | `2021-10-01`   |

### <a name="input-languages"></a>Idiomas de entrada

Al enviar documentos para que se procesen con el análisis de sentimiento, puede especificar los [idiomas admitidos](../language-support.md) en los que están escritos. Si no especifica el idioma, el análisis de sentimiento se establecerá de forma predeterminada en inglés. La API puede devolver desplazamientos en la respuesta para admitir diferentes [codificaciones multilingües y emojis](../../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envío de datos

El análisis de sentimiento y la minería de opiniones generan un resultado de mayor calidad cuando se les proporcionan fragmentos de texto más pequeños con los que trabajar. Sucede lo contrario con algunas características, como la extracción de frases clave, que funciona mejor en bloques de texto más grandes. 

Para enviar una solicitud de API, necesitará el punto de conexión y la clave del recurso de idioma.

> [!NOTE]
> Puede encontrar la clave y el punto de conexión de su recurso de idioma en Azure Portal. Los encontrará en la página **Key and endpoint** (Clave y punto de conexión) del recurso, en **Resource management** (Administración de recursos). 

El análisis se realiza tras la recepción de la solicitud. Para información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte a continuación la sección sobre los límites de datos.

El uso de las características de análisis de sentimiento y minería de opiniones de manera sincrónica no tiene estado. No se almacenan datos en la cuenta y los resultados se devuelven inmediatamente en la respuesta.

Cuando se usan las características de forma asincrónica, los resultados de la API están disponibles durante 24 horas desde el momento en que se ingiere la solicitud y se indican en la respuesta. Después de este período de tiempo, los resultados se purgan y ya no están disponibles para la recuperación.

## <a name="getting-sentiment-analysis-and-opinion-mining-results"></a>Obtención de resultados de análisis de sentimiento y minería de opiniones

Cuando se reciben resultados de la API, el modelo determina internamente el orden de las frases clave devueltas. Puede transmitir los resultados a una aplicación o guardar la salida en un archivo en el sistema local.

El análisis de sentimiento devuelve una etiqueta de sentimiento y una puntuación de confianza para todo el documento, y para cada oración del mismo. Las puntuaciones más próximas a 1 indican una mayor confianza en la clasificación de la etiqueta, mientras que las puntuaciones inferiores indican una menor confianza. Un documento puede tener varias oraciones y las puntuaciones de confianza de cada documento u oración suman 1.

La minería de opiniones buscará los destinos (sustantivos o verbos) en el texto y su evaluación asociada (adjetivos). Por ejemplo, la frase "*La comida del restaurante era buena y el camarero que nos atendió fue amable*" tiene dos destinos: *comida* y *camarero*. Cada destino tiene una evaluación. Por ejemplo, la evaluación de *comida* sería *buena* y la evaluación de *camarero* sería *amable*.

La API devuelve opiniones como un destino (sustantivo o verbo) y una evaluación (adjetivo).


## <a name="data-limits"></a>Límites de datos

> [!NOTE]
> * Si necesita analizar documentos con un tamaño superior al límite, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. 
> * Un documento es una sola cadena de caracteres de texto.  

| Límite | Value |
|------------------------|---------------|
| Tamaño máximo de un solo documento (sincrónico) | 5120 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Número máximo de caracteres por solicitud (asincrónico) | 125 000 caracteres entre todos los documentos enviados, medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Tamaño máximo de la solicitud completa | 1 MB. |
| Número máximo de documentos por solicitud | 10 |

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

## <a name="see-also"></a>Consulte también

* [Análisis de sentimiento y minería de opiniones](../overview.md)
