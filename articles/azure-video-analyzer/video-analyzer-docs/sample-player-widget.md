---
title: Ejemplo del widget del reproductor de Azure Video Analyzer
description: En este artículo de referencia se proporciona una descripción general breve de la aplicación de ejemplo del widget del reproductor Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 08/13/2021
ms.openlocfilehash: abea3bdd1098dbb32be35d632c7a8b0ad51fc49e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058498"
---
# <a name="azure-video-analyzer-player-widget-sample"></a>Ejemplo del widget del reproductor de Azure Video Analyzer

Esta aplicación de ejemplo muestra la integración del widget del reproductor de Video Analyzer con las características de reproducción de vídeo, dibujo de zona y generación de clips de vídeo.

* Clone el [repositorio de ejemplo de C# de AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp).
* Siga las instrucciones que aparecen en el archivo [LÉAME de la carpeta **src/video-player**](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/blob/main/src/video-player/README.md).

## <a name="get-started"></a>Introducción
Después de leer las instrucciones del archivo LÉAME y de que la aplicación esté en ejecución en http://localhost:3000/:
1. Haga clic en "Obtener vídeos".
2. Seleccione un vídeo en la lista desplegable de todos los vídeos de la cuenta de Video Analyzer.

## <a name="video-player"></a>Reproductor de vídeo
En la página Reproductor de vídeo se muestra el reproductor típico, tal como se muestra en el portal.  
![Captura de pantalla del reproductor de vídeo.](./media/sample-player-widget/widget-video-player.png)

## <a name="zone-drawer"></a>Cajón de zonas
El cajón de zonas le permite crear zonas mediante dibujos de polígonos y líneas en el vídeo. También puede guardar estas zonas y líneas para recibir las coordenadas de sus zonas y líneas respectivas.  
  
**Ejemplo**:
```json
  {
    "@type": "#Microsoft.VideoAnalyzer.NamedLineString",
    "name": "Line 1",
    "line": [
      {
        "x": 0.6987951807228916,
        "y": 0.4430992736077482
      },
      {
        "x": 0.6987951807228916,
        "y": 0.7046004842615012
      }
    ]
  }{
    "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
    "name": "Zone 2",
    "polygon": [
      {
        "x": 0.10575635876840696,
        "y": 0.7481840193704601
      },
      {
        "x": 0.16599732262382866,
        "y": 0.7796610169491526
      },
      {
        "x": 0.07764390896921017,
        "y": 0.9007263922518159
      },
      {
        "x": 0.024096385542168676,
        "y": 0.8547215496368039
      }
    ]
  }
```
Puede copiar estas coordenadas con el botón **Copiar al Portapapeles**. Es posible cambiar el nombre de las zonas y líneas, y también eliminarlas, con los tres puntos que están ubicados junto a cada zona y línea.  
![Captura de pantalla del cajón de zonas.](./media/sample-player-widget/widget-zone-drawer.png)

## <a name="video-clips"></a>Clips de vídeo
Los clips de vídeo le permiten seleccionar una fecha y hora de inicio junto con una fecha y hora de finalización. Para generar el clip de vídeo, use el botón *Agregar*.
Puede seleccionar cualquiera de los clips generados en la lista desplegable, donde cada clip se título como fechas y horas de inicio y finalización.
(Ejemplo: formato) Así, el clip de vídeo se puede ver en el formato de reproductor de vídeo típico.  
![Captura de pantalla de clips de vídeo.](./media/sample-player-widget/widget-video-clips.png)

## <a name="next-steps"></a>Pasos siguientes
Intente crear su propio widget personalizado del reproductor de Video Analyzer con estas [instrucciones](./player-widget.md).

