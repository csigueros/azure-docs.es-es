---
title: 'Notas de la versión de Azure Video Analyzer: Azure'
description: En este tema encontrará las notas de la versión de las mejoras, las versiones, las correcciones de errores y los problemas conocidos de Azure Video Analyzer.
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8b17c19d96699a6442bd6d606ffbc88b25088afe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087046"
---
# <a name="azure-video-analyzer-release-notes"></a>Notas de la versión de Azure Video Analyzer

>Reciba notificaciones para volver a visitar esta página y obtener actualizaciones; para ello, copie y pegue esta URL (`https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us`) en el lector de fuentes RSS.

En este artículo se proporciona información acerca de lo siguiente:

* Versiones más recientes
* Problemas conocidos
* Corrección de errores
* Funciones obsoletas

<hr width=100%>

## <a name="november-2-2021"></a>2 de noviembre de 2021

Esta versión es una actualización del módulo perimetral de Video Analyzer y del servicio Video Analyzer. La etiqueta de versión del módulo perimetral es:

```
mcr.microsoft.com/media/video-analyzer:1.1.0
```

> [!NOTE]
> En los artículos de inicio rápido y tutoriales, los manifiestos de implementación usan una etiqueta 1 (video-analyzer:1). Por lo tanto, con tan solo reimplementar tales manifiestos se debería actualizar el módulo en los dispositivos perimetrales cuando se publiquen etiquetas más nuevas.

La versión de la API de ARM del servicio Video Analyzer es:

```
2021-11-01-preview
```

### <a name="new-functionalities"></a>Nuevas funcionalidades

* Al usar Video Analyzer con [Computer Vision](edge/computer-vision-for-spatial-analysis.md)para el servicio de inteligencia artificial de análisis espacial de Cognitive Services, puede generar y ver nuevas conclusiones, como la velocidad, la orientación y el seguimiento de las personas en el vídeo en directo.
* Puede detectar [dispositivos compatibles con ONVIF](edge/camera-discovery.md) en la subred local del dispositivo perimetral.
* Puede [capturar y grabar vídeo en directo directamente en la nube](cloud/connect-cameras-to-cloud.md).
  * Puede usar el [streaming de baja latencia](playback-recordings-how-to.md#low-latency-streaming) para ver el vídeo en directo desde la cámara RTSP con latencias de un extremo a otro de aproximadamente 2 segundos.
  * Puede implementar el contrato PnP de [IoT de Video Analyzer](cloud/connect-devices.md) en la cámara RTSP para habilitar la captura de vídeo desde el dispositivo al servicio Video Analyzer.
* Puede [exportar la parte deseada del vídeo grabado](cloud/export-portion-of-video-as-mp4.md) a un archivo MP4.
* Puede especificar una directiva de retención para cualquiera de los vídeos grabados, donde el servicio recortaría periódicamente el contenido anterior al número de días especificado.
* Los vídeos grabados con el módulo perimetral de Video Analyzer pueden incluir [imágenes de vista previa](edge/enable-video-preview-images.md) o miniaturas periódicamente, lo que permite una mejor experiencia de exploración.

## <a name="october-1-2021"></a>1 de octubre de 2021
El servicio Video Analyzer ya está disponible (en versión preliminar) en el Este de Australia. Consulte [aquí](https://azure.microsoft.com/global-infrastructure/services/?products=video-analyzer&regions=all) para obtener la información de disponibilidad más reciente.

Se ha agregado un nuevo artículo para describir cómo puede [insertar el widget de Video Analyzer en Power BI](embed-player-in-power-bi.md)

## <a name="june-3-2021"></a>3 de junio de 2021

La etiqueta de versión de la actualización de julio de 2021 del módulo es la siguiente:

```
     mcr.microsoft.com/media/video-analyzer:1.0.1
```
> [!NOTE]
> En los artículos de inicio rápido y tutoriales, los manifiestos de implementación usan una etiqueta 1 (video-analyzer:1). Por lo tanto, con tan solo reimplementar tales manifiestos se debería actualizar el módulo en los dispositivos perimetrales cuando se publiquen etiquetas más nuevas.

### <a name="module-updates"></a>Actualizaciones del módulo
* Admite caracteres Unicode en las credenciales para conectarse a una cámara RTSP
* Actualizaciones para habilitar registros detallados en modo de depuración

<hr width=100%>

## <a name="may-25-2021"></a>25 de mayo de 2021

Esta versión es la primera versión preliminar pública de Azure Video Analyzer. La etiqueta de versión es la siguiente:

```
mcr.microsoft.com/media/video-analyzer:1.0.0
```

> [!NOTE]
> En los artículos de inicio rápido y tutoriales, los manifiestos de implementación usan una etiqueta 1 (video-analyzer:1). Por lo tanto, con tan solo reimplementar tales manifiestos se debería actualizar el módulo en los dispositivos perimetrales cuando se publiquen etiquetas más nuevas.

### <a name="supported-functionalities"></a>Funcionalidades admitidas

* Analice secuencias de vídeo en directo con los módulos de inteligencia artificial que prefiera y, de manera opcional, grabe vídeos en su dispositivo perimetral o en la nube.
* Grabación de vídeo junto con metadatos de inferencia en la nube
* Desencadenamiento de eventos cuando los objetos cruzan una línea con su propio modelo de detección de objetos
* Seguimiento de los objetos detectados por su propio modelo de detección 
* Uso de widgets (componentes web) del reproductor de Video Analyzer para reproducir metadatos de inferencia y vídeo grabado
* Implemente y configure el módulo a través de IoT Hub mediante Azure Portal o Visual Studio Code.
<!--REDIRECT* Manage [pipeline topologies](pipeline.md#pipeline-topologies) remotely or locally using [direct method](direct-methods.md) calls-->

## <a name="next-steps"></a>Pasos siguientes

[Información general](overview.md)
