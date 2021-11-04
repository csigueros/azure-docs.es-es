---
title: Ejemplo del widget del reproductor de Azure Video Analyzer
description: En este artículo de referencia se proporciona una descripción general breve de la aplicación de ejemplo del widget del reproductor Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 842ffa07a956c2bb90bd039f25c49bf72467ecee
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557782"
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
      "x": 0.7536813922356091,
      "y": 0.4358353510895884
    },
    {
      "x": 0.7563587684069611,
      "y": 0.7699757869249395
    }
  ]
}{
  "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
  "name": "Zone 2",
  "polygon": [
    {
      "x": 0.11780455153949129,
      "y": 0.7627118644067796
    },
    {
      "x": 0.15261044176706828,
      "y": 0.774818401937046
    },
    {
      "x": 0.0749665327978581,
      "y": 0.8983050847457628
    },
    {
      "x": 0.04551539491298527,
      "y": 0.864406779661017
    }
  ]
}
```
Puede copiar estas coordenadas con el botón **Copiar al Portapapeles**. Es posible cambiar el nombre de las zonas y líneas, y también eliminarlas, con los tres puntos que están ubicados junto a cada zona y línea.  
![Captura de pantalla del cajón de zonas.](./media/sample-player-widget/widget-zone-drawer.png)

## <a name="video-clips"></a>Clips de vídeo
Los clips de vídeo le permiten seleccionar una fecha y hora de inicio junto con una fecha y hora de finalización. Para generar el clip de vídeo, use el botón *Agregar*.
Puede seleccionar cualquiera de los clips generados en la lista desplegable, donde cada clip se título como fechas y horas de inicio y finalización.
Así pues, el clip de vídeo se puede ver en el formato de reproductor de vídeo típico.  
![Captura de pantalla de clips de vídeo.](./media/sample-player-widget/widget-video-clips.png)

## <a name="next-steps"></a>Pasos siguientes
Intente crear su propio widget personalizado del reproductor de Video Analyzer con estas [instrucciones](./player-widget.md).
