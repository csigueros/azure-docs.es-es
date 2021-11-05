---
title: Habilitación de imágenes de vista previa
description: En este artículo se explica cómo habilitar y acceder a imágenes de vista previa al grabar vídeo con Azure Video Analyzer
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed3ebeffb70b592c1a81e8ec7871a662f7b856bd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090924"
---
# <a name="enable-preview-images-when-recording-video"></a>Habilitación de imágenes de vista previa al grabar vídeo

Puede usar Azure Video Analyzer para [capturar y grabar vídeo](../video-recording.md) desde una cámara RTSP. Al hacerlo, se crea una topología de canalización que incluye un nodo receptor de vídeo, como se muestra en esta guía de [inicio rápido](detect-motion-record-video-clips-cloud.md) o en este [tutorial](use-continuous-video-recording.md). 

Si graba vídeo mediante el módulo perimetral de Video Analyzer, puede habilitar el nodo receptor de vídeo para generar periódicamente un conjunto de imágenes de vista previa de diferentes tamaños. Estas imágenes se pueden recuperar posteriormente del [recurso de vídeo](../terminology.md#video) de su cuenta de Video Analyzer. Por ejemplo, si su cámara genera un vídeo con una resolución de 1920 × 1080, las imágenes de vista previa tendrán los siguientes tamaños:

  * 320 × 180: pequeña
  * 640 × 360: mediana
  * 1280 × 720: grande

> [!NOTE]
> Las imágenes de vista previa mantendrán la relación de aspecto de vídeo de la cámara.

Las imágenes de vista previa se generan periódicamente y la frecuencia viene determinada por [`segmentLength`](../playback-recordings-how-to.md#recording-and-playback-latencies). Si usa la [grabación basada en eventos](record-event-based-live-video.md), debe tener en cuenta que las imágenes solo se generan cuando la canalización en directo está activa y el vídeo se está grabando. Cada vez que se genera un conjunto de imágenes de vista previa, este sobrescribe el conjunto anterior.

> [!NOTE]
> Esta funcionalidad solo está disponible actualmente con el módulo perimetral de Video Analyzer. Además, su habilitación tiene un impacto en los costos de almacenamiento de Azure, debido a las transacciones frecuentes para escribir las imágenes o visualizarlas y al tamaño de las imágenes.

## <a name="enable-preview-images-in-the-video-sink-node"></a>Habilitación de imágenes de vista previa en el nodo receptor de vídeo
Para habilitar las imágenes de vista previa, debe establecer la marca adecuada en el nodo receptor de vídeo de la topología de canalización. En **videoPublishingOptions**, establezca **enableVideoPreviewImage** en **true**.  

Ejemplo:
```
        "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "name": "videoSink",
          "videoName": "{$parameter-for-specifying-unique-videoName-for-each-pipeline}",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "videoPublishingOptions": {
            "enableVideoPreviewImage": true
          },
          "videoCreationProperties": {
            "title": "{$parameter-for-specifying-unique-title-for-each-pipeline}",
            "description": "{$parameter-for-specifying-unique-description-for-each-pipeline}k",
            "segmentLength": "PT30S"
          },
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048"
        }
      ]
``` 

## <a name="accessing-preview-images"></a>Acceso a las imágenes de vista previa

Para obtener las direcciones URL estáticas de las imágenes de vista previa disponibles, se debe llamar a una solicitud GET en el recurso de vídeo con un [token de portador autorizado](../playback-recordings-how-to.md#accessing-videos). Verá las direcciones URL enumeradas en **contentUrls** en la respuesta, como se muestra a continuación.

```
      "contentUrls": {
        ...
        "previewImageUrls": {
          "small": "XXXX",
          "medium": "XXXX",
          "large": "XXXX"
         }
       },
    
```

## <a name="next-steps"></a>Pasos siguientes

Pruebe a habilitar imágenes de vista previa de vídeo en la topología en este [inicio rápido](detect-motion-record-video-clips-cloud.md) o en este [tutorial](use-continuous-video-recording.md). 
