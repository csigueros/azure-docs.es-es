---
title: Escenario de intercambio de tokens de la plataforma de identidad de Microsoft con SAML y OIDC/OAuth en Azure Active Directory
description: Conozca los escenarios comunes de intercambio de tokens al trabajar con SAML y OIDC/OAuth en Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.custom: has-adal-ref
ms.openlocfilehash: 527c9bfac3df50d012f3b0a805d0ac4ec1ea9067
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428963"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Escenarios de intercambio de tokens de la plataforma de identidad de Microsoft con SAML y OIDC/OAuth

SAML y OpenID Connect (OIDC)/OAuth son protocolos conocidos que se usan para implementar el inicio de sesión único (SSO). Algunas aplicaciones solo pueden implementar SAML y otras solo pueden implementar OIDC/OAuth. Ambos protocolos emplean tokens para comunicar secretos. Para más información sobre SAML, consulte [Protocolo SAML de inicio de sesión único](single-sign-on-saml-protocol.md). Para más información sobre OIDC/OAuth, consulte [Protocolos OAuth 2.0 y OpenID Connect en la plataforma de identidad de Microsoft](active-directory-v2-protocols.md).

En este artículo se describe un escenario común en el que una aplicación implementa SAML, pero llama a Graph API, que usa OIDC/OAuth. Se proporcionan instrucciones básicas para las personas que trabajan con este escenario.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Escenario: Tiene un token SAML y quiere llamar a Graph API.
Muchas aplicaciones se implementan con SAML. Sin embargo, Graph API emplea los protocolos OIDC/OAuth. Es posible, aunque no resulta sencillo, agregar la funcionalidad OIDC/OAuth a una aplicación de SAML. Una vez que la funcionalidad OAuth está disponible en una aplicación, se puede usar Graph API.

La estrategia general consiste en agregar la pila OIDC/OAuth a la aplicación. Con la aplicación que implementa ambos estándares, puede usar una cookie de sesión. No intercambia un token explícitamente. Registra un usuario con SAML, lo que genera una cookie de sesión. Cuando Graph API invoca un flujo de OAuth, se usa la cookie de sesión para autenticarse. En esta estrategia se da por hecho que se han superado las comprobaciones de acceso condicional y que el usuario está autorizado.

> [!NOTE]
> La biblioteca recomendada para agregar el comportamiento de OIDC/OAuth es la biblioteca de autenticación de Microsoft (MSAL). Para obtener más información sobre MSAL, vea [Introducción a la Biblioteca de autenticación de Microsoft (MSAL)](msal-overview.md). La biblioteca anterior se llamaba Biblioteca de autenticación de Active Directory (ADAL); sin embargo, no se recomienda, ya que MSAL la reemplaza.

## <a name="next-steps"></a>Pasos siguientes
- [Flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md)
