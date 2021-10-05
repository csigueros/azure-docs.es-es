---
title: Administración de llamadas
titleSuffix: An Azure Communication Services how-to guide
description: Use SDK de Azure Communication Services para administrar llamadas.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4abf92e2d3003d8944ea75c411bc9699716e4684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699648"
---
# <a name="manage-calls"></a>Administración de llamadas

Aprenda a administrar llamadas con los SDK de Azure Communication Services. Aquí se va a aprender a realizar llamadas y a administrar sus participantes y sus propiedades.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- `User Access Token` para habilitar el cliente de llamada. Para más información sobre [cómo obtener `User Access Token`](../../quickstarts/access-tokens.md)
- Opcional: Complete el inicio rápido para [empezar a agregar llamadas a su aplicación](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Manage Calls JavaScript](./includes/manage-calls/manage-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Calls Android](./includes/manage-calls/manage-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Calls iOS](./includes/manage-calls/manage-calls-ios.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Manage Calls Windows](./includes/manage-calls/manage-calls-windows.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes
- [Aprenda a administrar vídeo](./manage-video.md)
- [Aprenda a grabar llamadas](./record-calls.md)
- [Aprenda a transcribir llamadas](./call-transcription.md)