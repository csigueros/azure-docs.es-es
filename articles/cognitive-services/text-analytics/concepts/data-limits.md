---
title: Límites de datos de la API Text Analytics
titleSuffix: Azure Cognitive Services
description: Limitaciones de datos de la API Text Analytics en Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/07/2021
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: b583caa4fdb2a1e72833d4e24c317282be041513
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822142"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Límites de datos y velocidad de la API Text Analytics
<a name="data-limits"></a>

Use este artículo para encontrar los límites de tamaño y las velocidades a las que se pueden enviar datos a la API Text Analytics.

## <a name="data-limits"></a>Límites de datos

> [!NOTE]
> * Los límites de datos o velocidad no afectan a los precios. Estos se basan en el número de registros de texto que se envían a la API y dependen de los [detalles de precio](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) del recurso de Text Analytics.
>   * Cada registro de texto tiene 1000 caracteres. 
> * Los límites de datos y velocidad se basan en el número de documentos que se envían a la API. Si necesita analizar documentos con un tamaño superior al límite, puede dividir el texto en fragmentos más pequeños antes de enviarlos a la API. 
>   * Un documento es una sola cadena de caracteres de texto.  

| Límite | Value |
|------------------------|---------------|
| Tamaño máximo de un documento individual | 5120 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). También se aplica a Text Analytics for Health. |
| Tamaño máximo de un documento individual (punto de conexión de `/analyze`)  | 125 000 caracteres medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). No se aplica a Text Analytics for Health. |
| Tamaño máximo de la solicitud completa | 1 MB. También se aplica a Text Analytics for Health. |


Si un documento supera el límite de caracteres, la API se comportará de forma diferente en función del punto de conexión que esté usando:

* Punto de conexión `/analyze`:
  * La API rechazará toda la solicitud y devolverá un error `400 bad request` si algún documento de la misma supera el tamaño máximo.
* Todos los demás puntos de conexión:  
  * La API no procesará un documento que supere el tamaño máximo y devolverá un error de documento no válido. Si una solicitud de API tiene varios documentos, la API seguirá procesándolos si están dentro del límite de caracteres.

El número máximo de documentos que puede enviar en una única solicitud dependerá de la versión de la API y de la característica que use, tal como se indica en la tabla siguiente.

#### <a name="version-3"></a>[Versión 3](#tab/version-3)

Los siguientes límites son para la API v3 actual. Si se superan los límites siguientes, se generará un código de error HTTP 400.


| Característica | Número máximo de documentos por solicitud | 
|----------|-----------|
| Detección de idiomas | 1000 |
| Análisis de sentimiento | 10 |
| Minería de opiniones | 10 |
| Extracción de frases clave | 10 |
| Reconocimiento de entidades con nombre | 5 |
| Entity Linking | 5 |
| Text Analytics for Health  | 10 para la API basada en web y 1000 para el contenedor. |
| Analizar punto de conexión | 25 para todas las operaciones. |

#### <a name="version-2"></a>[Versión 2](#tab/version-2)

| Característica | Número máximo de documentos por solicitud | 
|----------|-----------|
| Detección de idiomas | 1000 |
| Análisis de sentimiento | 1000 |
| Extracción de frases clave | 1000 |
| Reconocimiento de entidades con nombre | 1000 |
| Entity Linking | 1000 |

---

## <a name="rate-limits"></a>Límites de frecuencia

El límite de velocidad variará en función del [plan de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Estos límites son los mismos en ambas versiones de la API. Estos límites de velocidad no se aplican a Text Analytics para el contenedor de estado, que no tiene un límite de velocidad establecido.

| Nivel          | Solicitudes por segundo | Solicitudes por minuto |
|---------------|---------------------|---------------------|
| S / Varios servicios | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Los índices de solicitudes se miden por separado para cada característica de Text Analytics. Puede enviar el número máximo de solicitudes correspondiente al plan de tarifa a cada característica, al mismo tiempo. Por ejemplo, si se encuentra en el nivel de `S` y envía 1000 solicitudes a la vez, no podrá enviar ninguna otra solicitud durante 59 segundos.


Los niveles S0-S4 han quedado en desuso y se recomienda cambiar al nivel S.

## <a name="see-also"></a>Consulte también

* [¿Qué es la API Text Analytics?](../overview.md)
* [Detalles de precios](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
