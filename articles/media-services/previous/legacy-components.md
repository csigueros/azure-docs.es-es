---
title: Componentes heredados de Azure Media Services | Microsoft Docs
description: En este tema se describen los componentes heredados de Azure Media Services.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: d86a77e724bffeaea6cb39ffc1a20e8737ef6d81
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835543"
---
# <a name="azure-media-services-legacy-components"></a>Componentes heredados de Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Con el tiempo, se mejoran los componentes de servicio multimedia y se retiran los componentes heredados. Este artículo le ayuda a migrar la aplicación de un componente heredado a un componente actual.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planes de retirada de componentes heredados y guía de migración

Los procesadores de multimedia *Windows Azure Media Encoder* (WAME) y *Azure Media Encoder* (AME) han quedado en desuso.

* [Migración de Windows Azure Media Encoder a Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migración de Azure Media Encoder a Media Encoder Standard](migrate-azure-media-encoder.md)

Los siguientes procesadores de multimedia de Media Analytics están en desuso o a punto de estarlo:

 
| **Nombre de procesador de multimedia** | **Fecha de retirada** | **Notas adicionales** |
| --- | --- | ---|
| Azure Media Indexer | 1 de enero de 2020 | Este procesador de multimedia se reemplazará por el [modo básico AudioAnalyzerPreset de Media Services v3](../latest/analyze-video-audio-files-concept.md). Para más información, consulte [Migración de Azure Media Indexer 2 a Azure Video Analyzer for Media](migrate-indexer-v1-v2.md) (anteriormente Video Indexer). |
| Azure Media Indexer 2 | 1 de marzo de 2023 | Este procesador de multimedia se reemplazará por el [modo básico AudioAnalyzerPreset de Media Services v3](../latest/analyze-video-audio-files-concept.md). Para más información, consulte [Migración de Azure Media Indexer 2 a Azure Video Analyzer for Media](migrate-indexer-v1-v2.md) (anteriormente Video Indexer). |
| Detección de movimiento | 1 de junio de 2020|No hay planes de sustitución en este momento. |
| Resumen de vídeo |1 de junio de 2020|No hay planes de sustitución en este momento.|
| Reconocimiento óptico de caracteres de vídeo | 1 de junio de 2020 |Azure Video Analyzer for Media ha reemplazado a este procesador de multimedia. Además, considere la posibilidad de usar la [API de Azure Media Services v3](../latest/analyze-video-audio-files-concept.md). <br/>Consulte [Comparación de los valores preestablecidos de Azure Media Services v3 y Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Detector de caras | 1 de junio de 2020 | Azure Video Analyzer for Media ha reemplazado a este procesador de multimedia. Además, considere la posibilidad de usar la [API de Azure Media Services v3](../latest/analyze-video-audio-files-concept.md). <br/>Consulte [Comparación de los valores preestablecidos de Azure Media Services v3 y Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1 de junio de 2020 |Azure Video Analyzer for Media ha reemplazado a este procesador de multimedia. Además, considere la posibilidad de usar la [API de Azure Media Services v3](../latest/analyze-video-audio-files-concept.md). <br/>Consulte [Comparación de los valores preestablecidos de Azure Media Services v3 y Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Flujo de trabajo del Codificador multimedia | 29 de febrero de 2024 | La API de AMS v2 ya no es compatible con el codificador Premium. Si anteriormente usaba el codificador Premium basado en flujos de trabajo para la codificación de HEVC, debe migrar al [nuevo codificador estándar v3](../latest/encode-media-encoder-standard-formats-reference.md), que es compatible con la codificación de HEVC. <br/> Si necesita las características avanzadas de flujo de trabajo del codificador Premium, le recomendamos que empiece a usar la codificación avanzada de Azure de algún asociado, como [Imagine Communications](https://imaginecommunications.com/), [Telestream](https://telestream.net) o [Bitmovin](https://bitmovin.com). |

## <a name="next-steps"></a>Pasos siguientes

[Guía de migración para mover de Media Services v2 a v3](../latest/migrate-v-2-v-3-migration-introduction.md)
