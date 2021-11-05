---
title: 'Grabación de vídeo continua desde el perímetro: Azure Video Analyzer'
description: Grabación de vídeo continua (CVR) hace referencia al proceso de grabación continua desde un origen de vídeo en directo. En este tema se explica qué es CVR y cómo se usa con Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 830727c530d71e3700799ae296af6a124010a921
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031183"
---
# <a name="continuous-video-recording"></a>Grabación de vídeo continua    

Grabación de vídeo continua (CVR) hace referencia al proceso de grabación continua del vídeo desde un origen de vídeo. Azure Video Analyzer admite la grabación de vídeo continua, de forma ininterrumpida, de una cámara de videovigilancia a través de una [topología de canalización](pipeline.md) de procesamiento de vídeo que se compone de un nodo de origen RTSP y un nodo receptor de vídeo. El diagrama siguiente muestra una representación gráfica de esa canalización. La representación JSON de la topología se encuentra en este [documento](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json). Puede usar esta topología para crear grabaciones arbitrariamente largas (años de contenido). Las marcas de tiempo de las grabaciones se almacenan en formato UTC.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Grabación de vídeo continua":::

Una instancia de la topología de canalización descrita anteriormente se puede ejecutar en un dispositivo perimetral en el servicio Video Analyzer, con el receptor de vídeo grabando en un [recurso de vídeo](terminology.md#video). El vídeo se grabará durante todo el tiempo que la canalización permanezca en estado activado. El vídeo grabado se puede reproducir mediante las funcionalidades de streaming de Video Analyzer. Para más información, consulte [Reproducción de grabaciones de vídeo](playback-recordings-how-to.md).

## <a name="suggested-pre-reading"></a>Sugerencias antes de la lectura  

Es aconsejable leer los siguientes artículos antes de continuar.

* [Concepto de topología de canalización](pipeline.md)
* [Concepto de grabación de vídeo](video-recording.md) 
 
## <a name="resilient-recording"></a>Grabación resistente

El módulo perimetral Video Analyzer admite el funcionamiento en condiciones en las que el dispositivo perimetral puede perder ocasionalmente la conectividad con la nube o experimentar una reducción del ancho de banda disponible. Para tener todo esto en cuenta, el vídeo del origen se registra localmente en una caché y se sincroniza automáticamente con el recurso de vídeo de forma periódica. Si examina la [topología de canalización](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json), observará que tiene definidas las siguientes propiedades:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
```

Las dos últimas propiedades son apropiadas para la grabación resistente (también son propiedades obligatorias para un nodo de receptor de vídeo). La propiedad `localMediaCachePath` indica al receptor de vídeo que use la ruta de acceso de la carpeta para almacenar en caché los datos multimedia antes de cargarlos en la nube. Puede consultar [este](../../iot-edge/how-to-access-host-storage-from-module.md) artículo para entender de qué forma puede el módulo IoT Edge usar el almacenamiento local del dispositivo. La propiedad `localMediaCacheMaximumSizeMiB` define cuánto espacio en disco puede usar el receptor de vídeo como memoria caché (1 MiB = 1024 * 1024 bytes). 

Si el módulo IoT Edge pierde la conectividad durante mucho tiempo y el contenido almacenado en la carpeta de la caché alcanza el valor `localMediaCacheMaximumSizeMiB`, el receptor de vídeo empezará a descartar los datos de la caché, empezando por los más antiguos. Por ejemplo, si el dispositivo perdió la conectividad a las 10 a. m. y la caché alcanza el límite máximo a las 6 p. m., el receptor de vídeo empieza a eliminar los datos grabados a las 10 a. m. 

Cuando se restaure la conectividad de red, el receptor de vídeo comenzará la carga desde la memoria caché y volverá a empezar por los datos más antiguos. En el ejemplo anterior, supongamos que es necesario descartar de la memoria caché 5 minutos de vídeo antes de restaurar la conectividad (por ejemplo, a las 6:02 p. m.). El receptor de vídeo empezará a realizar la carga desde la marca 10:05 a. m.

También pueden producirse deficiencias en las grabaciones, por ejemplo, si reinicia canalizaciones por el motivo que sea. También puede detener una canalización y restaurarla en un momento posterior (mientras la configuración de la cámara no cambie, podrá seguir grabando en el mismo recurso de vídeo de Video Analyzer).

## <a name="segmented-recording"></a>Grabación segmentada  

La propiedad `segmentLength` que se muestra anteriormente le ayudará a controlar el costo de las transacciones de escritura asociado a la escritura de datos en la cuenta de almacenamiento en la que se registra el recurso de vídeo. Por ejemplo, si aumenta el valor de 30 segundos a 5 minutos, el número de transacciones de almacenamiento se reducirá 10 veces (5 * 60/30).

La propiedad `segmentLength` garantiza que el vídeo se escribe en la cuenta de almacenamiento como máximo una vez cada `segmentLength` segundos. Esta propiedad tiene un valor mínimo de 30 segundos (también el valor predeterminado) y se puede aumentar en incrementos de 30 segundos hasta un máximo de 5 minutos.

Esta propiedad se aplica tanto al módulo perimetral de Video Analyzer como al servicio Video Analyzer. Consulte el artículo [Reproducción de grabaciones de vídeo](playback-recordings-how-to.md) para ver el efecto que `segmentLength` tiene en la reproducción.

## <a name="see-also"></a>Consulte también

* [Grabación de vídeo basada en eventos](event-based-video-recording-concept.md) 
* [Reproducción de grabaciones de vídeo](playback-recordings-how-to.md) 

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: grabación continua de vídeo](edge/use-continuous-video-recording.md) 
