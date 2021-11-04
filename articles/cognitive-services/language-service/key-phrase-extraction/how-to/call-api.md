---
title: cómo llamar a la API Extracción de frases clave
titleSuffix: Azure Cognitive Services
description: Cómo extraer frases clave mediante la característica de la API Extracción de frases clave.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
ms.openlocfilehash: 72a7d077b5ee7752f7b91a2f5db5e1b5614c4ddd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091640"
---
# <a name="how-to-use-key-phrase-extraction"></a>Cómo usar la extracción de frases clave 

La característica de extracción de frases clave puede evaluar el texto no estructurado y devolver una lista de frases clave para cada documento.

Esta característica es útil si necesita identificar rápidamente los puntos principales en una colección de documentos. Por ejemplo, si el texto de entrada es "*La comida estaba deliciosa y el personal era maravilloso*", el servicio devuelve los temas principales: "*comida*" y "*personal maravilloso*".

> [!TIP]
> Si quiere empezar a usar esta característica, puede seguir el [artículo de inicio rápido](../quickstart.md). También puede realizar solicitudes de ejemplo mediante [Language Studio](../../language-studio.md) sin necesidad de escribir código.


## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-key-phrase-extraction-model"></a>Especificación del modelo de extracción de frases clave

De manera predeterminada, la extracción de frases clave usará el modelo de IA más reciente disponible en el texto. También puede configurar las solicitudes de API para usar una versión específica del modelo. El modelo que especifique se usará para realizar operaciones de extracción de frases clave.

| Versiones admitidas | Versión más reciente |
|--|--|
| `2019-10-01`, `2020-07-01`, `2021-06-01`  | `2021-06-01`   |

### <a name="input-languages"></a>Idiomas de entrada

Al enviar documentos para que se procesen con la extracción de frases clave, puede especificar [los idiomas admitidos](../language-support.md) en los que están escritos. Si no especifica el idioma, la extracción de frases clave se establecerá de forma predeterminada en inglés. La API puede devolver desplazamientos en la respuesta para admitir diferentes [codificaciones multilingües y emojis](../../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envío de datos

La extracción de frases clave funciona mejor cuando se proporcionan cantidades más grandes de texto en los que trabajar. Es lo contrario del análisis de opiniones, que funciona mejor con cantidades de texto más pequeñas. Para obtener los mejores resultados de ambas operaciones, considere la posibilidad de reestructurar las entradas en consecuencia.

Para enviar una solicitud de API, necesitará el punto de conexión y la clave del recurso de idioma.

> [!NOTE]
> Puede encontrar la clave y el punto de conexión de su recurso de idioma en Azure Portal. Los encontrará en la página **Key and endpoint** (Clave y punto de conexión) del recurso, en **Resource management** (Administración de recursos). 

El análisis se realiza tras la recepción de la solicitud. Para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte los límites de datos a continuación.

El uso de la característica de extracción de frases clave de forma sincrónica no tiene estado. No se almacenan datos en la cuenta y los resultados se devuelven inmediatamente en la respuesta.

Cuando se usa esta característica de forma asincrónica, los resultados de la API están disponibles durante 24 horas desde el momento en que se ingiere la solicitud y se indican en la respuesta. Después de este período de tiempo, los resultados se purgan y ya no están disponibles para la recuperación.


## <a name="getting-key-phrase-extraction-results"></a>Obtención de resultados de la extracción de frases clave

Cuando se reciben resultados de la API, el modelo determina internamente el orden de las frases clave devueltas. Puede transmitir los resultados a una aplicación o guardar la salida en un archivo en el sistema local.

## <a name="data-limits"></a>Límites de datos

> [!NOTE]
> * Si necesita analizar documentos con un tamaño superior al límite, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. 
> * Un documento es una sola cadena de caracteres de texto.  

| Límite | Value | 
|------------------------|---------------|
| Tamaño máximo de un solo documento (sincrónico) | 5120 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Número máximo de caracteres por solicitud (asincrónico) | 125 000 caracteres entre todos los documentos enviados, medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Tamaño máximo de la solicitud completa | 1 MB.  |
| Número máximo de documentos por solicitud |  10 |

Si un documento supera el límite de caracteres, la API se comportará de forma diferente en función del punto de conexión que esté usando:

* Asincrónico: la API rechazará toda la solicitud y devolverá un error `400 bad request` si algún documento de la misma supera el tamaño máximo.
* Sincrónico: la API no procesará un documento que supere el tamaño máximo y devolverá un error de documento no válido. Si una solicitud de API tiene varios documentos, la API seguirá procesándolos si están dentro del límite de caracteres.

### <a name="rate-limits"></a>Límites de frecuencia

El límite de velocidad variará en función del [plan de tarifa](https://aka.ms/unifiedLanguagePricing).

| Nivel          | Solicitudes por segundo | Solicitudes por minuto |
|---------------|---------------------|---------------------|
| S / Varios servicios | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="next-steps"></a>Pasos siguientes

[Información general de extracción de frases clave](../overview.md)
