---
title: Autenticación SAML con Azure Active Directory
description: Guía de arquitectura para lograr la autenticación SAML con Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c1173a6dbb47bfad61c0987fcb409731bc9f28c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451699"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Autenticación SAML con Azure Active Directory

El Lenguaje de marcado de aserción de seguridad (SAML) es un estándar abierto para intercambiar datos de autenticación y autorización entre un proveedor de identidades y un proveedor de servicios. SAML es un lenguaje de marcado basado en XML para las aserciones de seguridad, que son instrucciones que los proveedores de servicios usan para tomar decisiones relativas al control de acceso. 

La especificación de SAML define tres roles:

* La entidad de seguridad, normalmente un usuario
* El proveedor de identidades (IdP)
* El proveedor de servicios (SP)


## <a name="use-when"></a>Cuándo se utiliza

Existe la necesidad de brindar una experiencia de inicio de sesión único (SSO) para una aplicación SAML empresarial.

Si bien uno de los casos de uso más importantes que aborda SAML es SSO, en especial al ampliar SSO a los dominios de seguridad, también hay otros casos de uso (denominados "perfiles"). 

![Diagrama de la arquitectura para SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Componentes del sistema

* **Usuario**: Solicita un servicio a la aplicación.

* **Explorador web**: Componente con el que interactúa el usuario.

* **Aplicación web**: Aplicación empresarial que admite SAML y usa Azure AD como IdP.

* **Token**: Aserción SAML (también conocida como "token SAML") que incluye conjuntos de notificaciones realizadas por el IdP sobre la entidad de seguridad (usuario). Contiene información de autenticación, atributos e instrucciones sobre la decisión de autorización.

* **Azure AD**: IdP de la nube empresarial que proporciona SSO y autenticación multifactor para las aplicaciones SAML. Sincroniza, mantiene y administra la información de identidad de los usuarios, al tiempo que proporciona servicios de autenticación a las aplicaciones de confianza. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implementación de la autenticación SAML con Azure AD

* [Tutoriales para integrar aplicaciones SaaS con Azure Active Directory](../saas-apps/tutorial-list.md) 

* [Configuración de inicio de sesión único basado en SAML para aplicaciones que no están en la galería](../manage-apps/add-application-portal.md) 

* [Uso del protocolo SAML por parte de Azure AD](../develop/active-directory-saml-protocol-reference.md)
