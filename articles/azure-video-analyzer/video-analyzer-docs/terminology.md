---
title: Terminología de Azure Video Analyzer
description: En este artículo se proporciona información general sobre la terminología de Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9010ae81606bcd84d37debc4c702a0bc53e328dd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846964"
---
# <a name="azure-video-analyzer-terminology"></a>Terminología de Azure Video Analyzer

En este artículo se proporciona información general sobre la terminología relacionada con [Azure Video Analyzer](overview.md).

## <a name="pipeline-topology"></a>Topología de canalización

Una [topología de canalización](pipeline.md) le permite definir dónde se debe capturar el elemento multimedia, cómo se debe procesar y dónde se deben entregar los resultados. Permite definir una canalización que consta de orígenes, procesadores y nodos receptores con la que puede crear aplicaciones de análisis de vídeo en vivo. 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) hace referencia al Protocolo de streaming en tiempo real. Este es un protocolo de nivel de aplicación para controlar la entrega de datos con propiedades en tiempo real. RTSP proporciona un marco extensible para habilitar la entrega controlada a petición de datos en tiempo real, como audio y vídeo. Video Analyzer [admite](pipeline.md#rtsp-source) la captura, el análisis y la grabación de vídeo desde cámaras IP que admitan RTSP.

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) hace referencia a un sistema de administración de vídeo. Estos sistemas se usan para configurar y controlar cámaras de vigilancia, y para capturar y grabar vídeos de ellas. Estos sistemas también proporcionan aplicaciones cliente para reproducir el vídeo grabado.

## <a name="recording"></a>Grabando

En el contexto de un sistema de administración de vídeo para cámaras de seguridad, la grabación de vídeo hace referencia al proceso de capturar vídeo desde las cámaras y almacenarlo para verlo posteriormente mediante aplicaciones móviles y de explorador. La grabación de vídeo se puede clasificar en [grabación continua de vídeo](continuous-video-recording.md) y [grabación de vídeo basada en eventos](event-based-video-recording-concept.md). Ambas categorías se explican con más detalle en la página de conceptos sobre la [grabación de vídeo](video-recording.md).

## <a name="streaming"></a>Streaming

Puede usar Video Analyzer para transmitir grabaciones de vídeo a los clientes mediante protocolos de streaming multimedia estándar del sector basados en HTTP, como [HTTP Live Streaming (HLS)](https://developer.apple.com/streaming/) y [MPEG-DASH](https://dashif.org/about/). Puede usar los [widgets del reproductor de Azure Video Analyzer](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md) (componentes web) para reproducir recursos de vídeo. Además, HLS es compatible con reproductores web como [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/) y [Shaka Player, de Google](https://github.com/google/shaka-player), o bien se puede representar de forma nativa en aplicaciones móviles con [Exoplayer](https://github.com/google/ExoPlayer) para Android y [AV Foundation](https://developer.apple.com/av-foundation/) para iOS. De forma similar, MPEG-DASH es compatible con [la lista de clientes de esta página](https://dashif.org/tools/clients/).

## <a name="exporting"></a>Exportación

En el contexto de sistema de administración de vídeo para cámaras de seguridad, la exportación de vídeo hace referencia al proceso de tomar partes seleccionadas de una grabación de vídeo y crear un archivo independiente, normalmente un archivo [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14), que luego se puede compartir externamente. Por ejemplo, la grabación de vídeo podría contener imágenes en las que una acción rápida por parte del personal impidió un incidente de seguridad. Esa parte de la grabación podría exportarse a un archivo MP4 para su uso en sesiones de entrenamiento futuras.

## <a name="video"></a>Vídeo

Los vídeos son recursos de la cuenta de Video Analyzer que permiten funcionalidades de sistema de administración de vídeo como grabación, reproducción, streaming y exportación. Los vídeos se pueden grabar desde cámaras RTSP o se pueden crear mediante la exportación de partes de un vídeo grabado existente. Los vídeos grabados se pueden transmitir y ver mediante el widget del reproductor de Video Analyzer u otros reproductores compatibles. Los vídeos exportados se pueden descargar como archivos MP4.

Al usar Video Analyzer para grabar desde una cámara RTSP, debe asociar ese recurso de vídeo a esa cámara. Puede grabar vídeo de forma continua desde esa cámara en ese recurso de vídeo, o puede grabar de forma esporádica en función de eventos: Video Analyzer admite el anexo de datos a un recurso de vídeo existente. Pero, para ello, se requiere que no se cambien las propiedades de la cámara RTSP (su resolución, velocidad de fotogramas, etc.). Si tiene que cambiar la configuración de la cámara, debe cambiar a la grabación a un nuevo recurso de vídeo.

Al crear una cuenta de Video Analyzer, tiene que asociar una cuenta de Azure Storage a ella. Tanto los vídeos grabados como los exportados se almacenan como blobs en un contenedor de la cuenta de almacenamiento. Todo el contenido asociado a estos recursos de vídeo se almacena como blobs en los contenedores correspondientes, mientras que Video Analyzer contiene los metadatos (por ejemplo, nombre, descripción u hora de creación).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) es un marco de llamada a procedimiento remoto (RPC) de alto rendimiento e independiente del idioma. Usa esquemas estructurados basados en sesiones a través de [búferes de protocolo 3](https://developers.google.com/protocol-buffers/docs/proto3) como su formato de intercambio de mensajes subyacente para la comunicación.

## <a name="low-latency-streaming"></a>Streaming de baja latencia

El streaming de vídeo de baja latencia es una funcionalidad beneficiosa de un sistema de administración de vídeo. Video Analyzer puede transmitir vídeo en directo con un retraso de aproximadamente 2 segundos. Latencia significa el retraso entre el momento en que se produce un evento delante de la cámara y el momento en que se ve ese evento en un dispositivo de reproducción.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [canalizaciones](pipeline.md).
