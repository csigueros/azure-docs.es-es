---
title: Aumento de la resistencia con la administración de credenciales en Azure Active Directory
description: Guía para arquitectos y administradores de TI sobre la compilación de una estrategia de credenciales resistente.
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
ms.openlocfilehash: fd03ce16f0695c23f30146cb8099eb67614982dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754414"
---
# <a name="build-resilience-with-credential-management"></a>Aumento de la resistencia con la administración de credenciales

Cuando se presenta una credencial para Azure AD en una solicitud de token, hay varias dependencias que deben estar disponibles para la validación. El primer factor de autenticación se basa en la autenticación de Azure AD y, en algunos casos, en la infraestructura local. Para más información sobre las arquitecturas de autenticación híbrida, consulte [Aumento de la resistencia en la infraestructura híbrida](resilience-in-hybrid.md). 

Si implementa un segundo factor, las dependencias del segundo factor se agregan a las de la primera. Por ejemplo, si el primer factor es a través de PTA y el segundo factor es SMS, las dependencias son las siguientes:

* Servicios de autenticación de Azure AD

* Servicio Azure MFA

* Infraestructura local

* Operador telefónico

* Dispositivo del usuario (no ilustrado)

 
![Imagen de los métodos de autenticación y las dependencias](./media/resilience-in-credentials/admin-resilience-credentials.png)

La estrategia de credenciales debe tener en cuenta las dependencias de cada tipo de autenticación y aprovisionar métodos que eviten un único punto de error. 

Dado que los métodos de autenticación tienen diferentes dependencias, es una buena idea permitir que los usuarios se registren para tantas opciones de segundo factor como sea posible. Asegúrese de incluir los segundos factores con diferentes dependencias, si es posible. Por ejemplo, las llamadas de voz y los SMS como segundos factores comparten las mismas dependencias, por lo que tenerlos como únicas opciones no mitiga el riesgo.

La estrategia de credenciales más resistente consiste en usar la autenticación sin contraseña. Las claves de seguridad de Windows Hello para empresas y FIDO 2.0 tienen menos dependencias que la autenticación sólida con dos factores independientes. Las claves de seguridad de la aplicación Microsoft Authenticator, Windows Hello para empresas y Fido 2.0 son las más seguras. 

En el caso de los segundos factores, la aplicación Microsoft Authenticator u otras aplicaciones de autenticador que usan el código de acceso de un solo uso (TOTP) de duración definida o los tokens de hardware OATH tienen el menor número de dependencias y, por lo tanto, son más resistentes.

## <a name="how-do-multiple-credentials-help-resilience"></a>¿Cómo ayudan varias credenciales a la resistencia?

El aprovisionamiento de varios tipos de credenciales ofrece a los usuarios opciones que se adaptan a las preferencias y restricciones de sus entornos. Como resultado, la autenticación interactiva en la que se solicita a los usuarios la autenticación multifactor es más resistente a las dependencias específicas que no están disponibles en el momento de la solicitud. Puede [optimizar los mensajes de reautenticación para Multi-Factor Authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Además de la resistencia de usuario individual descrita anteriormente, las empresas deben planear las contingencias para interrupciones a gran escala, como errores operativos que introducen una configuración incorrectamente, un desastre natural o una interrupción de recursos de toda la empresa en un servicio de federación local (especialmente cuando se usa para la autenticación multifactor). 

## <a name="how-do-i-implement-resilient-credentials"></a>¿Cómo implementar credenciales resistentes?

* Implemente [credenciales sin contraseña](../authentication/howto-authentication-passwordless-deployment.md), como las claves de seguridad de Windows Hello para empresas, autenticación por teléfono y FIDO2, para reducir las dependencias.

* Implemente la [aplicación Microsoft Authenticator](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc) como segundo factor.

* Active la [sincronización de hash de contraseña](../hybrid/whatis-phs.md) para las cuentas híbridas que se sincronizan desde Windows Server Active Directory. Esta opción se puede habilitar junto con servicios de federación como AD FS y proporciona un plan alternativo en caso de que se produzca un error en el servicio de federación.

* [Analice el uso de métodos de autenticación multifactor](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) para mejorar la experiencia de los usuarios.

* [Implemente una estrategia de control de acceso resistente](../authentication/concept-resilient-controls.md).

## <a name="next-steps"></a>Pasos siguientes
Recursos de resistencia para administradores y arquitectos
 
* [Aumento de la resistencia con estados de dispositivos](resilience-with-device-states.md)

* [Aumento de la resistencia mediante la evaluación continua de acceso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Aumento de la resistencia en la autenticación de usuario externo](resilience-b2b-authentication.md)

* [Aumento de la resistencia en la autenticación híbrida](resilience-in-hybrid.md)

* [Aumento de la resistencia en el acceso a la aplicación con Application Proxy](resilience-on-premises-access.md)

Recursos de resistencia para desarrolladores

* [Aumento de la resistencia IAM en las aplicaciones](resilience-app-development-overview.md)

* [Aumento de la resistencia en los sistemas CIAM](resilience-b2c.md)