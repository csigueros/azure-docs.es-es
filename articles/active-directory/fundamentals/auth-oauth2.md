---
title: Autenticación de OAuth 2.0 con Azure Active Directory
description: Guía de arquitectura para lograr la autenticación de OAUTH 2.0 con Azure Active Directory.
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
ms.openlocfilehash: 6bb7b35dc4a0e41278fcadd8ed487a7da0f00fc4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045963"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Autenticación de OAuth 2.0 con Azure Active Directory

OAuth 2.0 es el protocolo de autorización del sector. Permite que un usuario conceda acceso limitado a sus recursos protegidos. Diseñado para trabajar específicamente con el Protocolo de transferencia de hipertexto (HTTP), OAuth separa el rol del cliente del propietario del recurso. El cliente solicita acceso a los recursos que controla el propietario del recurso y que hospeda el servidor de recursos. El servidor de recursos emite tokens de acceso con la aprobación del propietario del recurso. El cliente usa los tokens de acceso para acceder a los recursos protegidos que hospeda el servidor de recursos. 

OAuth 2.0 está directamente relacionado con OpenID Connect (OIDC). Como OIDC es una capa de autenticación y autorización basada en OAuth 2.0, no es compatible con OAuth 1.0, una versión anterior. Azure Active Directory (Azure AD) admite todos los flujos de OAuth 2.0. 

## <a name="use-for"></a>Se utiliza para:

Escenarios de aplicaciones modernas y clientes enriquecidos y acceso a la API web RESTful.

![Diagrama de la arquitectura](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Componentes del sistema

* **Usuario**: solicita un servicio a la aplicación web (aplicación). Por lo general, el usuario es el propietario del recurso que posee los datos y tiene la capacidad de permitir que los clientes accedan a los datos o al recurso. 

* **Explorador web**: el explorador web con el que interactúa el usuario es el cliente OAuth. 

* **Aplicación web**: la aplicación web o el servidor de recursos es donde residen los datos o el recurso. Confía en el servidor de autorizaciones para autenticar y autorizar de forma segura al cliente OAuth. 

* **Azure AD**: Azure AD es el servidor de autorización, también conocido como el proveedor de identidades (IdP). Controla de forma segura todo lo que se debe hacer con la información del usuario, su acceso y la relación de confianza. Es responsable de emitir los tokens que conceden y revocan el acceso a los recursos.

## <a name="implement-oauth-20-with-azure-ad"></a>Implementación de OAuth 2.0 con Azure AD

* [Integración de aplicaciones con Azure AD](../saas-apps/tutorial-list.md) 

* [Protocolos OAuth 2.0 y OpenID Connect en la Plataforma de identidad de Microsoft](../develop/active-directory-v2-protocols.md) 

* [Tipos de aplicación y OAuth 2.0](../develop/v2-app-types.md) 

