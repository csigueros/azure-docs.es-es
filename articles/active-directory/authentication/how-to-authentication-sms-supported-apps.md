---
title: Compatibilidad con aplicaciones para la autenticación basada en SMS en Azure Active Directory
description: Obtenga información sobre qué aplicaciones se admiten para que los usuarios inicien sesión en Azure Active Directory mediante SMS.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: justinha
author: aanjusingh
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f5b0ff9c70c84a1f3adda2e93c74ce388ceb322
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228579"
---
# <a name="app-support-for-sms-based-authentication"></a>Compatibilidad con aplicaciones para la autenticación basada en SMS

La autenticación basada en SMS está disponible para las aplicaciones de Microsoft integradas con la Plataforma de identidad de Microsoft (Azure AD). En la tabla se enumeran algunas de las aplicaciones web y móviles que admiten la autenticación basada en SMS. Si desea agregar o validar alguna aplicación, [póngase en contacto con nosotros](https://feedback.azure.com/forums/169401-azure-active-directory). 

| Aplicación | Aplicación web o de explorador | Aplicación móvil nativa |
| --- |:---:|:---:|
| Office 365 - Microsoft Online Services* | ● |   |
| Microsoft One Note | ● |   |
| Microsoft Teams | ● | ● |
| Portal de la compañía de Microsoft Intune | ● | ● |
| Portal Mis aplicaciones | ● |No disponible|
| Microsoft Forms | ● |No disponible|
| Microsoft Edge | ● |   |
| Microsoft Power BI | ● |   |
| Microsoft Stream | ● |   |
| Microsoft Power Apps | ● |   |
| Microsoft Azure | ● | ● |
| Azure Virtual Desktop | ● |  | 

*_El inicio de sesión por SMS no está disponible para aplicaciones de Office, como Word, Excel, etc., cuando se accede directamente en la web, pero está disponible cuando se accede a través de la [aplicación web Office 365](https://www.office.com)._

Las aplicaciones de Microsoft mencionadas anteriormente admiten el inicio de sesión por SMS porque usan el inicio de sesión de Microsoft Identity (`https://login.microsoftonline.com/`), que permite al usuario escribir el número de teléfono y el código SMS.

## <a name="unsupported-microsoft-apps"></a>Aplicaciones de Microsoft no admitidas

Las aplicaciones de escritorio de Microsoft 365 (Windows o Mac) y aplicaciones web de Microsoft 365 (excepto MS One Note) a las que se accede directamente en la web no admiten el inicio de sesión por SMS. Estas aplicaciones usan el inicio de sesión de Microsoft Office (`https://office.live.com/start/*`) que requiere una contraseña para iniciar sesión.
Por la misma razón, las aplicaciones móviles de Microsoft Office (excepto Microsoft Teams, Portal de empresa de Intune y Microsoft Azure) no admiten el inicio de sesión por SMS.

| Aplicaciones de Microsoft no admitidas| Ejemplos |
| --- | --- |
| Aplicaciones nativas de escritorio de Microsoft | Microsoft Teams, aplicaciones O365, Word, Excel, etc.|
| Aplicaciones nativas móviles de Microsoft (excepto Microsoft Teams, Portal de empresa de Intune y Microsoft Azure) | Outlook, Edge, Power BI, Stream, SharePoint, Power Apps, Word, etc.|
| Aplicaciones web de Microsoft 365 (a las que se accede directamente en la web) | [Outlook](https://outlook.live.com/owa/), [Word](https://office.live.com/start/Word.aspx), [Excel](https://office.live.com/start/Excel.aspx), [PowerPoint](https://office.live.com/start/PowerPoint.aspx), [OneDrive](https://onedrive.live.com/about/signin)|  

## <a name="support-for-non-microsoft-apps"></a>Compatibilidad con aplicaciones que no son de Microsoft 

Para que las aplicaciones que no son de Microsoft sean compatibles con la característica de inicio de sesión por SMS: 
- Integre las aplicaciones web que no son de Microsoft con Azure AD y use la autenticación de Azure AD. Utilice [SAML](../manage-apps/add-application-portal-setup-sso.md) (lenguaje de marcado de aserción de seguridad) u [OIDC](../manage-apps/add-application-portal-setup-oidc-sso.md) (Open ID Connect) para la integración con el inicio de sesión único de Azure AD. 
- Integre las aplicaciones locales que no son de Microsoft en Azure AD mediante el [proxy de aplicación de Azure AD](../app-proxy/application-proxy-add-on-premises-application.md).
- Integre las aplicaciones cliente que no son de Microsoft con la [Plataforma de identidad de Microsoft](../develop/v2-overview.md) para la autenticación. 
    - [Aplicación de ejemplo para iOS](../develop/tutorial-v2-ios.md)
    - [Aplicación de ejemplo para Android](../develop/tutorial-v2-android.md)

## <a name="next-steps"></a>Pasos siguientes

- [Configuración y habilitación de los usuarios para la autenticación basada en SMS mediante Azure Active Directory](howto-authentication-sms-signin.md)
- Consulte los vínculos siguientes para habilitar el inicio de sesión por SMS para aplicaciones móviles nativas mediante bibliotecas MSAL: 
  - [iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)
  - [Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Tutoriales para integrar aplicaciones SaaS con Azure Active Directory](../saas-apps/tutorial-list.md)

## <a name="recommended-content"></a>Contenido recomendado

- [Inicio rápido: Agregar una aplicación a su inquilino](../manage-apps/add-application-portal.md)
- [Introducción a la Biblioteca de autenticación de Microsoft (MSAL)](../develop/msal-overview.md)
- [Configuración de la aplicación Managed Home Screen de Microsoft para Android Enterprise](/mem/intune/apps/app-configuration-managed-home-screen-app)
