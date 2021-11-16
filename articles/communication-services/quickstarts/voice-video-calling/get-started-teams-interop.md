---
title: 'Inicio rápido: Interoperabilidad con Teams en Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: En este inicio rápido aprenderá a unirse a una reunión de Teams con el SDK de llamada de Azure Communication Services.
author: chpalm
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4ab25d1105f8dcad40599a8096ea114c31dfa61a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488338"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Inicio rápido: Incorporación de una aplicación de llamadas a una reunión de Teams

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

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
- Más información sobre la [llamada a las funcionalidades de SDK](./getting-started-with-calling.md)
- Más información sobre [cómo funciona la llamada](../../concepts/voice-video-calling/about-call-types.md)