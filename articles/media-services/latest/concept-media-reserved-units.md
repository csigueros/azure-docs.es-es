---
title: 'Unidades reservadas de multimedia: Azure'
description: Las unidades reservadas de multimedia le permiten escalar el proceso multimedia y determinar la velocidad de las tareas de procesamiento multimedia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 44205bc628a839dd28cd574dbd19a22e9856d999
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719413"
---
# <a name="media-reserved-units"></a>Unidades reservadas de multimedia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services le permite escalar el procesamiento multimedia mediante la administración de unidades reservadas de multimedia (MRU). Una MRU proporciona capacidad informática adicional necesaria para codificar los elementos multimedia. El número de MRU determina la velocidad con la que se procesan las tareas multimedia y el número de tareas multimedia que se pueden procesar a la vez en una cuenta. Por ejemplo, si su cuenta tiene cinco MRU y hay tareas que procesar, se pueden ejecutar cinco tareas multimedia a la vez. El resto de tareas esperarán en la cola y se elegirán para el procesamiento secuencialmente cuando finalice la tarea en ejecución. Cada MRU que aprovisione tiene como resultado una reserva de capacidad, pero no proporciona recursos dedicados. Durante períodos de demanda excesivamente alta, puede que todas las MRU no empiecen a procesarse de inmediato.

Una tarea es una operación de trabajo en un recurso, por ejemplo, la codificación de streaming adaptable. Al enviar un trabajo, Media Services se encargará de dividirlo en operaciones (es decir, tareas) que se asociarán a distintas MRU.

## <a name="choosing-between-different-reserved-unit-types"></a>Selección de los distintos tipos de unidad reservada

Con esta tabla será más fácil tomar la decisión de elegir entre distintas velocidades de codificación.  En ella se muestra la duración de la codificación de un vídeo de 7 minutos y 1080p en función de la MRU utilizada.

|Tipo de RU|Escenario|Resultados de ejemplo para el vídeo de 7 min y 1080 p |
|---|---|---|
| **S1**|Codificación con velocidad de bits sencilla. <br/>Archivos con resolución SD o menor, no sujetos a limitación temporal y de bajo costo.|La codificación en el archivo MP4 de resolución SD de velocidad de bits única con "H264 Single Bitrate SD 16x9" tarda en torno a 7 minutos.|
| **S2**|Codificación con velocidad de bits sencilla y múltiple.<br/>Uso normal para codificación SD y HD.|La codificación con el valor predeterminado "H264 Single Bitrate 720p" tarda en torno a 6 minutos.<br/><br/>La codificación con el valor predeterminado "H264 Multiple Bitrate 720p" tarda en torno a 12 minutos.|
| **S3**|Codificación con velocidad de bits sencilla y múltiple.<br/>Vídeos con resolución Full HD y 4K. Codificación con respuesta más rápida, sujeta a limitación temporal.|La codificación con el valor predeterminado "H264 Single Bitrate 1080p" tardará aproximadamente 3 minutos.<br/><br/>La codificación con el valor predeterminado "H264 Multiple Bitrate 1080p" tarda aproximadamente 8 minutos.|

> [!NOTE]
> Si no aprovisiona MRU para su cuenta, las tareas multimedia se procesarán con el rendimiento de una MRU S1 y las tareas se seleccionarán de manera secuencial. Como no se reserva capacidad de procesamiento, el tiempo de espera entre una tarea que finaliza y otra que empieza dependerá de la disponibilidad de los recursos del sistema.

## <a name="considerations"></a>Consideraciones

* En los trabajos de análisis de audio y vídeo desencadenados por Media Services v3 o Azure Video Analyzer for Media se recomienda encarecidamente aprovisionar la cuenta con diez unidades S3. Si necesita más de 10 MRU S3, abra una incidencia de soporte técnico desde [Azure Portal](https://portal.azure.com/).
* En el caso de las tareas de codificación que no tienen MRU, no hay ningún límite superior con respecto al tiempo que las tareas pueden pasar en estado en cola y, como máximo, solo una tarea se ejecutará a la vez.

## <a name="billing"></a>Facturación

Se le cobra en función del número de minutos que se aprovisionan las unidades reservadas de multimedia en su cuenta, tanto si hay trabajos en ejecución como si no. Para obtener una explicación detallada, vea la sección de preguntas más frecuentes de la página de [precios de Media Services](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-step"></a>Paso siguiente
[Escalado de unidades reservadas de codificación con la CLI](media-reserved-units-cli-how-to.md)
[Análisis de vídeos](analyze-videos-tutorial.md)

## <a name="see-also"></a>Consulte también

* [Cuotas y límites](limits-quotas-constraints-reference.md)
