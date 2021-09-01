---
title: 'Inicio rápido: Interoperabilidad con Teams en Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: En este inicio rápido aprenderá a unirse a una reunión de Teams con el SDK de llamada de Azure Communication Services.
author: chpalm
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 3a9c685794b8d656fb3bc550bba2b05db8961bcd
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250946"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Inicio rápido: Incorporación de una aplicación de llamadas a una reunión de Teams

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

Comience a usar Azure Communication Services mediante la conexión de la solución de llamada a Microsoft Teams con el SDK para JavaScript.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Calling with Windows](./includes/teams-interop/teams-interop-windows.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop/teams-interop-ios.md)]
::: zone-end

La funcionalidad descrita en este documento usa la versión de disponibilidad general de los SDK de Communication Services. La interoperabilidad de Teams requiere la versión beta de los SDK de Communication Services. Se pueden explorar los SDK de la versión beta en la [página de notas de la versión](https://github.com/Azure/Communication/tree/master/releasenotes).

Al ejecutar el paso "Instalación del paquete" con los SDK de la versión beta, modifique la versión del paquete a la versión beta más reciente. Para ello, especifique la versión `@1.0.0-beta.10` (versión en el momento de redactar este artículo) en el nombre del paquete `communication-calling`. No es necesario modificar el comando del paquete `communication-common`. Por ejemplo:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y quitar una suscripción a Communication Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él. Obtenga más información sobre la [limpieza de recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos.

- Consulte nuestro [ejemplo de elementos principales de una llamada](../../samples/calling-hero-sample.md).
- Más información sobre la [llamada a las funcionalidades de SDK](./calling-client-samples.md)
- Más información sobre [cómo funciona la llamada](../../concepts/voice-video-calling/about-call-types.md)
