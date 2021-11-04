---
title: Resumen de texto con la API de resumen extractivo
titleSuffix: Azure Cognitive Services
description: En este artículo se muestra cómo resumir texto con la API de resumen extractivo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-summarization, ignite-fall-2021
ms.openlocfilehash: 21be719780b4633456ceea89c0cb6c6295480348
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093637"
---
# <a name="how-to-use-text-summarization-preview"></a>Uso del resumen de texto (versión preliminar)

> [!IMPORTANT] 
> La característica de resumen extractivo es una funcionalidad en versión preliminar que se proporciona "tal cual" y "con todos los errores". Por lo tanto, el resumen extractivo (versión preliminar) no debe implementarse en ningún uso de producción. El cliente es el único responsable de cualquier uso del resumen extractivo. 

En general, hay dos enfoques de resumen automático de texto: extractivo y abstractivo. Esta API proporciona resúmenes extractivos.

El resumen extractivo es una característica que genera un resumen mediante la extracción de frases que representan colectivamente la información más importante o pertinente dentro del contenido original.

Esta característica está diseñada para abreviar el contenido que los usuarios consideran demasiado largo para leer. El resumen extractivo condensa artículos, trabajos o documentos en oraciones clave.

El servicio proporciona los modelos de AI que usa la API, el usuario solo tiene que enviar contenido para su análisis.

## <a name="features"></a>Características

> [!TIP]
> Si quiere empezar a usar esta característica, puede seguir el [artículo de inicio rápido](../quickstart.md). También puede realizar solicitudes de ejemplo mediante [Language Studio](../../language-studio.md) sin necesidad de escribir código.

La API de resumen extractivo usa técnicas de procesamiento de lenguaje natural para buscar oraciones clave en un documento de texto no estructurado. Estas oraciones transmiten colectivamente la idea principal del documento.

El resumen extractivo devuelve una puntuación de clasificación como parte de la respuesta del sistema, junto con las oraciones extraídas y su posición en los documentos originales. Una puntuación de clasificación es un indicador de lo pertinente que se determina que es una oración para la idea principal de un documento. El modelo proporciona una puntuación de entre 0 y 1 (ambos inclusive) a cada oración y devuelve las oraciones con mayor puntuación por solicitud. Por ejemplo, si solicita un resumen de tres oraciones, el servicio devuelve las tres oraciones con mayor puntuación.

Hay otra característica en Azure Cognitive Service for Language, [la extracción de frases clave](./../../key-phrase-extraction/how-to/call-api.md), con la que es posible extraer información. A la hora de decidir si usar la extracción de frases clave o el resumen extractivo, tenga presente lo siguiente:
* La extracción de frases clave devuelve frases, mientras que el resumen extractivo devuelve oraciones.
* El resumen extractivo devuelve oraciones junto con una puntuación de clasificación. Se devolverán las oraciones con mayor puntuación.

## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-text-summarization-model"></a>Especificación del modelo de resumen de texto

De forma predeterminada, el resumen de texto usará el modelo de IA más reciente disponible en el texto. También puede configurar las solicitudes de API para usar una versión específica del modelo. El modelo que especifique se usará para realizar operaciones de resumen de texto.

| Versiones admitidas | Versión más reciente |
|--|--|
| `2021-08-01` | `2021-08-01` |

### <a name="input-languages"></a>Idiomas de entrada

Al enviar documentos para que se procesen con la extracción de frases clave, puede especificar [los idiomas admitidos](../language-support.md) en los que están escritos. Si no especifica el idioma, la extracción de frases clave se establecerá de forma predeterminada en inglés. La API puede devolver desplazamientos en la respuesta para admitir diferentes [codificaciones multilingües y emojis](../../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envío de datos

Los documentos se envían a la API como cadenas de texto. El análisis se realiza tras la recepción de la solicitud. Dado que la API es asincrónica, puede haber un retraso entre el envío de una solicitud de API y la recepción de los resultados.  Para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte los límites de datos a continuación.

Cuando se usa esta característica, los resultados de la API están disponibles durante 24 horas desde el momento en que se ingiere la solicitud y se indican en la respuesta. Después de este período de tiempo, los resultados se purgan y ya no están disponibles para la recuperación.

Puede usar el parámetro `sentenceCount` para especificar cuántas oraciones se devolverán, siendo `3` el valor predeterminado. El rango es de 1 a 20.

También puede usar el parámetro `sortby` para especificar en qué orden se devolverán las oraciones extraídas: `Offset` o `Rank`, siendo `Offset` el valor predeterminado. 


|valor del parámetro  |Descripción  |
|---------|---------|
|Rango    | Ordena las oraciones según su pertinencia para el documento de entrada, según lo decida el servicio.        |
|Offset    | Mantiene el orden original en el que aparecen las oraciones en el documento de entrada.        |

## <a name="getting-text-summarization-results"></a>Obtención de resultados de resumen de texto

Cuando obtiene los resultados de la detección de idioma, puede transmitir los resultados a una aplicación o guardar la salida en un archivo en el sistema local.

A continuación se muestra un ejemplo de contenido que se podrá enviar para la obtención de un resumen, que se extrae del artículo de blog [A holistic representation toward integrative AI](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/), redactado en inglés. Este artículo es solo un ejemplo, la API puede aceptar texto de entrada mucho más largo. Consulte la sección de límites de datos para obtener más información.
 
*"At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."*

La API de resumen extractivo se aplica tras la recepción de la solicitud mediante la creación de un trabajo para el back-end de la API. Si el trabajo se completa correctamente, se devolverá la salida de la API. La salida estará disponible para su recuperación durante 24 horas. Después de este tiempo, se purgará. Debido a la compatibilidad con varios idiomas y con Emojis, la respuesta puede contener desplazamientos de texto. Consulte [cómo procesar desplazamientos](../../concepts/multilingual-emoji-support.md) para más información.

Con el ejemplo anterior, la API podría devolver las siguientes oraciones resumidas:

*"At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding."*

*"In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z)."*

*"At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better."*


## <a name="data-limits"></a>Límites de datos

En esta sección se describen los límites de tamaño y la velocidad a la que se puede enviar datos a la API de resumen de texto. Tenga en cuenta que los límites de datos o de velocidad no afectan al precio. Los precios están sujetos a los [detalles de precios](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) del recurso de lenguaje.

> [!NOTE]
> * Si necesita analizar documentos con un tamaño superior al límite, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. 
> * Un documento es una sola cadena de caracteres de texto.  

| Límite | Value |
|------------------------|---------------|
| Número máximo de caracteres por solicitud  | 125 000 caracteres entre todos los documentos enviados, medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Número máximo de documentos por solicitud | 25 |

Si el documento supera los límites, la API rechazará toda la solicitud y devolverá un error `400 bad request` si algún documento de esta supera el tamaño máximo.

### <a name="rate-limits"></a>Límites de frecuencia

El límite de velocidad variará en función del [plan de tarifa](https://aka.ms/unifiedLanguagePricing).

| Nivel          | Solicitudes por segundo | Solicitudes por minuto |
|---------------|---------------------|---------------------|
| S / Varios servicios | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>Consulte también

* [Introducción al resumen de texto](../overview.md)
