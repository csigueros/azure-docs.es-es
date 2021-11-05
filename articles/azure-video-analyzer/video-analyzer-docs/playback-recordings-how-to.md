---
title: 'Visualización de vídeos: Azure Video Analyzer'
description: Puede usar Azure Video Analyzer para la grabación continua de vídeo, lo que permite grabar vídeo en la nube durante semanas o meses. También puede limitar la grabación a clips de interés mediante la grabación basada en eventos. Además, al usar el servicio Video Analyzer para capturar vídeos de cámaras, puede transmitir el vídeo a medida que se captura. En este artículo se explica cómo ver estos vídeos.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 552daea0a5a19c7c9e11aca9b47d61ea112a42b3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846983"
---
# <a name="viewing-of-videos"></a>Visualización de vídeos

## <a name="suggested-pre-reading"></a>Sugerencias antes de la lectura

* [Recurso de vídeo de Video Analyzer](terminology.md#video)
* [Grabación de vídeo continua](continuous-video-recording.md)
* [Grabación de vídeo basada en eventos](event-based-video-recording-concept.md)

## <a name="background"></a>Información previa  

Para crear [recursos de vídeo](terminology.md#video) en la cuenta de Azure Video Analyzer puede grabar desde una cámara RTSP, o bien exportar una parte de esa grabación. Si va a crear [VMS](terminology.md#vms) mediante las API de Video Analyzer, este artículo le ayudará a comprender cómo puede ver vídeos. Después de leer este artículo, debe continuar con la revisión del artículo sobre las [directivas de acceso](access-policies.md) y el [widget del reproductor de Video Analyzer](player-widget.md). 

Si va a evaluar las funcionalidades de Video Analyzer, puede usar uno de los [inicios rápidos](edge/detect-motion-record-video-clips-cloud.md) o [tutoriales](edge/use-continuous-video-recording.md), y Azure Portal para ver los vídeos.
<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="creating-videos"></a>Creación de vídeos

A continuación se muestran algunas de las formas de crear vídeos mediante el módulo perimetral Video Analyzer:

* Grabación [continuada](continuous-video-recording.md) (CVR) desde una cámara RTSP, durante semanas, meses o más.
* Grabación de solo las partes que son de interés, mediante la [grabación de vídeo basada en eventos](event-based-video-recording-concept.md) (EVR). 
 
También puede usar el servicio Video Analyzer para crear vídeos mediante CVR. También puede usar el servicio para crear un vídeo mediante la exportación de una parte de una grabación de vídeo; estos vídeos contendrán un archivo descargable (en formato MP4).

## <a name="accessing-videos"></a>Acceso a los vídeos

Puede consultar la API de ARM [`Videos`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json) para ver las propiedades del recurso de vídeo. Video Analyzer también permite compilar aplicaciones en las que los usuarios finales pueden examinar y ver vídeos sin pasar por ARM. Como se muestra en el artículo sobre [puntos de conexión públicos](access-public-endpoints-networking.md), tiene acceso a las llamadas API de cliente con las que puede consultar las propiedades de los vídeos. Estas API se exponen mediante un punto de conexión de API de cliente, que puede encontrar en la sección Información general del panel Video Analyzer de Azure Portal, o mediante la API de ARM [`VideoAnalyzers`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/VideoAnalyzers.json). Puede controlar el acceso a este punto de conexión por medio de [directivas de acceso](access-policies.md) y en el artículo del [widget del reproductor de Video Analyzer](player-widget.md) se muestra cómo hacerlo.

## <a name="determining-that-a-video-recording-is-ready-for-viewing"></a>Determinación de que una grabación de vídeo está lista para la visualización

Si el recurso de vídeo representa la grabación desde una cámara RTSP, puede [transmitir ese contenido](terminology.md#streaming) una vez que se complete la grabación o mientras está en curso. Esto se indica mediante la marca `canStream`, que se establecerá en `true` para el recurso de vídeo. Tenga en cuenta que en estos vídeos `type` se habrá establecido en `archive` y la dirección URL de reproducción o streaming se devuelve en `archiveBaseUrl`. 

Al exportar una parte de una grabación de vídeo a un archivo MP4, en el recurso de vídeo resultante `type` se habrá establecido en `file` y estará disponible para su reproducción o descarga una vez que se complete el trabajo de exportación de vídeo. La dirección URL para la reproducción o descarga de estos archivos se devuelve en `downloadUrl`.
   > [!NOTE]
   > En las direcciones URL anteriores se necesita un [token de portador](./access-policies.md#creating-a-token). Vea la documentación del [widget del reproductor de Video Analyzer](player-widget.md) para más información.

## <a name="recording-and-playback-latencies"></a>Latencias de grabación y reproducción

Al usar el módulo perimetral de Video Analyzer para grabar en un recurso de vídeo, tendrá que especificar una [propiedad `segmentLength`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json) en la topología de canalización que indique al módulo que agregue una duración mínima de vídeo (en segundos) antes de que se escriba en la nube. Por ejemplo, si `segmentLength` se establece en 300, el módulo acumulará 5 minutos de vídeo antes de cargar un "fragmento" de 5 minutos y, a continuación, entrará en el modo de acumulación durante los 5 minutos siguientes y volverá a cargarlos. Aumentar el valor de `segmentLength` tiene la ventaja de reducir los costos de las transacciones de Azure Storage, ya que el número de lecturas y escrituras no tendrá una frecuencia superior a una vez cada `segmentLength` segundos. Si usa el servicio Video Analyzer, la topología de canalización tiene la misma [propiedad `segmentLength`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/PipelineTopologies.json).

Por lo tanto, el streaming del vídeo de la cuenta de Video Analyzer se retrasará al menos durante ese tiempo. 

Otro factor que determina la latencia de un extremo a otro (el retraso entre el momento en que se produce un evento delante de la cámara hasta el momento en que ve en un dispositivo de reproducción) es la duración del grupo de imágenes ([GOP](https://en.wikipedia.org/wiki/Group_of_pictures)). Como se describe en [Reducción del retraso del streaming en vivo mediante tres sencillas técnicas](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641), cuanto mayor sea la duración del GOP, mayor será la latencia. Es habitual que las cámaras IP que se usan en escenarios de vigilancia y seguridad se configuren con un valor de GOP de más de 30 segundos. Esto tiene un gran impacto sobre la latencia general.

## <a name="low-latency-streaming"></a>Streaming de baja latencia

Al usar el servicio Video Analyzer para capturar y grabar vídeos desde cámaras RTSP, puede ver el vídeo con latencias de aproximadamente 2 segundos mediante el [streaming de baja latencia](terminology.md#low-latency-streaming). El servicio pone a disposición un túnel websocket por el que un reproductor compatible con RTSP, como el [widget del reproductor de Video Analyzer](player-widget.md) puede recibir vídeo mediante el [protocolo RTSP](https://datatracker.ietf.org/doc/html/rfc7826.html). Tenga en cuenta que la latencia general depende del ancho de banda de red entre la cámara y la nube, así como entre la nube y el dispositivo de reproducción, y también de la capacidad de procesamiento del dispositivo de reproducción. La dirección URL para el streaming de baja latencia se devuelve en `rtspTunnelUrl`.

   > [!NOTE]
   > En la dirección URL anterior se necesita un [token de portador](./access-policies.md#creating-a-token). Vea la documentación del [widget del reproductor de Video Analyzer](player-widget.md) para más información.

## <a name="video-analyzer-player-widget"></a>Widget del reproductor de Video Analyzer
Video Analyzer ofrece las funcionalidades necesarias para entregar secuencias mediante los protocolos HLS, MPEG-DASH o RTSP a los dispositivos de reproducción (clientes). Puede usar el [widget del reproductor de Video Analyzer](player-widget.md) para obtener las direcciones URL pertinentes y el token de autorización de contenido, y utilizarlos en las aplicaciones cliente para reproducir el vídeo e inferir los metadatos.

Puede instalar el widget del reproductor de Video Analyzer para ver vídeos. El widget se puede instalar mediante `npm` o `yarn`, y esto le permitirá incluirlo en su propia aplicación del lado cliente. Ejecute uno de los siguientes comandos para incluir el widget en su propia aplicación:

NPM:
```
npm install –-save @azure/video-analyzer-widgets
```
YARN:
```
yarn add @azure/video-analyzer-widgets 
```
También puede insertar un script previo a la compilación existente; para ello, agregue type="module" al elemento del script que hace referencia a la ubicación previa a la compilación mediante el ejemplo siguiente:

```
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script> 
``` 

## <a name="viewing-video-with-inference-results"></a>Visualización de vídeo con resultados de inferencia
Al grabar vídeo con el módulo perimetral de Video Analyzer, si en la [canalización](pipeline.md) se usa inteligencia artificial para generar resultados de inferencia, puede grabarlos junto con el vídeo. Al visualizar el vídeo, el widget del reproductor de Video Analyzer puede superponer los resultados en el vídeo. Consulte [este tutorial](edge/record-stream-inference-data-with-video.md) para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

* [Descripción de las directivas de acceso](access-policies.md)
* [Uso del widget de reproductor de Video Analyzer](player-widget.md)
* [Grabación de vídeo continua en el perímetro](edge/use-continuous-video-recording.md)
* [Grabación de vídeo continua en la nube](cloud/get-started-livepipelines-portal.md)
