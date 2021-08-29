---
title: Configuración del inicio de sesión único de la aplicación
description: Aprenda a configurar el inicio de sesión único para una aplicación personalizada desarrollada y registrada con Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, has-adal-ref
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 5fa84215c6393fe1bddee3d72dd93951dee06f31
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428995"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Para configurar el inicio de sesión único para una aplicación

El inicio de sesión único federado (SSO) de la aplicación se habilita automáticamente cuando la federación se realiza a través de Azure AD para OpenID Connect, SAML 2.0, o WS-Fed. Si los usuarios finales tienen que iniciar sesión aunque ya tengan una sesión con Azure AD, es probable que la aplicación no esté configurada correctamente.

* Si utiliza ADAL/MSAL, asegúrese de que **PromptBehavior** está establecido en **Auto** y no en **Always**.

* Si está creando una aplicación móvil, tendrá que configurar otras opciones para habilitar el SSO con y sin intermediación.

En el caso de Android, consulte [Habilitación de SSO entre aplicaciones en Android](../azuread-dev/howto-v1-enable-sso-android.md).<br>

En el caso de iOS, consulte [Habilitación del inicio de sesión único entre aplicaciones en iOS ](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Pasos siguientes

[SSO en Azure AD](../manage-apps/what-is-single-sign-on.md)<br>

[Habilitación de SSO entre aplicaciones en Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Habilitación del inicio de sesión único entre aplicaciones en iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integración de aplicaciones con Azure Active Directory](./quickstart-register-app.md)<br>

[Permisos y consentimiento en la plataforma de identidad de Microsoft](./v2-permissions-and-consent.md)<br>

[Microsoft Q&A para Azure AD](/answers/topics/azure-active-directory.html)