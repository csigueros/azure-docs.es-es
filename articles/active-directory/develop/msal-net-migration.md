---
title: Migración a MSAL.NET y Microsoft.Identity.Web
titleSuffix: Microsoft identity platform
description: Vea por qué y cómo migrar de la Biblioteca de autenticación de Azure AD para .NET (ADAL.NET) a la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5a3dd6265c9fbefb85cf72b80473538983c682b7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214710"
---
# <a name="migrating-applications-to-msalnet-or-microsoftidentityweb"></a>Migración de aplicaciones a MSAL.NET o Microsoft.Identity.Web

## <a name="why-migrate-to-msalnet-or-microsoftidentityweb"></a>Por qué migrar a MSAL.NET o a Microsoft.Identity.Web

Tanto la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET) como la Biblioteca de autenticación de Azure AD para .NET (ADAL.NET) se usan para autenticar entidades de Azure AD y solicitar tokens a Azure AD. Hasta ahora, la mayoría de los desarrolladores ha solicitado los tokens de la plataforma de Azure AD para desarrolladores (v1.0) usando la Biblioteca de autenticación de Azure AD (ADAL). Estos tokens se usan para autenticar las identidades (cuentas profesionales y educativas) de Azure AD. 

MSAL incluye ventajas respecto a ADAL. A continuación se enumeran algunas de estas ventajas:

- Puede autenticar un conjunto más amplio de identidades de Microsoft: cuentas profesionales o educativas, cuentas personales de Microsoft y cuentas sociales o locales con Azure AD B2C.
- Los usuarios obtendrán la mejor experiencia de inicio de sesión único.
- Las aplicaciones pueden habilitar el consentimiento incremental y el acceso condicional.
- Innovación continua en términos de seguridad y resistencia.
- Las aplicaciones implementan los procedimientos recomendados en términos de resistencia y seguridad.

**MSAL.NET o Microsoft.Identity.Web ahora son las bibliotecas de autenticación recomendadas para usarlas con la Plataforma de identidad de Microsoft**. No se implementarán nuevas características en ADAL.NET. Todos los esfuerzos se centran en mejorar MSAL. Para obtener información más detallada, vea el anuncio: [Actualización de las aplicaciones para usar la Biblioteca de autenticación de Microsoft y Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>Necesidad de migrar a MSAL.NET o a Microsoft.Identity.Web

Antes de profundizar en los detalles que diferencian a MSAL.NET de ADAL.NET, compruebe si desea usar MSAL.NET o una abstracción de nivel superior, como [Microsoft.Identity.Web](microsoft-identity-web.md).

Para obtener información detallada sobre el siguiente árbol de decisión, lea [¿MSAL.NET o Microsoft.Identity.Web?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-or-Microsoft.Identity.Web)

!["Diagrama de bloques en el que se explica cómo saber si necesita usar MSAL.NET, Microsoft.Identity.Web o ambos al migrar desde ADAL.NET"](media/msal-net-migration/decision-diagram.png)

<!-- 1P
## Examples of 1P Migrations

[See examples](https://identitydivision.visualstudio.com/DevEx/_wiki/wikis/DevEx.wiki/20413/1P-ADAL.NET-to-MSAL.NET-migration-examples) of other 1P teams who have already, or are currently, migrating from ADAL to one of the MSAL+ solutions above. See their code, and in some cases read about their migration story.
 -->
 
## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las [aplicaciones cliente públicas y confidenciales](msal-client-applications.md).
- Vea cómo [migrar aplicaciones cliente confidenciales basadas en ASP.NET MVC o .NET clásico de ADAL.NET a MSAL.NET](msal-net-migration-confidential-client.md).
- Vea cómo [migrar aplicaciones cliente públicas basadas en .NET o .NET clásico de ADAL.NET a MSAL.NET](msal-net-migration-public-client.md).
- Obtenga más información sobre las [Diferencias entre las aplicaciones de ADAL.NET y MSAL.NET](msal-net-differences-adal-net.md).
- Vea cómo migrar aplicaciones cliente confidenciales basadas en ASP.NET Core de ADAL.NET a Microsoft.Identity.Web:
  -  [Aplicaciones web](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apps#migrating-from-previous-versions--adding-authentication)
  -  [API web](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apis)
