---
title: Procedimiento para unir dos o más archivos de vídeo con .NET | Microsoft Docs
description: En este artículo se muestra cómo unir dos o más archivos de vídeo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: fcf3ef3c122639ee0d2c9fa2664fe1b165195a8f
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493723"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Procedimiento para unir dos o más archivos de vídeo con .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Unión de dos o más archivos de vídeo

En el ejemplo siguiente se ilustra cómo generar un valor preestablecido para unir dos o más archivos de vídeo. El escenario más frecuente es cuando desea agregar un encabezado o un finalizador al vídeo principal.

> [!NOTE]
> Los archivos de vídeo que se editan juntos deben compartir propiedades (resolución de vídeo, velocidad de fotogramas, número de pistas de audio, etc.). Procure no para combinar vídeos de distintas velocidades de fotogramas o con un número diferente de pistas de audio.

## <a name="prerequisites"></a>Requisitos previos

Clone o descargue los [ejemplos de .NET de Media Services](https://github.com/Azure-Samples/media-services-v3-dotnet/). 

## <a name="example"></a>Ejemplo

Busque el código que muestra cómo unir archivos de vídeo en la [carpeta EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/Encoding_StitchTwoAssets/Program.cs).
