---
title: Migración de Indexer v1 y v2 a Video Indexer de Azure Media Services | Microsoft Docs
description: En este tema se aborda cómo migrar de Azure Media Indexer v1 y v2 a Video Indexer de Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2021
ms.author: inhenkel
ms.openlocfilehash: 3a2871bd42e6b4da9ec20dc918c5c0ab79ed1a64
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721562"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-analyzer-for-media"></a>Migración de Media Indexer y Media Indexer 2 a Video Analyzer for Media 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Se recomienda que los clientes migren de los indizadores v1 y v2 a usar el [modo básico AudioAnalyzerPreset de Media Services v3](../latest/analyze-video-audio-files-concept.md). Tanto el procesador de multimedia [Azure Media Indexer](media-services-index-content.md) como los procesadores de multimedia [Azure Media Indexer 2 Preview](./legacy-components.md) se están retirando. Para ver las fechas de retirada, consulte el tema [componentes heredados](legacy-components.md).

Azure Video Analyzer for Media se basa en Azure Media Analytics, Azure Cognitive Search y Cognitive Services (como Face API, Microsoft Translator, Computer Vision API y el servicio Habla personalizada). Permite extraer información de los vídeos mediante los modelos de vídeo y audio de Video Analyzer for Media. Para ver en qué escenarios se puede usar Video Analyzer for Media, qué características ofrece y cómo empezar, consulte los [modelos de vídeo y audio de Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md). 

También puede extraer la información de los archivos de audio y vídeo mediante los [valores preestablecidos del analizador de Azure Media Services v3](../latest/analyze-video-audio-files-concept.md) o directamente mediante las [API de Video Indexer for Media](https://api-portal.videoindexer.ai/). Actualmente, hay una superposición entre las características que ofrecen las API de Video Analyzer for Media y las API de Media Services v3.

> [!NOTE]
> Para comprender las diferencias entre los valores preestablecidos de Video Analyzer for Media y de Media Services, consulte el [documento en que se realiza una comparación entre ambos](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md).

En este artículo se describen los pasos para migrar desde Azure Media Indexer y Azure Media Indexer 2 a Video Analyzer for Media.  

## <a name="migration-options"></a>Opciones de migración

|Si requiere  |y luego |
|---|---|
|una solución que proporcione una transcripción de voz a texto para cualquier formato de archivo multimedia en formatos de archivo de subtítulos VTT, SRT o TTML,<br/>así como información de audio adicional, como palabras clave, inferencia de temas, eventos acústicos, diarización de hablantes, traducción y extracción de entidades| actualice las aplicaciones para usar las funcionalidades de Video Analyzer for Media a través de la API REST de Video Analyzer for Media v2 o los valores preestablecidos del analizador de audio de Azure Media Services v3.|
|capacidades de voz a texto| use Cognitive Services Speech API directamente.|  

## <a name="getting-started-with-video-analyzer-for-media"></a>Introducción a Video Analyzer for Media

En la sección siguiente se indican vínculos pertinentes: [¿Cómo empiezo a trabajar con Video Analyzer for Media?](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md#how-can-i-get-started-with-video-analyzer-for-media) 

## <a name="getting-started-with-media-services-v3-apis"></a>Introducción a las API de Media Services v3

La API de Azure Media Services v3 permite extraer la información de los archivos de audio y vídeo mediante los [valores preestablecidos del analizador de Azure Media Services v3](../latest/analyze-video-audio-files-concept.md).

**AudioAnalyzerPreset** permite extraer diversa información de audio de un archivo de audio o vídeo. La salida incluye un archivo VTT o TTML para la transcripción del audio y un archivo JSON (con toda la información de audio adicional). La información de audio incluye palabras clave, indexación de hablantes y análisis de sentimiento de voz. AudioAnalyzerPreset también admite la detección de idioma para idiomas específicos. Para obtener más información, consulte [Transforms](/rest/api/media/transforms/createorupdate#audioanalyzerpreset) (Transformaciones).

### <a name="get-started"></a>Introducción

Para empezar, consulte:

* [Tutorial](../latest/analyze-videos-tutorial.md)
* Ejemplos de AudioAnalyzerPreset: [SDK de Java](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) o [SDK de .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Ejemplos de VideoAnalyzerPreset: [SDK de Java](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) o [SDK de .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Introducción a los servicios de voz de Cognitive Services

[Azure Cognitive Services](../../cognitive-services/index.yml) proporciona un servicio de voz a texto que transcribe secuencias de audio a texto en tiempo real que sus aplicaciones, herramientas o dispositivos pueden usar o mostrar. Puede usar la conversión de voz a texto para [personalizar sus propios modelos acústicos, modelos de lenguaje o modelos de pronunciación](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Para más información, consulte [Conversión de voz a texto de Cognitive Services](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> El servicio de conversión de voz a texto no acepta formatos de archivo de vídeo y solo acepta [determinados formatos de audio](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats). 

Para obtener más información sobre el servicio de texto a voz y cómo empezar, consulte [¿Qué es la conversión de voz a texto?](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>Diferencias conocidas de servicios en desuso

Observará que Video Analyzer for Media, AudioAnalyzerPreset de Azure Media Services v3 y los servicios de Voz de Cognitive Services son más confiables y producen salidas de mejor calidad que los procesadores retirados de Azure Media Indexer 1 y Azure Media Indexer 2.  

Entre las diferencias conocidas se incluyen las siguientes:

* Los servicios de voz de Cognitive Services no admiten la extracción de palabras clave. Sin embargo, Video Analyzer for Media y AudioAnalyzerPreset de Media Services v3 ofrecen un conjunto sólido de palabras clave en formato de archivo JSON.

## <a name="support"></a>Soporte técnico

Puede abrir una incidencia de soporte técnico si se desplaza a la [nueva solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

* [Componentes heredados](legacy-components.md)
* [Página de precios](https://azure.microsoft.com/pricing/details/media-services/#encoding)
