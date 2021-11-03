---
title: Llamada a la API de vinculación de entidad
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo identificar y vincular las entidades encontradas en el texto con la API de vinculación de entidad.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: a7edc27898c1af9fcb8f7bc72698d2d6c3a63e68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017635"
---
# <a name="how-to-use-entity-linking"></a>Uso de la vinculación de entidad

La característica de vinculación de entidad se puede utilizar para identificar y eliminar la ambigüedad de identidad de una entidad que se ha encontrado en el texto (por ejemplo, determinar si una aparición de la palabra *Marte* hace referencia al planeta o al dios romano de la guerra). Devolverá las entidades del texto con vínculos a [Wikipedia](https://www.wikipedia.org/) como base de conocimiento.

> [!TIP]
> Si quiere empezar a usar esta característica, puede seguir el [artículo de inicio rápido](../quickstart.md). También puede realizar solicitudes de ejemplo mediante [Language Studio](../../language-studio.md) sin necesidad de escribir código.

## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-entity-linking-model"></a>Especificación del modelo de vinculación de entidad

De manera predeterminada, la vinculación de entidad usará el modelo de IA disponible más reciente en el texto. También puede configurar las solicitudes de API para usar una versión específica del modelo. El modelo que especifique se usará para realizar operaciones de vinculación de entidad.

| Versiones admitidas | Versión más reciente |
|--|--|
| `2019-10-01`, `2020-02-01` | `2020-02-01` |

### <a name="input-languages"></a>Idiomas de entrada

Al enviar documentos para que se procesen mediante la vinculación de entidad, puede especificar en cuál de los [idiomas admitidos](../language-support.md) están escritos. Si no especifica un idioma, la vinculación de entidad se establecerá de manera predeterminada en inglés. Debido a la [compatibilidad con varios idiomas y con emojis](../../concepts/multilingual-emoji-support.md), la respuesta puede contener desplazamientos de texto. 

## <a name="submitting-data"></a>Envío de datos

La vinculación de entidad genera un resultado de mayor calidad cuando se le proporcionan fragmentos de texto más pequeños para trabajar. Esto es lo contrario de algunas características, como la extracción de frases clave, que funciona mejor en bloques de texto más grandes. Para obtener los mejores resultados de ambas operaciones, considere la posibilidad de reestructurar las entradas en consecuencia.

Para enviar una solicitud de API, necesitará el punto de conexión y la clave de un recurso de idioma.

> [!NOTE]
> Puede encontrar la clave y el punto de conexión de su recurso de idioma en Azure Portal. Los encontrará en la página **Key and endpoint** (Clave y punto de conexión) del recurso, en **Resource management** (Administración de recursos). 

El análisis se realiza tras la recepción de la solicitud. Para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte los límites de datos a continuación.

El uso de la vinculación de entidad de forma sincrónica no tiene estado. No se almacenan datos en la cuenta y los resultados se devuelven inmediatamente en la respuesta.

Cuando se usa esta característica de forma asincrónica, los resultados de la API están disponibles durante 24 horas desde el momento en que se ingiere la solicitud y se indican en la respuesta. Después de este período de tiempo, los resultados se purgan y ya no están disponibles para la recuperación.

### <a name="getting-entity-linking-results"></a>Obtención de los resultados de la vinculación de entidad  

Puede transmitir los resultados a una aplicación o guardar la salida en un archivo en el sistema local.

## <a name="data-limits"></a>Límites de datos

> [!NOTE]
> * Si necesita analizar documentos con un tamaño superior al límite, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. 
> * Un documento es una sola cadena de caracteres de texto.  

| Límite | Value |
|------------------------|---------------|
| Tamaño máximo de un documento individual | 5120 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Número máximo de caracteres por solicitud (asincrónico)  | 125 000 caracteres entre todos los documentos enviados, medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Tamaño máximo de la solicitud completa | 1 MB |
| Número máximo de documentos por solicitud | 5 |

Si un documento supera el límite de caracteres, la API se comportará de forma diferente en función de si se usa de forma sincrónica o asincrónica:

* Asincrónico: la API rechazará toda la solicitud y devolverá un error `400 bad request` si algún documento de la misma supera el tamaño máximo.
* Sincrónico: la API no procesará un documento que supere el tamaño máximo y devolverá un error de documento no válido. Si una solicitud de API tiene varios documentos, la API seguirá procesándolos si están dentro del límite de caracteres.

### <a name="rate-limits"></a>Límites de frecuencia

El límite de velocidad variará en función del [plan de tarifa](https://aka.ms/unifiedLanguagePricing).

| Nivel          | Solicitudes por segundo | Solicitudes por minuto |
|---------------|---------------------|---------------------|
| S / Varios servicios | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>Consulte también

* [Introducción a la vinculación de entidad](../overview.md)
