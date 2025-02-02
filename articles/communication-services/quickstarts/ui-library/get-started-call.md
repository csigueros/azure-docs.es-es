---
title: 'Inicio rápido: Incorporación de la composición de llamadas a una aplicación'
titleSuffix: An Azure Communication Services quickstart
description: En este inicio rápido, aprenderá a trabajar con la composición de llamadas de la biblioteca de la interfaz de usuario.
author: jorgegarc
ms.author: jorgegarc
ms.date: 10/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: a8bd62d55dfe4cf85b4af01bb93d9a4ea44a0a40
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133999"
---
# <a name="quickstart-get-started-with-calling-composite"></a>Inicio rápido: Introducción a la composición de llamadas 

Comience a usar Azure Communication Services mediante la biblioteca de la interfaz de usuario para integrar rápidamente las experiencias de comunicación en sus aplicaciones. En este inicio rápido, aprenderá a integrar la composición de llamadas en la aplicación Android o iOS.

La biblioteca de la interfaz de usuario representará una experiencia de comunicación completa directamente en la aplicación. Se encarga de conectarse a la llamada deseada y configurarla en segundo plano. Como desarrollador, solo tiene que preocuparse de dónde quiere, en su experiencia, que se inicie la experiencia de comunicación. La composición acompaña al usuario a lo largo de la configuración de sus dispositivos, de la incorporación a la llamada y de la participación en ella, así como de la representación de otros participantes.

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

::: zone pivot="platform-android"
[!INCLUDE [UI Library with Android](./includes/get-started-call/android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [UI Library with iOS](./includes/get-started-call/ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y quitar una suscripción a Communication Services, puede eliminar el recurso o grupo de recursos.

Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él.
Obtenga más información sobre la [limpieza de recursos](../create-communication-resource.md#clean-up-resources).
