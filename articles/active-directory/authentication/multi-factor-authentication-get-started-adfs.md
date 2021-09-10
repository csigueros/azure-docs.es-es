---
title: 'Verificación en dos pasos en Azure AD MFA y ADFS: Azure Active Directory'
description: En esta página de Azure AD Multi-Factor Authentication se describe cómo empezar a trabajar con Azure AD MFA y AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9abc9e2e2ca2b9305cccf5cec554994c3703ef1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214500"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Introducción a Azure AD Multi-Factor Authentication y a los Servicios de federación de Active Directory

<center>

![Introducción a Azure AD MFA y AD FS](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Si su organización ha federado su Active Directory local con Azure Active Directory mediante AD FS, tiene a su disposición dos opciones para usar Azure AD Multi-Factor Authentication.

* Recursos de nube seguros a través del uso de Azure AD Multi-Factor Authentication o los Servicios de federación de Active Directory
* Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication

En la tabla siguiente se resume la experiencia de verificación entre la protección de recursos con Azure AD Multi-Factor Authentication y AD FS.

| Experiencia de verificación: aplicaciones basadas en explorador | Experiencia de verificación: aplicaciones no basadas en explorador |
|:--- |:--- |
| Protección de recursos de Azure AD con Azure AD Multi-Factor Authentication |<li>El primer paso de comprobación se realiza localmente utilizando AD FS.</li> <li>El segundo factor es un método basado en teléfono que se lleva a cabo utilizando la autenticación en la nube.</li> |
| Protección de los recursos de Azure AD mediante los servicios de federación de Active Directory |<li>El primer paso de comprobación se realiza localmente utilizando AD FS.</li><li>El segundo paso es realiza localmente respondiendo a la notificación.</li> |

Advertencias sobre las contraseñas de aplicación para usuarios federados:

* Las contraseña de aplicación se comprueban mediante autenticación en la nube y, por lo tanto, omiten las federaciones. La federación solo se usa activamente al configurar una contraseña de aplicación.
* La configuración del control de acceso de cliente local no se cumple con las contraseñas de aplicación.
* Se pierde la capacidad de registro de autenticación local para las contraseñas de aplicación.
* La deshabilitación o eliminación de la cuenta puede tardar hasta 3 horas en la sincronización de directorios, retrasando la deshabilitación o eliminación de las contraseñas de aplicación en la identidad de nube.

Para información sobre cómo configurar Azure AD Multi-Factor Authentication o Servidor Azure Multi-Factor Authentication con AD FS, consulte los siguientes artículos:

* [Protección de recursos en la nube con Azure AD Multi-Factor Authentication y AD FS](howto-mfa-adfs.md)
* [Protección de recursos en la nube y locales mediante Servidor Microsoft Azure Multi-Factor Authentication con Windows Server](howto-mfaserver-adfs-windows-server.md)
* [Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS 2.0](howto-mfaserver-adfs-2.md)
