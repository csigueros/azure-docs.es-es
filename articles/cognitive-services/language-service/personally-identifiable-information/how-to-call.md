---
title: Procedimiento para detectar información de identificación personal (DCP)
titleSuffix: Azure Cognitive Services
description: En este artículo se muestra cómo extraer información de identificación personal y sanitaria (PHI) del texto y detectar información identificable.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 93f232233da0310881539441fdb296ac4ae39b62
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093355"
---
# <a name="how-to-detect-and-redact-personally-identifying-information-pii"></a>Procedimiento para detectar y censurar información de identificación personal (DCP)

La característica DCP puede evaluar texto no estructurado, extraer información confidencial (PII) e información sanitaria (PHI) en texto en varias categorías predefinidas.

## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-pii-detection-model"></a>Especificación del modelo de detección de DCP

De manera predeterminada, esta característica usará el modelo de IA disponible más reciente en el texto. También puede configurar las solicitudes de API para usar una versión específica del modelo. El modelo que especifique se usará para realizar operaciones de NER y DCP.

| Versiones admitidas | Versión más reciente |
|--|--|
| `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |

### <a name="input-languages"></a>Idiomas de entrada

Al enviar documentos para que se procesen, puede especificar en cuál de los [idiomas admitidos](language-support.md) están escritos. Si no especifica un idioma, la extracción de frases clave se establecerá de forma predeterminada en inglés. La API puede devolver desplazamientos en la respuesta para admitir diferentes [codificaciones multilingües y emojis](../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envío de datos

El análisis se realiza tras la recepción de la solicitud. Para información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte a continuación la sección sobre los límites de datos.

El uso de la característica de detección de DCP de forma asincrónica no tiene estado. No se almacenan datos en la cuenta y los resultados se devuelven inmediatamente en la respuesta.

Cuando se usan estas características de forma asincrónica, los resultados de la API están disponibles durante 48 horas desde el momento en que se ingiere la solicitud y se indica en la respuesta. Después de este período de tiempo, los resultados se purgan y ya no están disponibles para la recuperación.

La API intentará detectar las [categorías de entidad definidas](concepts/entity-categories.md) para un idioma de documento determinado. Si desea especificar qué entidades se detectarán y devolverán, use el parámetro opcional `piiCategories` con las categorías de entidad adecuadas. Este parámetro también puede permitirle detectar entidades que no están habilitadas de forma predeterminada para el lenguaje del documento. En la URL de ejemplo siguiente se detectaría un número del carné de conducir de un conductor francés que podría aparecer en texto en inglés, junto con las entidades predeterminadas en inglés.

> [!TIP]
> Si no incluye `default` al especificar las categorías de entidad, la API solo devolverá las categorías de entidad que especifique.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii?piiCategories=default,FRDriversLicenseNumber`

## <a name="getting-pii-results"></a>Obtención de resultados de DCP

Cuando obtiene los resultados de la detección de DCP, puede transmitirlos a una aplicación, o bien guardar la salida en un archivo en el sistema local. La respuesta de la API contendrá [entidades reconocidas](concepts/entity-categories.md), incluidas sus categorías y subcategorías, y las puntuaciones de confianza. También se devolverá la cadena de texto con las entidades de DCP censuradas.

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
