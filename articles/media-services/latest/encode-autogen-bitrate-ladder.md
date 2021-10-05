---
title: Codificación de vídeos con Standard Encoder en Media Services
description: En este tema se muestra cómo usar Standard Encoder en Media Services para codificar un vídeo de entrada con una escala de velocidad de bits generada automáticamente según la velocidad de bits y la resolución de entrada.
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
ms.date: 09/21/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 7fc9731769f568107a1ce6dacb5658fe164bd616
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657526"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Codificación con una escala de velocidad de bits generada automáticamente

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Información general

En este artículo se explica cómo usar Standard Encoder en Media Services para codificar un vídeo de entrada en una escala de velocidad de bits generada automáticamente (pares de velocidad de bits-resolución) según la velocidad de bits y la resolución de entrada. Esta configuración de codificador integrado, o valor preestablecido, nunca superará la velocidad de bits ni la resolución de entrada. Por ejemplo, si la entrada es 720p a 3 Mbps, la salida permanecerá en 720p en el mejor de los casos y se iniciará a velocidades inferiores a 3 Mbps.

### <a name="encoding-for-streaming"></a>Codificación para streaming

Si se usa **AdaptiveStreaming** o **H265AdaptiveStreaming** preestablecido en **Transform**, se recibe una salida adecuada para su entrega con protocolos de streaming como HLS y DASH. Cuando se usa uno de estos dos valores preestablecidos, el servicio determina de manera inteligente cuántas capas de vídeo debe generar y la velocidad de bits y la resolución. El contenido de la salida tiene archivos MP4 con el audio codificado con AAC y el vídeo codificado en H.264 (en el caso del valor preestablecido AdaptiveStreaming) o H.265/HEVC (en el caso del valor preestablecido H265AdaptiveStreaming). Los archivos MP4 de salida no son intercalados.

Para ver un ejemplo de cómo se usa este valor preestablecido, consulte [Streaming de un archivo](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Output

En esta sección se muestran tres ejemplos de las capas de vídeo de salida generadas por el codificador de Media Services como resultado de la codificación con el valor preestablecido **AdaptiveStreaming** (H.264) o **H265AdaptiveStreaming** (HEVC). En todos los casos, la salida contiene un archivo MP4 de solo audio con el audio estéreo codificado a 128 kbps.

### <a name="example-1"></a>Ejemplo 1
Un origen con un alto de "1080" y una tasa de fotogramas de "29.970" genera 6 niveles de vídeo:

|Nivel|Alto|Ancho|Velocidad de bits (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Ejemplo 2
Un origen con un alto de "720" y una tasa de fotogramas de "23.970" genera 5 niveles de vídeo:

|Nivel|Alto|Ancho|Velocidad de bits (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Ejemplo 3
Un origen con un alto de "360" y una tasa de fotogramas de "29.970" genera 3 niveles de vídeo:

|Nivel|Alto|Ancho|Velocidad de bits (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|


## <a name="content-aware-encoding-comparison"></a>Comparación de codificación en función del contenido

Los [valores predeterminados de codificación en función del contenido](./encode-content-aware-concept.md) constituyen una mejor solución que los valores preestablecidos de streaming adaptable gracias al análisis del contenido de origen antes de decidir el conjunto correcto de velocidades de bits de salida y resoluciones que se van a usar en la escala.
Se recomienda probar los [valores predeterminados de codificación en función del contenido](./encode-content-aware-concept.md) antes de usar la escala más estática y fija proporcionada por los valores preestablecidos de streaming con velocidad de bits adaptable.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Transmisión de un archivo](stream-files-dotnet-quickstart.md)
> [Uso del valor predeterminado de codificación en función del contenido](./encode-content-aware-concept.md)
> [Uso de la codificación en función del contenido](./encode-content-aware-how-to.md)
