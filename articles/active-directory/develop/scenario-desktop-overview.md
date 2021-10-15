---
title: Compilación de una aplicación de escritorio que llama a las API web | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a compilar una aplicación de escritorio que llame a API web (información general).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3440fef53d0c133223439830769c916f782fce23
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231903"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Escenario: Aplicación de escritorio que llama a las API web

Aprenda a crear una aplicación de escritorio que llame a las API web.

## <a name="get-started"></a>Introducción

Si aún no lo ha hecho, cree su primera aplicación siguiendo un inicio rápido:

- [Inicio rápido: Adquisición de un token y llamada a Microsoft Graph API desde una aplicación de escritorio de Windows](./quickstart-v2-windows-desktop.md)
- [Inicio rápido: Adquisición de un token y llamada a Microsoft Graph API desde una aplicación de UWP](./quickstart-v2-uwp.md)
- [Inicio rápido: Adquisición de un token y llamada a Microsoft Graph API desde una aplicación nativa de macOS](./quickstart-v2-ios.md)
- [Inicio rápido: Adquisición de un token y llamada a Microsoft Graph API desde una aplicación de Node.js y Electron](./quickstart-v2-nodejs-desktop.md)

## <a name="overview"></a>Información general

Supongamos que escribe una aplicación de escritorio y que desea que los usuarios inicien sesión en la aplicación, así como llamar a alguna API web como Microsoft Graph, otras API de Microsoft o su propia API web. Tiene varias opciones:

- Puede utilizar la adquisición interactiva de tokens:

  - Si la aplicación de escritorio admite controles gráficos; por ejemplo, si es una aplicación Windows.Form, una aplicación WPF o una aplicación nativa de macOS.
  - O si se trata de una aplicación .NET Core y acepta que la interacción de autenticación con Azure Active Directory (Azure AD) suceda en el explorador del sistema.
  - O bien, si se trata de una aplicación de Node.js y Electron, que se ejecuta en una instancia de Chromium.

- En el caso de las aplicaciones hospedadas en Windows, también es posible que las aplicaciones que se ejecutan en equipos unidos a un dominio de Windows o unidos a Azure AD adquieran un token en modo silencioso mediante el uso de la autenticación integrada de Windows.
- Por último, y aunque no es recomendable, puede usar un nombre de usuario y una contraseña en las aplicaciones clientes públicas. Esto sigue siendo necesario en algunos escenarios, como DevOps. Su uso impone restricciones en la aplicación. Por ejemplo, no puede iniciar sesión si el usuario necesita realizar la [autenticación multifactor](../authentication/concept-mfa-howitworks.md) (acceso condicional). Además, la aplicación no se beneficiará del inicio de sesión único (SSO).

  También va en contra de los principios de la autenticación moderna y solo se proporciona por motivos de herencia.

  ![Aplicación de escritorio](media/scenarios/desktop-app.svg)

- Si escribe una herramienta de línea de comandos portátil (probablemente una aplicación .NET Core que se ejecuta en Linux o Mac) y acepta que la autenticación se delegue en el explorador del sistema, podrá usar la autenticación interactiva. .NET Core no proporciona un [explorador web](https://aka.ms/msal-net-uses-web-browser), por lo que la autenticación se produce en el explorador del sistema. De lo contrario, la mejor opción en ese caso es utilizar el flujo de código de dispositivo. Este flujo también se utiliza en las aplicaciones sin un explorador, como las aplicaciones de IoT.

  ![Aplicación sin explorador](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Características específicas

Las aplicaciones de escritorio tienen una serie de características específicas. Dependen principalmente de si la aplicación usa la autenticación interactiva o no.

## <a name="recommended-reading"></a>Lecturas recomendadas

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Registro de la aplicación](scenario-desktop-app-registration.md).
