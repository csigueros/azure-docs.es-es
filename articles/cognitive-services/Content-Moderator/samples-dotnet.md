---
title: 'Ejemplos de código: Content Moderator, .NET'
titleSuffix: Azure Cognitive Services
description: Aprenda a usar Content Moderator de Azure Cognitive Services en las aplicaciones de .NET a través del SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 14750f4bfcab2ffcbb9763d9242ed09273fdc6cd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450426"
---
# <a name="content-moderator-net-sdk-samples"></a>Ejemplos del SDK de .NET de Content Moderator

En la lista siguiente se incluyen vínculos a ejemplos de código compilados mediante el SDK de Azure Content Moderator para .NET.

## <a name="moderation"></a>Moderación

- **Moderación de imágenes**: [evalúe una imagen en busca de contenido explícito y para adultos, texto y caras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consulte la [guía de inicio rápido del SDK de .NET](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Imágenes personalizadas**: [modere con listas de imágenes personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consulte la [guía de inicio rápido del SDK de .NET](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Hay un límite máximo de **5 listas de imágenes** y cada una de ellas **no debe superar las 10 000 imágenes**.
>

- **Moderación de texto**: [Pantalla de texto para blasfemias y datos personales](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consulte la [guía de inicio rápido del SDK de .NET](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Términos personalizados**: [modere con listas de términos personalizados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consulte la [guía de inicio rápido del SDK de .NET](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Hay un límite máximo de **5 listas de términos** y cada lista **no debe superar los 10 000 términos**.
>

- **Moderación de vídeos**: [examine un vídeo para buscar contenido explícito y para adultos y obtener resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consulte el [inicio rápido](video-moderation-api.md).

## <a name="review"></a>Revisar

- **Trabajos con imágenes**: [inicie un trabajo de moderación que examine y cree revisiones](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consulte el [inicio rápido](moderation-jobs-quickstart-dotnet.md).
- **Revisiones de imágenes**: [cree revisiones con intervención humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consulte el [inicio rápido](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Revisiones de vídeos**: [cree revisiones de vídeo con intervención humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consulte el [inicio rápido](video-reviews-quickstart-dotnet.md).
- **Revisiones de transcripciones de vídeos**: [cree revisiones de transcripciones de vídeos con intervención humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Consulte el [inicio rápido](video-reviews-quickstart-dotnet.md).

Consulte todos los ejemplos de .NET en los [ejemplos de .NET para Content Moderator en GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).