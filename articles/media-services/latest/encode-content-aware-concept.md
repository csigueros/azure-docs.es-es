---
title: Valor predeterminado de codificación en función del contenido
description: En este artículo se describe la codificación según el contenido en Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f333b4ca86e24c845a8a91c621a2b3f7c8c984e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429761"
---
# <a name="content-aware-encoding-preset"></a>Valor predeterminado de codificación en función del contenido

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para poder preparar el contenido para la entrega mediante [streaming con velocidad de bits adaptable](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), el vídeo debe codificarse en varias velocidades de bits (de alta a baja). Esto garantiza una degradación correcta de la calidad, ya que la resolución del vídeo disminuye a medida que lo hace la velocidad de bits. Esta codificación con velocidad de bits múltiple utiliza algo que se conoce como escalera de codificación (una table de resoluciones y velocidades de bits, consulte los [valores predeterminados de codificación integrados](/rest/api/media/transforms/createorupdate#encodernamedpreset) de Media Services).

Debe tener en cuenta el contenido que va a procesar y personalizar o ajustar la escalera de codificación a la complejidad del vídeo individual. A cada resolución le corresponde una velocidad de bits más allá de la cual no se percibe un aumento en la calidad: el codificador funciona en este valor de velocidad de bits óptimo. El siguiente nivel de optimización consiste en seleccionar las resoluciones en función del contenido: por ejemplo, un vídeo de una presentación de PowerPoint no saca ningún provecho al bajar de 720p. Más adelante, el codificador puede encargarse de optimizar la configuración de cada captura dentro del vídeo. 

El valor predeterminado de [streaming adaptable](encode-autogen-bitrate-ladder.md) de Microsoft soluciona parcialmente el problema de la variabilidad de calidad y resolución de los vídeos de origen. El contenido de nuestros clientes es muy variado: algunos vídeos tienen una resolución de 1080p, otros 720p y algunos tienen resoluciones de SD e inferiores. Además, no todo el contenido de origen está en formato mezzanine de alta calidad procedente de estudios de televisión o cinematográficos. El valor preestablecido de streaming adaptable soluciona estos problemas garantizando que la escala de velocidad de bits nunca supere la resolución o la velocidad de bits media del archivo mezzanine de entrada. Sin embargo, las únicas propiedades del origen que examina este valor predeterminado son la resolución y la velocidad de bits.

## <a name="the-content-aware-encoding-preset"></a>El valor predeterminado de codificación en función del contenido

El valor predeterminado de codificación en función del contenido amplía el mecanismo de "streaming con velocidad de bits adaptable", para lo que incorpora una lógica personalizada que permite al codificador buscar el valor óptimo de velocidad de bits para una resolución determinada sin requerir un análisis de cálculo exhaustivo. Este valor predefinido produce un conjunto de archivos MP4s alineados con GOP. Dado cualquier contenido de entrada, el servicio realiza un análisis ligero inicial del mismo y usa los resultados para determinar automáticamente el número óptimo de capas, la velocidad de bits adecuada y la configuración de resolución para la entrega a través del streaming adaptable. Este valor predefinido resulta particularmente eficaz en los vídeos de complejidad media y baja, donde los archivos de salida tendrán velocidades de bits más lentas que el valor predefinido de streaming adaptable, pero una calidad que seguirá ofreciendo una buena experiencia a los visores. La salida contendrá archivos MP4 con el vídeo y audio intercalados.

En los siguientes gráficos de ejemplo se muestra la comparación usando métricas de calidad como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) y [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). El origen se creó mediante la concatenación de clips cortos de capturas de gran complejidad de películas y programas de TV con la finalidad de destacar el codificador. Por definición, este valor predeterminado genera resultados que varían de contenido en contenido. Esto también significa que, para cierto contenido, puede ser que no se produzca una reducción significativa de la velocidad de bits ni una mejora en la calidad.

![Curva de distorsión de la velocidad (RD) mediante PSNR](media/encode-content-aware-concept/msrv1.png)

**Figura 1: Curva de distorsión de la velocidad (RD) con la métrica de PSNR para un origen de gran complejidad**

![Curva de distorsión de la velocidad (RD) mediante VMAF](media/encode-content-aware-concept/msrv2.png)

**Figura 2: Curva de distorsión de la velocidad (RD) con la métrica de VMAF para un origen de gran complejidad**

A continuación encontrará los resultados de otra categoría de contenido de origen, en la que el codificador pudo determinar que la entrada era de baja calidad (muchos artefactos de compresión debido a una baja velocidad de bits). Tenga en cuenta que, con el valor preestablecido en función del contenido, el codificador decidió generar solo una capa de salida a una velocidad de bits lo suficientemente baja para que la mayoría de los clientes pudieran reproducir la transmisión sin sufrir un estancamiento.

![Curva de RD mediante PSNR](media/encode-content-aware-concept/msrv3.png)

**Figura 3: Curva de RD mediante PSNR para entradas de baja calidad (a 1080p)**

![Curva de RD mediante VMAF](media/encode-content-aware-concept/msrv4.png)

**Ilustración 4: Curva de RD mediante VMAF para entradas de baja calidad (a 1080p)**

## <a name="8-bit-hevc-h265-support"></a>Compatibilidad con HEVC de 8 bits (H.265)

El codificador estándar de Azure Media Services ahora admite la codificación HEVC (H.265) de 8 bits. El contenido de HEVC se puede entregar y empaquetar a través del empaquetador dinámico con el formato "hev1".

Hay disponible una nueva codificación .NET personalizada con un ejemplo de HEVC en el [repositorio de Git Hub media-services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC). Además de la codificación personalizada, AMS también admite otros nuevos valores preestablecidos de codificación HEVC integrados que puede ver en nuestras [notas de la versión de febrero de 2021](https://docs.microsoft.com/azure/media-services/latest/release-notes#february-2021).
  
## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Carga, codificación y streaming de vídeos con Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Carga, codificación y transmisión de vídeos con REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: Node.js](stream-files-nodejs-quickstart.md)
