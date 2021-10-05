---
title: Administre las notificaciones push para llamadas.
titleSuffix: An Azure Communication Services how-to guide
description: Use SDK de Azure Communication Services para habilitar las notificaciones push para las llamadas.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 87e9aa1741808e60af14eb32e1837bc87c280062
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635724"
---
# <a name="enable-push-notifications-for-calls"></a>Habilitación de las notificaciones push para llamadas

Aquí, aprenderemos a habilitar las notificaciones push para las llamadas de Azure Communication Services. Si las configura, los usuarios sabrán cuándo tienen una llamada entrante a la que pueden responder.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Un token de acceso de usuario para habilitar el cliente de llamada. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md).
- Opcional: Realice el inicio rápido para [agregar llamadas de voz a la aplicación](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Enable push notifications Android](./includes/push-notifications/push-notifications-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Enable push notifications iOS](./includes/push-notifications/push-notifications-ios.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes
- [Aprenda a suscribirse a eventos](./events.md)
- [Aprenda a administrar llamadas](./manage-calls.md)
- [Aprenda a administrar vídeos](./manage-video.md)
