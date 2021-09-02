---
title: Componentes heredados de Azure Media Services | Microsoft Docs
description: En este tema se describen los componentes heredados de Azure Media Services.
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
ms.openlocfilehash: 588eb1f93fa953a9051b4e3228980217c63ea27b
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719393"
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
| Azure Media Indexer 2 | 1 de enero de 2020 | Este procesador de multimedia se reemplazará por el [modo básico AudioAnalyzerPreset de Media Services v3](../latest/analyze-video-audio-files-concept.md). Para más información, consulte [Migración de Azure Media Indexer 2 a Azure Video Analyzer for Media (anteriormente Video Indexer)](migrate-indexer-v1-v2.md). |
| Azure Media Indexer | 1 de marzo de 2023 | Este procesador de multimedia se reemplazará por el [modo básico AudioAnalyzerPreset de Media Services v3](../latest/analyze-video-audio-files-concept.md). Para más información, consulte [Migración de Azure Media Indexer 2 a Azure Video Analyzer for Media](migrate-indexer-v1-v2.md). |
| Detección de movimiento | 1 de junio de 2020|No hay planes de sustitución en este momento. |
| Resumen de vídeo |1 de junio de 2020|No hay planes de sustitución en este momento.|
| Reconocimiento óptico de caracteres de vídeo | 1 de junio de 2020 |Azure Video Analyzer for Media ha reemplazado a este procesador de multimedia. Además, considere la posibilidad de usar la [API de Azure Media Services v3](../latest/analyze-video-audio-files-concept.md). <br/>Consulte [Comparación de los valores preestablecidos de Azure Media Services v3 y Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Detector de caras | 1 de junio de 2020 | Azure Video Analyzer for Media ha reemplazado a este procesador de multimedia. Además, considere la posibilidad de usar la [API de Azure Media Services v3](../latest/analyze-video-audio-files-concept.md). <br/>Consulte [Comparación de los valores preestablecidos de Azure Media Services v3 y Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1 de junio de 2020 |Azure Video Analyzer for Media ha reemplazado a este procesador de multimedia. Además, considere la posibilidad de usar la [API de Azure Media Services v3](../latest/analyze-video-audio-files-concept.md). <br/>Consulte [Comparación de los valores preestablecidos de Azure Media Services v3 y Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Pasos siguientes

[Guía de migración para mover de Media Services v2 a v3](../latest/migrate-v-2-v-3-migration-introduction.md)
