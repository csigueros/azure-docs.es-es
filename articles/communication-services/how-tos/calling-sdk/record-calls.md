---
title: Administración de la grabación de llamadas en el cliente
titleSuffix: An Azure Communication Services how-to guide
description: Use SDK de Azure Communication Services para administrar la grabación de llamadas en el cliente.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.topic: how-to
ms.subservice: calling
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 5ef33ef46b208f66fa7d31b20bb603cd543880f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699644"
---
# <a name="manage-call-recording-on-the-client"></a>Administración de la grabación de llamadas en el cliente

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

La [grabación de llamadas](../../concepts/voice-video-calling/call-recording.md) permite que los usuarios graben las llamadas que hacen con Azure Communication Services. Aquí aprenderemos a administrar la grabación en el lado cliente. Para que esto funcione, deberá configurar la grabación del [lado servidor](../../quickstarts/voice-video-calling/call-recording-sample.md).

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Un token de acceso de usuario para habilitar el cliente de llamada. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md).
- Opcional: Realice el inicio rápido para [agregar llamadas de voz a la aplicación](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Record Calls Client-side JavaScript](./includes/record-calls/record-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Record Calls Client-side Android](./includes/record-calls/record-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Record Calls Client-side iOS](./includes/record-calls/record-calls-ios.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes
- [Aprenda a administrar llamadas](./manage-calls.md)
- [Aprenda a administrar vídeo](./manage-video.md)
- [Aprenda a transcribir llamadas](./call-transcription.md)