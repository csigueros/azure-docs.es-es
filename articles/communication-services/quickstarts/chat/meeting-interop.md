---
title: Introducción a la interoperabilidad con Teams en Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: En esta guía de inicio rápido aprenderá a unirse a una reunión de Teams con el SDK de chat de Azure Communication Services.
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 09a4f044ee7084cec31c61c3a00a6e3a42d688f0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442897"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Inicio rápido: Incorporación de una aplicación de chat a una reunión de Teams

> [!IMPORTANT]
> Para habilitar o deshabilitar la [interoperabilidad de los inquilinos de equipos](../../concepts/teams-interop.md), complete [este formulario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Comience a usar Azure Communication Services mediante la conexión de la solución de chat a Microsoft Teams. 

::: zone pivot="platform-web"
[!INCLUDE [Teams interop with JavaScript SDK](./includes/meeting-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Teams interop with iOS SDK](./includes/meeting-interop-swift.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Teams interop with Android SDK](./includes/meeting-interop-android.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y quitar una suscripción a Communication Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él. Obtenga más información sobre la [limpieza de recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos.

- Consulte nuestro [ejemplo de elementos principales de un chat](../../samples/chat-hero-sample.md).
- Más información sobre el [funcionamiento del chat](../../concepts/chat/concepts.md).
