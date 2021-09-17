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
ms.date: 08/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b6537547db2bb3371b6d7b4cd36ebf9ffc48530c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429845"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>Uso del valor predeterminado de codificación en función del contenido

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Introducción
El [valor predeterminado de codificación en función del contenido](encode-content-aware-how-to.md) prepara el contenido de mejor forma para su entrega mediante un [streaming con velocidad de bits adaptable](encode-autogen-bitrate-ladder.md), donde los vídeos deben codificar a varias velocidades de bits. Este valor predeterminado tiene incorporada una lógica personalizada que permite al codificador buscar el mejor valor de velocidad de bits posible para una resolución determinada, sin requerir un análisis de cálculo exhaustivo. El valor predeterminado define automáticamente el número óptimo de capas, la velocidad de bits adecuada y la configuración de resolución para la entrega a través del streaming adaptable. 

En comparación con el valor predeterminado de streaming adaptable, la codificación en función del contenido es más eficaz en vídeos de una complejidad baja y media, donde los archivos de salida tendrán una velocidad de bits inferior, pero con una calidad que ofrezca una buena experiencia de visualización.

## <a name="use-the-content-aware-encoding-preset"></a>Uso del valor predeterminado de codificación en función del contenido
Puede crear transformaciones que usen este valor preestablecido como se indica a continuación.

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

Consulte la sección [Pasos siguientes](#next-steps) para ver otros tutoriales que usan salidas para la transformación. El recurso de la salida se puede entregar desde los puntos de conexión de streaming de Media Services en protocolos como MPEG-DASH y HLS (como se muestra en los tutoriales).

> [!NOTE]
> Los trabajos de codificación que usan el valor predeterminado `ContentAwareEncoding` se facturan únicamente en función de los minutos de salida. AMS usa la codificación de dos pasos y no hay ningún cargo extra derivado del uso de ninguno de los valores preestablecidos más allá de lo que aparece en nuestra [página de precios](https://azure.microsoft.com/pricing/details/media-services/#overview).
  
## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Carga, codificación y streaming de vídeos con Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Carga, codificación y transmisión de vídeos con REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: Node.js](stream-files-nodejs-quickstart.md)
