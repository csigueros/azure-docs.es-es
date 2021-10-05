---
title: Administración de vídeo durante llamadas
titleSuffix: An Azure Communication Services how-to guide
description: Use SDK de Azure Communication Services para administrar videollamadas.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 536dc2233ca0ae0773c9e953d35bee6ee4a4da40
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699645"
---
# <a name="manage-video-during-calls"></a>Administración de vídeo durante llamadas

Aprenda a administrar videollamadas con los SDK de Azure Communication Services. Aquí se va a aprender a administrar la recepción y el envío de vídeo en una llamada.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Un token de acceso de usuario para habilitar el cliente de llamada. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md).
- Opcional: realice el inicio rápido para [agregar llamadas de voz a la aplicación](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Manage Video Calls JavaScript](./includes/manage-video/manage-video-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Video Calls Android](./includes/manage-video/manage-video-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Video Calls iOS](./includes/manage-video/manage-video-ios.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes
- [Aprenda a administrar llamadas](./manage-calls.md)
- [Aprenda a grabar llamadas](./record-calls.md)
- [Aprenda a transcribir llamadas](./call-transcription.md)
