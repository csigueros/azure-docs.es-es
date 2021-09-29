---
title: Protocolos de autenticación de la Plataforma de identidad de Microsoft
description: Información general sobre los protocolos de autenticación compatibles con la Plataforma de identidad de Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: d489dbad512af238601636e9f0c4f144f9b2216d
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153945"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocolos de autenticación de la Plataforma de identidad de Microsoft

La Plataforma de identidad de Microsoft admite varios de los protocolos de autenticación y autorización más usados. En los temas de esta sección se describen los protocolos admitidos y su implementación en la Plataforma de identidad de Microsoft. En los temas se incluye una revisión de los tipos de notificación compatibles, una introducción al uso de los metadatos de federación, documentación de referencia detallada sobre los protocolos OAuth 2.0. y SAML 2.0, y una sección de solución de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artículos y referencia de protocolos de autenticación

* [Información importante acerca de la cadencia de sustitución de clave en la Plataforma de identidad de Microsoft](active-directory-signing-key-rollover.md): obtenga información sobre la cadencia de sustitución de clave de firma de la Plataforma de identidad de Microsoft, los cambios que puede realizar para actualizar la clave automáticamente y una explicación sobre cómo actualizar los escenarios de aplicación más comunes.
* [Tipos de token y notificación compatibles](id-tokens.md): obtenga información sobre las notificaciones en los tokens que emite la Plataforma de identidad de Microsoft.
* [OAuth 2.0 en la Plataforma de identidad de Microsoft](v2-oauth2-auth-code-flow.md): obtenga información sobre la implementación de OAuth 2.0 en la Plataforma de identidad de Microsoft.
* [OpenID Connect 1.0](v2-protocols-oidc.md) : obtenga información sobre cómo usar el protocolo de autorización OAuth 2.0 para la autenticación.
* [Llamadas de servicio a servicio con credenciales de cliente](v2-oauth2-client-creds-grant-flow.md): aprenda a usar el flujo de concesión de credenciales de cliente de OAuth 2.0 para las llamadas de servicio a servicio.
* [Llamadas de servicio a servicio con el flujo En nombre de](v2-oauth2-on-behalf-of-flow.md): aprenda a usar el flujo "En nombre de" de OAuth 2.0 en las llamadas de servicio a servicio.
* [Referencia del protocolo SAML](active-directory-saml-protocol-reference.md) : obtenga información sobre los perfiles SAML de inicio de sesión único y de cierre de sesión único de la Plataforma de identidad de Microsoft.

## <a name="see-also"></a>Consulte también

* [Introducción a la Plataforma de identidad de Microsoft](v2-overview.md)
* [Ejemplos de código de Azure Active Directory](sample-v2-code.md)
