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
ms.date: 09/16/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a34e2d16edb4a8ec9ba40a4426fcbbd9b2127b16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590411"
---
# <a name="content-aware-encoding"></a>Codificación que tiene en cuenta el contenido

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview-of-the-content-aware-encoding-preset"></a>Información general sobre el valor predeterminado de codificación en función del contenido

Para preparar el contenido para la entrega mediante [streaming con velocidad de bits adaptable](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), el vídeo debe codificarse en varias velocidades de bits (de alta a baja) y con varias resoluciones. Esta técnica permite a los reproductores de vídeo modernos de hoy en día de Apple iOS, Android, Windows y Mac usar protocolos de streaming que transmitan contenido fácilmente sin almacenar en búfer. Estas diferentes representaciones de tamaño de pantalla (resolución) y calidad (velocidad de bits) permiten al reproductor seleccionar la mejor versión del vídeo que las condiciones de red actuales pueden admitir. La red puede variar ampliamente de LAN, 4G, 5G, Wi-Fi pública o una red doméstica.

El proceso de codificación de contenido en varias representaciones requiere la generación de una "escala de codificación": una tabla de resoluciones y velocidades de bits que indica al codificador qué generar. Para obtener un ejemplo de este tipo de escala, vea los [valores predeterminados de codificación integrados](/rest/api/media/transforms/createorupdate#encodernamedpreset) de Media Services.

En condiciones ideales, quiere tener en cuenta el tipo de contenido que está codificando. Con esta información, puede ajustar la escala de codificación para que coincida con la complejidad y el movimiento del vídeo de origen. Esto significa que, en cada tamaño de pantalla (resolución) de la escala, debe haber una velocidad de bits más allá de la cual cualquier aumento de la calidad no sea perceptible: el codificador funciona con este valor de velocidad de bits óptimo.

El siguiente nivel de optimización que se puede realizar es seleccionar las resoluciones basadas en el contenido; por ejemplo, un vídeo de una presentación de PowerPoint con texto pequeño tendría un aspecto desenfocado con una codificación por debajo de 720 líneas de píxeles de alto. Además, también puede tener un vídeo que cambia el movimiento y la complejidad en función de cómo se rodó y editó.  Esto proporciona una oportunidad para adaptar y ajustar la configuración de codificación en cada escena o límite de toma. Un codificador inteligente puede encargarse de optimizar la configuración de codificación de cada toma dentro del vídeo.

Azure Media Services ofrece un valor predeterminado de [streaming adaptable](encode-autogen-bitrate-ladder.md) que soluciona parcialmente el problema de la variabilidad de la velocidad de bits y la resolución de los vídeos de origen. Sin embargo, este valor predeterminado no analiza el contenido de origen para ver lo complejo que es ni la cantidad de movimiento que contiene. 

El valor predeterminado de codificación en función del contenido mejora el valor predeterminado de codificación de "streaming con velocidad de bits adaptable" más estático mediante la adición de lógica que permite al codificador buscar un valor de velocidad de bits óptimo para una resolución determinada, pero sin requerir un análisis computacional amplio. Este valor predeterminado genera una "escala" única de MP4 alineados con GOP en función del archivo de origen. Dado un vídeo de origen, el valor predeterminado realiza un análisis rápido inicial del contenido de entrada y usa los resultados para determinar el número óptimo de capas, velocidad de bits y resoluciones necesarias para ofrecer la experiencia de streaming de velocidad de bits adaptable de máxima calidad. Este valor predefinido resulta eficaz en los vídeos de complejidad baja y media, donde los archivos de salida tendrán velocidades de bits más lentas que el valor predefinido de streaming adaptable más estático, pero con una calidad que seguirá ofreciendo una buena experiencia a la audiencia. La carpeta de salida contendrá varios archivos MP4 con vídeo y audio listos para el streaming.

## <a name="configure-output-settings"></a>Establecimiento de la configuración de salida

Además, los desarrolladores también pueden controlar el intervalo de salidas que usa el valor predeterminado de codificación con reconocimiento de contenido al decidir la configuración óptima para codificar la escala de streaming de velocidad de bits adaptable.

Con la clase **PresetConfigurations**, los desarrolladores pueden pasar un conjunto de restricciones y opciones al valor predeterminado de codificación con reconocimiento de contenido para controlar los archivos resultantes generados por el codificador. Las propiedades son especialmente útiles para situaciones en las que quiera limitar toda la codificación a una resolución máxima específica para controlar la experiencia o los costos de los trabajos de codificación.  También es útil poder controlar las velocidades de bits máximas y mínimas que el público puede admitir en una red móvil o en una región global que tenga restricciones de ancho de banda.

## <a name="supported-codecs"></a>Códecs admitidos

El valor predeterminado de codificación en función del contenido está disponible para su uso con los códecs siguientes:
-  H.264
-  HEVC (H.265)

## <a name="how-to-use"></a>How-to use (Procedimientos)

Vea la [guía de codificación en función del contenido](./encode-content-aware-How-to.md) para obtener información detallada sobre cómo usar el valor predeterminado del código y vínculos para completar ejemplos.

## <a name="technical-details-on-content-aware-preset"></a>Detalles técnicos sobre el valor predeterminado en función del contenido

Ahora vamos a profundizar un poco más en cómo funciona el valor predeterminado de codificación en función del contenido.  En los siguientes gráficos de ejemplo se muestra la comparación usando métricas de calidad como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) y [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). El origen se creó mediante la concatenación de clips cortos de capturas de gran complejidad de películas y programas de TV con la finalidad de destacar el codificador. Por definición, este valor predeterminado genera resultados que varían de contenido en contenido. Esto también significa que, para cierto contenido, puede ser que no se produzca una reducción significativa de la velocidad de bits ni una mejora en la calidad.

![Curva de distorsión de la velocidad (RD) mediante PSNR](media/encode-content-aware-concept/msrv1.png)

**Figura 1: Curva de distorsión de la velocidad (RD) con la métrica de PSNR para un origen de gran complejidad**

![Curva de distorsión de la velocidad (RD) mediante VMAF](media/encode-content-aware-concept/msrv2.png)

**Figura 2: Curva de distorsión de la velocidad (RD) con la métrica de VMAF para un origen de gran complejidad**

A continuación encontrará los resultados de otra categoría de contenido de origen, en la que el codificador pudo determinar que la entrada era de baja calidad (muchos artefactos de compresión debido a una baja velocidad de bits). Con el valor predeterminado en función del contenido, el codificador decidió generar solo una capa de salida a una velocidad de bits lo suficientemente baja para que la mayoría de los clientes pudieran reproducir la transmisión sin sufrir un estancamiento.

![Curva de RD mediante PSNR](media/encode-content-aware-concept/msrv3.png)

**Figura 3: Curva de RD mediante PSNR para entradas de baja calidad (a 1080p)**

![Curva de RD mediante VMAF](media/encode-content-aware-concept/msrv4.png)

**Ilustración 4: Curva de RD mediante VMAF para entradas de baja calidad (a 1080p)**

  
## <a name="next-steps"></a>Pasos siguientes
* [Uso del valor predeterminado de codificación en función del contenido](encode-content-aware-how-to.md)
* [Tutorial: Carga, codificación y streaming de vídeos con Media Services v3](stream-files-tutorial-with-api.md)