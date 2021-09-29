---
title: Aumento de la resistencia mediante el uso de estados de dispositivos en Azure Active Directory
description: Guía para que los arquitectos y los administradores de TI aumenten la resistencia mediante el uso de estados de dispositivos
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d9c22974ec3452df9597999eb62569a08eb85dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754405"
---
# <a name="build-resilience-with-device-states"></a>Aumento de la resistencia con estados de dispositivos

Al habilitar los [estados de dispositivos](../devices/overview.md) con Azure AD, los administradores pueden crear [directivas de acceso condicional](../conditional-access/overview.md) que controlen el acceso a las aplicaciones en función del estado del dispositivo. La ventaja agregada de los dispositivos es que satisface los requisitos de autenticación seguros para el acceso a los recursos, lo que reduce las solicitudes de autenticación de MFA adicionales y mejora la resistencia. 

El siguiente diagrama de flujo presenta las distintas formas de incorporar dispositivos en Azure AD que habilitan los estados de los dispositivos. Puede usar más de una en su organización.

![diagrama de flujo para elegir estados de dispositivos](./media/resilience-with-device-states/admin-resilience-devices.png)

Al utilizar [estados de dispositivos](../devices/overview.md), en la mayoría de los casos los usuarios experimentarán un inicio de sesión único a través de un [token de actualización principal](../devices/concept-primary-refresh-token.md) (PRT). El PRT contiene notificaciones sobre el usuario y el dispositivo y puede usarse para obtener tokens de autenticación para tener acceso a las aplicaciones desde el dispositivo. El PRT es válido durante 14 días y se renueva continuamente siempre y cuando el usuario utilice el dispositivo activamente, proporcionando a los usuarios una experiencia resistente. Un PRT también puede recibir una notificación de autenticación multifactor de diferentes formas. Para obtener más información, consulte [¿Cuándo recibe un PRT una notificación de MFA?](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>¿Cómo ayudan los estados de los dispositivos?

Cuando se usa un PRT para solicitar acceso a una aplicación, sus notificaciones de dispositivo, sesión y MFA son de confianza para Azure AD. Cuando los administradores crean directivas que requieren un control basado en dispositivo o un control de autenticación multifactor, el requisito de la directiva se puede cumplir a través de su estado de dispositivo sin intentar la autenticación multifactor. Los usuarios no verán mensajes adicionales de autenticación multifactor en el mismo dispositivo. Esto aumenta la resistencia ante una interrupción del servicio de Azure MFA o sus dependencias, como los proveedores de telecomunicaciones locales.

## <a name="how-do-i-implement-device-states"></a>¿Cómo se implementan los estados de los dispositivos?

* Habilite la [unión a Azure AD híbrido](../devices/hybrid-azuread-join-plan.md) y la [unión a Azure AD](../devices/azureadjoin-plan.md) para dispositivos Windows propiedad de la empresa y requiera que se unan, si es posible. Si no es posible, requiera que se registren.

  Si hay versiones anteriores de Windows en la organización, actualice esos dispositivos para usar Windows 10.

* Estandarice el acceso al explorador del usuario para usar [Microsoft Edge](/deployedge/microsoft-edge-security-identity) o Google Chrome con [extensiones](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) [compatibles](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) que habiliten el inicio de sesión único de conexión directa a aplicaciones web mediante el PRT.

* En el caso de los dispositivos iOS y Android de propiedad personal o de la empresa, implemente la [aplicación Microsoft Authenticator](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc). Además de las funcionalidades de inicio de sesión con autenticación multifactor y sin contraseña, la aplicación Microsoft Authenticator habilitará el inicio de sesión único en la aplicación nativa a través de la [autenticación con intermediación](../develop/msal-android-single-sign-on.md) con menos solicitudes de autenticación para los usuarios finales.

* En el caso de los dispositivos iOS y Android de propiedad personal o de la empresa, use la [administración de aplicaciones móviles](/mem/intune/apps/app-management) para tener acceso de forma segura a los recursos de la empresa con menos solicitudes de autenticación. 

* [Use el complemento de Microsoft Enterprise SSO para dispositivos Apple (versión preliminar)](../develop/apple-sso-plugin.md). Esto registra el dispositivo y proporciona SSO en todas las aplicaciones de Azure AD nativas y del explorador. 

## <a name="next-steps"></a>Pasos siguientes
Recursos de resistencia para administradores y arquitectos
 
* [Aumento de la resistencia con la administración de credenciales](resilience-in-credentials.md)

* [Aumento de la resistencia mediante la evaluación continua de acceso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Aumento de la resistencia en la autenticación de usuario externo](resilience-b2b-authentication.md)

* [Aumento de la resistencia en la autenticación híbrida](resilience-in-hybrid.md)

* [Aumento de la resistencia en el acceso a la aplicación con Application Proxy](resilience-on-premises-access.md)


Recursos de resistencia para desarrolladores

* [Aumento de la resistencia IAM en las aplicaciones](resilience-app-development-overview.md)

* [Aumento de la resistencia en los sistemas CIAM](resilience-b2c.md)