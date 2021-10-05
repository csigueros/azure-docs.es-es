---
title: Visualización del estado de transcripción de llamadas en el cliente
titleSuffix: An Azure Communication Services how-to guide
description: Uso del SDK de Azure Communication Services para mostrar el estado de transcripción de llamadas
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 9304f7905b12362f8026e59af8cf294ed76fc42e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635748"
---
# <a name="display-call-transcription-state-on-the-client"></a>Visualización del estado de transcripción de llamadas en el cliente

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Al usar la transcripción de llamadas, es posible que desee que los usuarios sepan que se está transcribiendo una llamada. Esta es la manera de hacerlo.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Un token de acceso de usuario para habilitar el cliente de llamada. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md).
- Opcional: Realice el inicio rápido para [agregar llamadas de voz a la aplicación](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Call transcription client-side Android](./includes/call-transcription/call-transcription-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Call transcription client-side iOS](./includes/call-transcription/call-transcription-ios.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes
- [Aprenda a administrar vídeos](./manage-video.md)
- [Aprenda a administrar llamadas](./manage-calls.md)
- [Aprenda a grabar llamadas](./record-calls.md)