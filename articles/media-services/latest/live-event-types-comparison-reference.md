---
title: Tipos de LiveEvent en Azure Media Services
description: 'En Azure Media Services, los objetos LiveEvent pueden ser de uno de estos dos tipos: *codificación en directo* y de *paso a través*. En este artículo se muestra una tabla detallada que compara los tipos de LiveEvent.'
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0916439ef75c73d59ada1f9a832b95e733520c9c
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401035"
---
# <a name="live-event-types-comparison"></a>Comparación de tipos de objetos LiveEvent

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En Azure Media Services, un [evento en directo](/rest/api/media/liveevents) se puede establecer en una *codificación de paso a través* (un codificador en directo local envía una secuencia de velocidad de bits múltiple) o en una *codificación en directo* (un codificador en directo local envía una secuencia de velocidad de bits única). 

En este artículo se comparan las características de los tipos de eventos en directo.

## <a name="types-comparison"></a>Comparación de tipos 

En la tabla siguiente se comparan las características de los tipos de LiveEvent. Los tipos se establecen durante la creación mediante [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.PassthroughBasic**: un codificador en directo local envía una secuencia con velocidad de bits múltiple. El tránsito básico se limita a una entrada máxima de 5 Mbps, una ventana DVR de 8 horas y no se admite la transcripción en directo.
* **LiveEventEncodingType.PassthroughStandard**: un codificador en directo local envía una secuencia con velocidad de bits múltiple. El tránsito estándar tiene límites de ingesta más altos, una ventana DVR de 25 horas como máximo y admite transcripciones en directo.
* **LiveEventEncodingType.Standard**: un codificador en directo local envía una secuencia única de velocidad de bits al evento en directo y Media Services crea varias secuencias de velocidad de bits. Si la fuente de contribución tiene una resolución de 720p o más, el valor predeterminado **Default720p** codificará un conjunto de 6 pares de velocidad de bits-resolución (los detalles se muestran más adelante en el artículo).
* **LiveEventEncodingType.Premium1080p**: un codificador en directo local envía una única secuencia de velocidad de bits al evento en directo y Media Services crea varias secuencias de velocidad de bits. El valor predeterminado Default1080p especifica el conjunto de salida de pares de resolución-velocidad de bits (los detalles se muestran más adelante en el artículo). 

| Característica                                                                           | Tránsito básico                                                                                                | Tránsito estándar                                                                                             | Evento de codificación Standard 720P o Premium 1080P                                                                                                                          |
| --------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| La entrada con velocidad de bits única se codifica en varias velocidades de bits en la nube            | No                                                                                                                | No                                                                                                                | Sí                                                                                                                                                          |
| Resolución de vídeo máxima para la fuente de contribución                                    | 4 K (4096 x 2160 en 60 fotogramas por segundo)                                                                                   | 4 K (4096 x 2160 en 60 fotogramas por segundo)                                                                                   | 1080 p (1920 x 1088 en 30 fotogramas por segundo)                                                                                                                           |
| Niveles máximos recomendados en la fuente de contribución (dentro de los límites de ancho de banda de ingesta) | Limitado al ancho de banda agregado máximo de 5 Mbps                                                                                              | Limitado al ancho de banda agregado máximo de 60 Mbps                                                                                                       | 1 pista de vídeo y 1 pista de audio (las pistas adicionales se descartan silenciosamente)                                                                                                                                                    |
| Número máximo de niveles en la salida                                                          | Igual que la entrada                                                                                                     | Igual que la entrada                                                                                                     | Hasta 6 (vea debajo los valores predeterminados del sistema)                                                                                                                           |
| Ancho de banda agregado máximo de la fuente de contribución                                  | Admite entradas combinadas de hasta 5 Mbps y velocidades de bits individuales que no superen los 4 Mbps. Sin restricción en la velocidad de fotogramas de vídeo.                                                                                                     | Admite entradas combinadas de hasta 60 Mbps y velocidades de bits individuales que no superen los 20 Mbps. Sin restricción en la velocidad de fotogramas de vídeo.                                                                                                    | Admite una entrada con velocidad de bits única. El ancho de banda de entrada individual no puede superar los 20 Mbps. La velocidad de fotogramas de vídeo no puede superar los 60 fotogramas por segundo.                                                                                                                                                         |
| Duración máxima de la ventana DVR (cambio de hora) permitida                                  | Hasta 8 horas                                                                                                     | Hasta 25 horas                                                                                                    | Hasta 25 horas                                                                                                                                               |
| Número máximo de salidas en directo permitidas                                            | Solo 1 salida en directo                                                                                                | Hasta 3 salidas en directo                                                                                              | Hasta 3 salidas en directo                                                                                                                                         |
| Velocidad de bits máxima para una sola capa de la contribución                            | Hasta 4 Mbps                                                                                                      | 20 Mbps                                                                                                           | 20 Mbps                                                                                                                                                      |
| Compatibilidad con pistas de audio de varios idiomas                                        | Sí                                                                                                               | Sí                                                                                                               | No                                                                                                                                                           |
| Códecs compatibles de vídeo de entrada                                                      | H.264/AVC (RTMP y Smooth) o H.265/HEVC (solo ingesta de Smooth Streaming)                                         | H.264/AVC (RTMP y Smooth) o H.265/HEVC (solo ingesta de Smooth Streaming)                                         | H.264/AVC (ingesta de RTMP y de Smooth Streaming)                                                                                                                 |
| Códecs compatibles de vídeo de salida                                                     | Igual que la entrada                                                                                                     | Igual que la entrada                                                                                                     | H.264/AVC                                                                                                                                                    |
| Profundidad de bits, entrada y salida de vídeo compatibles                                      | Hasta 10 bits incluido HDR 10/HLG                                                                                 | Hasta 10 bits incluido HDR 10/HLG                                                                                 | 8 bits                                                                                                                                                        |
| Códecs de audio de entrada compatibles                                                      | AAC-LC, HE-AAC v1, HE-AAC v2                                                                                      | AAC-LC, HE-AAC v1, HE-AAC v2                                                                                      | AAC-LC, HE-AAC v1, HE-AAC v2                                                                                                                                 |
| Códecs de audio de salida compatibles                                                     | Igual que la entrada                                                                                                     | Igual que la entrada                                                                                                     | AAC-LC                                                                                                                                                       |
| Resolución máxima de vídeo para el vídeo de salida                                          | Igual que la entrada                                                                                                     | Igual que la entrada                                                                                                     | Standard: 720p, Premium1080p: 1080p                                                                                                                        |
| Velocidad de fotogramas máxima de vídeo de entrada                                                 | 60 fotogramas por segundo                                                                                                  | 60 fotogramas por segundo                                                                                                  | La salida transcodificada Standard o Premium1080p de 60 fotogramas/segundo se reducirá a 23,98, 24, 25, 29,97 o 30 fps solo en función de la velocidad de fotogramas de origen. |
| Protocolos de entrada                                                                   | RTMP, MP4 fragmentado (Smooth Streaming)                                                                           | RTMP, MP4 fragmentado (Smooth Streaming)                                                                           | RTMP, MP4 fragmentado (Smooth Streaming)                                                                                                                      |
| Price                                                                             | Consulte la [página de precios](https://azure.microsoft.com/pricing/details/media-services/) y haga clic en la pestaña "Vídeo en vivo" | Consulte la [página de precios](https://azure.microsoft.com/pricing/details/media-services/) y haga clic en la pestaña "Vídeo en vivo" | Consulte la [página de precios](https://azure.microsoft.com/pricing/details/media-services/) y haga clic en la pestaña "Vídeo en vivo"                                            |
| Tiempo de ejecución máximo                                                                  | 24 horas x 365 días, lineal en directo                                                                                    | 24 horas x 365 días, lineal en directo                                                                                    | 24 horas x 365 días, lineal en directo (vista previa)                                                                                                                     |
| Posibilidad de pasar a través de los subtítulos CEA 608/708 insertados                        | Sí                                                                                                               | Sí                                                                                                               | Sí                                                                                                                                                          |
| Compatibilidad con la transcripción en directo                                            | No. No se admiten transcripciones en directo en el tránsito básico.                                                 | Sí                                                                                                               | Sí                                                                                                                                                          |
| Compatibilidad con los mensajes de señalización de anuncios mediante SCTE-35 en banda | Sí | Sí | Sí                                                                                                                                                                                                   |
| Compatibilidad con GOP de entrada no uniformes                    | Sí | Sí | Sí, duración                                               |
| Apagado automático de LiveEvent cuando se pierde la fuente de entrada    | No  | No  | Después de 12 horas, si no hay ningún objeto LiveEvent en ejecución                                                                                                                                                     |

## <a name="system-presets"></a>Valores predeterminados del sistema

Las resoluciones y velocidades de bits contenidas en la salida del codificador en directo vienen determinadas por [presetName](/rest/api/media/liveevents/create#liveeventencoding). Si usa un codificador en directo **Standard** (LiveEventEncodingType.Standard), el valor predeterminado *Default720p* especifica un conjunto de 6 pares de resolución-velocidad de bits descritos debajo. En caso contrario, si usa un codificador en directo **Premium1080p** (LiveEventEncodingType.Premium1080p), el valor predeterminado *Default1080p* especifica el conjunto de salida de pares de resolución-velocidad de bits.

> [!NOTE]
> No se puede aplicar el valor predeterminado Default1080p a un evento en directo si se ha configurado para la codificación en directo Standard. Si lo hace, se producirá un error. También obtendrá un error si intenta aplicar el valor predeterminado Default720p predeterminado a un codificador en directo Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Secuencias de vídeo de salida para Default720p

Si la fuente de contribución tiene una resolución de 720p o más, el valor predeterminado **Default720p** codificará la fuente en las 6 capas siguientes. En la tabla siguiente, Velocidad de bits se expresa en kbps, MaxFPS representa la velocidad máxima de fotogramas permitida (en fotogramas por segundo) y Perfil representa el perfil de H.264 usado.

Si la velocidad de fotogramas de origen de la entrada es >30 fps, la velocidad de fotogramas se reducirá para que coincida con la mitad de la velocidad de fotogramas de entrada.  Por ejemplo, 60 fps se reducirían a 30 fps.  50 fps se reducirían a 25 fps, etc.


| Bitrate | Ancho | Alto | Fotogramas/seg. máx. | Perfil |
| ------- | ----- | ------ | ------ | ------- |
| 3500    | 1280  | 720    | 30     | Alto    |
| 2200    | 960   | 540    | 30     | Alto    |
| 1350    | 704   | 396    | 30     | Alto    |
| 850     | 512   | 288    | 30     | Alto    |
| 550     | 384   | 216    | 30     | Alto    |
| 200     | 340   | 192    | 30     | Alto    |

> [!NOTE]
> Si necesita personalizar el valor predeterminado de codificación en directo, abra una incidencia de soporte técnico a través de Azure Portal. Debe especificar la tabla de resoluciones de vídeo y velocidades de bits deseadas. No se admite la personalización de la velocidad de bits de codificación de audio. Compruebe que hay solo una capa a 720p y, como máximo, 6 capas. También se debe especificar que se solicita un valor predeterminado.

### <a name="output-video-streams-for-default1080p"></a>Secuencias de vídeo de salida para Default1080p

Si la fuente de contribución tiene una resolución de 1080p, el valor predeterminado **Default1080p** codificará la fuente en las 6 capas siguientes.

Si la velocidad de fotogramas de origen de la entrada es >30 fps, la velocidad de fotogramas se reducirá para que coincida con la mitad de la velocidad de fotogramas de entrada.  Por ejemplo, 60 fps se reducirían a 30 fps.  50 fps se reducirían a 25 fps, etc.

| Bitrate | Ancho | Alto | Fotogramas/seg. máx. | Perfil |
| ------- | ----- | ------ | ------ | ------- |
| 5500    | 1920  | 1080   | 30     | Alto    |
| 3000    | 1280  | 720    | 30     | Alto    |
| 1600    | 960   | 540    | 30     | Alto    |
| 800     | 640   | 360    | 30     | Alto    |
| 400     | 480   | 270    | 30     | Alto    |
| 200     | 320   | 180    | 30     | Alto    |

> [!NOTE]
> Si necesita personalizar el valor predeterminado de codificación en directo, abra una incidencia de soporte técnico a través de Azure Portal. Debe especificar la tabla de resoluciones y velocidades de bits deseadas. Compruebe que hay solo una capa a 1080p y, como máximo, 6 capas. También debe especificar que se solicita un valor predeterminado para un codificador en directo Premium1080p. Es posible que, a lo largo del tiempo, se tengan que ajustar los valores específicos de las velocidades de bits y resoluciones.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Secuencias de audio de salida para Default720p y Default1080p

Para los valores predeterminados *Default720p* y *Default1080p*, el audio se codifica en estéreo AAC-LC a 128 kbps. La velocidad de muestreo sigue la de la pista de audio en la fuente de contribución.

## <a name="implicit-properties-of-the-live-encoder"></a>Propiedades implícitas del codificador en directo

En la sección anterior se describen las propiedades del codificador en directo que se pueden controlar de forma explícita, mediante el valor predeterminado, como el número de capas, resoluciones y velocidades de bits. En esta sección se explican las propiedades implícitas.

### <a name="group-of-pictures-gop-duration"></a>Duración del grupo de imágenes (GOP)

El codificador en directo sigue la estructura de la contribución de fuente del [GOP](https://en.wikipedia.org/wiki/Group_of_pictures), lo que significa que las capas de salida tendrán la misma duración del GOP. Por lo tanto, se recomienda que configure el codificador local para generar una fuente de contribución que haya corregido la duración del GOP (normalmente 2 segundos). Esto garantizará que las secuencias HLS y MPEG DASH salientes desde el servicio también tengan duraciones del GOP corregidas. La mayoría de dispositivos es probable que toleren pequeñas variaciones en las duraciones del GOP.

### <a name="frame-rate-limits"></a>Límites de velocidad de fotogramas

El codificador en directo también sigue las duraciones de los fotogramas de vídeo individuales en la contribución de fuente, lo que significa que las capas de salida tendrán fotogramas con las mismas duraciones. Por lo tanto, se recomienda que configure el codificador local para generar una fuente de contribución que haya corregido la velocidad de fotogramas (como máximo 30 fotogramas por segundo). Esto garantizará que las secuencias HLS y MPEG DASH salientes desde el servicio también tengan duraciones de velocidad de fotogramas corregidas. Es probable que la mayoría de dispositivos toleren pequeñas variaciones en las velocidades de fotogramas, pero no existen garantías de que el codificador en directo generará una salida que se reproducirá correctamente. El codificador en directo local no debe perder fotogramas (por ejemplo, en condiciones de batería baja) ni modificar la velocidad de fotogramas de ninguna forma.

Si la velocidad de fotogramas de origen de la entrada es >30 fps, la velocidad de fotogramas se reducirá para que coincida con la mitad de la velocidad de fotogramas de entrada.  Por ejemplo, 60 fps se reducirían a 30 fps.  50 fps se reducirían a 25 fps, etc.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Resolución de la fuente de contribución y las capas de salida

El codificador en directo está configurado para evitar aumentar la resolución de la fuente de contribución. Por lo tanto, la resolución máxima de las capas de salida no superará la de la fuente de contribución.

Por ejemplo, si envía una fuente de contribución a 720p a un evento en directo configurado para codificación en directo de Default1080p, la salida tendrá solo 5 capas, empezando por 720p a 3 Mbps, y dejará de funcionar en 1080p a 200 kbps. O bien, si envía una fuente de contribución a 360p a un evento en directo configurado para la codificación en directo Standard, el resultado contendrá 3 capas (con resoluciones de 288p, 216p y 192p). En el caso degenerado, si envía una fuente de contribución de, por ejemplo, 160 x 90 píxeles a un codificador en directo Standard, la salida contendrá una capa con una resolución de 160 x 90 a la misma velocidad de bits que la de la fuente de contribución.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Velocidad de bits de la fuente de contribución y las capas de salida

El codificador en directo está configurado para respetar la configuración de velocidad de bits en el valor predeterminado, con independencia de la velocidad de bits de la fuente de contribución. Por lo tanto, puede que la velocidad de bits de las capas de salida supere la de la fuente de contribución. Por ejemplo, si se envía en una fuente de contribución con una resolución de 720p a 1 Mbps, las capas de salida serán las mismas que en la [tabla](live-event-types-comparison-reference.md#output-video-streams-for-default720p) anterior.

## <a name="next-steps"></a>Pasos siguientes

[Introducción al streaming en vivo](stream-live-streaming-concept.md)
