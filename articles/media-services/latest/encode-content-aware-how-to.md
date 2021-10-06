---
title: Uso del valor predeterminado de codificación en función del contenido
description: En este artículo se describe cómo usar el valor predeterminado de codificación en función del contenido en Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 09/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 09d3f199cc17cc1457413f673afe3c6be0646f58
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668628"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>Uso del valor predeterminado de codificación en función del contenido

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Introducción
Los [valores preestablecidos con reconocimiento de contenido](encode-content-aware-concept.md) mejoran la preparación del contenido para su entrega mediante un [streaming con velocidad de bits adaptable](encode-autogen-bitrate-ladder.md), donde los vídeos se deben codificar a varias velocidades de bits. Incluye lógica personalizada que permite al codificador representar el mejor valor de velocidad de bits posible para una resolución determinada, sin necesidad de un análisis de cálculo exhaustivo. El valor predeterminado define automáticamente el número óptimo de capas, la velocidad de bits adecuada y la configuración de resolución para la entrega a través del streaming adaptable.

En comparación con el valor preestablecido de streaming adaptable, la codificación con reconocimiento de contenido es más eficaz en vídeos de una complejidad baja y media, donde los archivos de salida tendrán una velocidad de bits inferior, pero con una calidad que ofrezca una buena experiencia de visualización.

### <a name="using-the-content-aware-encoding-presets"></a>Uso de valores preestablecidos de codificación con reconocimiento de contenido

Para codificar con el valor preestablecido con reconocimiento de contenido en el código, use el objeto [BuiltInStandardEncoderPreset](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset) y establezca la propiedad [PresetName](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset.presetname) en uno de los siguientes nombres preestablecidos integrados.

- **ContentAwareEncoding:** : este valor preestablecido admite H.264.
- **H265ContentAwareEncoding:** : este valor preestablecido admite HEVC (H.265)

> [!NOTE]
> Asegúrese de usar el valor preestablecido **ContentAwareEncoding**, en lugar de ContentAwareEncodingExperimental. O bien, si desea codificar con HEVC, puede usar **H265ContentAwareEncoding**.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

## <a name="configure-output-settings"></a>Establecimiento de la configuración de salida

Además, los desarrolladores también pueden controlar el intervalo de salidas que usa el valor preestablecido de codificación con reconocimiento de contenido al decidir la configuración óptima para codificar la escalera de streaming de velocidad de bits adaptable.

Con la clase **PresetConfigurations**, los desarrolladores pueden pasar un conjunto de restricciones y opciones al valor preestablecido de codificación con reconocimiento de contenido para controlar los archivos resultantes generados por el codificador. Las propiedades son especialmente útiles para situaciones en las que quiera limitar toda la codificación a una resolución máxima específica para controlar la experiencia o los costos de los trabajos de codificación.  También es útil poder controlar las velocidades de bits máximas y mínimas que el público puede admitir en una red móvil o en una región global que tenga restricciones de ancho de banda.

La clase PresetConfigurations permite ajustar la siguiente configuración en los valores preestablecidos de codificación con reconocimiento de contenido. 


| Propiedad                   | Descripción                                                                                                                                                                         |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| complejidad              | Velocidad, Equilibrada o Calidad. Permite configurar los valores del codificador para controlar el equilibrio entre velocidad y calidad. Ejemplo: establezca Complejidad como Velocidad para una codificación más rápida, pero menos eficacia de compresión |
| interleaveOutput          |    Controla cómo se formateará el archivo MP4 de salida.  Se pueden intercalar con audio y vídeo para que sean más fáciles de compartir individualmente, o bien pueden ser archivos de audio y vídeo independientes para facilitar el enlace en tiempo de ejecución de pistas de audio de idioma adicionales o modificar el audio más adelante.                                                                                                                                                                                 |
| keyFrameIntervalInSeconds |        Establece la distancia entre fotogramas clave que se usa para cada grupo de imágenes (GOP) al codificar los archivos.  Los protocolos de streaming adaptables modernos típicos usan dos segundos.  En función de las condiciones de red que se entreguen, a veces es útil usar intervalos más largos. Consulte con el proveedor de la CDN o experimente en redes propias.                                                                                                                                                                             |
| maxBitrateBps             |               Limita la velocidad de bits superior de la escalera de streaming adaptable.  Esta restricción es útil para controlar los costos de almacenamiento y de entrega de red.  Además, esto también puede mantener las velocidades de bits entregadas dentro de un intervalo compatible con los clientes.                                                                                                                                                                       |
| maxHeight                 |       La resolución superior que se puede codificar en la escalera adaptable.  Esto también es útil para controlar los costos o controlar la experiencia del público. Puede limitar todas las codificaciones para que sean de definición Estándar o limitarlas a un máximo de 720P si lo prefiere.                                                                                                                                                                              |
| maxLayers                 |        Esta propiedad controla el número de capas en la escalera de streaming adaptable. También controla, por definición, el número de archivos representados como MP4 en el contenedor de almacenamiento.  Reducir este valor también reduce los costos ya que, al producirse menos representaciones de salida, se reducirá el número total de minutos de codificación. Use esto en combinación con maxHeight y maxBitrateBps para mantener el control de los costos durante la codificación y hacer que la facturación sea más predecible.                                                                                                                                                                             |
| minBitrateBps             |                           Controla la velocidad de bits más baja que elegirá el codificador.  Puede usarlo para optimizar la calidad de las representaciones de baja velocidad de bits y baja resolución.                                                                                                                                                         |
| minHeight                 |     Este valor controla la resolución más baja que producirá la escalera de velocidad de bits adaptable. Esto ayuda a evitar que los clientes seleccionen una representación de muy baja resolución que podría ser demasiado borrosa para el tipo de contenido que se codifica. Esto mejora considerablemente la calidad de la experiencia que ofrece.                                                                                                                                                                                |
|

### <a name="example-code-for-configuring-content-aware-preset"></a>Código de ejemplo para configurar un valor preestablecido con reconocimiento de contenido

El fragmento de código siguiente es del ejemplo [Codificación con restricciones, H.246 y reconocimiento de contenido](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained).

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/Encoding_H264_ContentAware_Constrained/Program.cs#PresetConfigurations)]

## <a name="samples"></a>Ejemplos

En los ejemplos siguientes se muestran los distintos códecs y restricciones que se pueden usar, cómo crear instancias del valor preestablecido y cómo enviar y supervisar un trabajo de codificación.

### <a name="net"></a>.NET

| Muestra | Descripción|
|---------|------------------|
| [Codificación con reconocimiento de contenido y H.246](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | Muestra el uso más básico de la codificación con reconocimiento de contenido H.264 sin restricciones |
| [Codificación con restricciones, H.246 y reconocimiento de contenido](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | Muestra cómo usar la clase PresetConfigurations para restringir el comportamiento de salida del valor preestablecido|
| [Codificación con reconocimiento de contenido y HEVC (H.265)](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | Muestra el uso básico del códec HEVC con codificación con reconocimiento de contenido y sin restricciones.  La clase PresetConfigurations también es compatible con HEVC y se puede agregar a este ejemplo.|

> [!NOTE]
> Los trabajos de codificación que usan el valor predeterminado `ContentAwareEncoding` se facturan únicamente en función de los minutos de salida. AMS usa la codificación de dos pasos y no hay ningún cargo extra derivado del uso de ninguno de los valores preestablecidos más allá de lo que aparece en nuestra [página de precios](https://azure.microsoft.com/pricing/details/media-services/#overview).
  
## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Carga, codificación y streaming de vídeos con Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Carga, codificación y transmisión de vídeos con REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: Node.js](stream-files-nodejs-quickstart.md)
