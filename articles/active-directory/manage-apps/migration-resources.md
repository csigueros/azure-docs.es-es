---
title: Recursos para migrar aplicaciones a Azure Active Directory
description: Recursos para ayudarle a migrar el acceso a la aplicación y la autenticación a Azure Active Directory (Azure AD).
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: b8aab437e9ffac19da56a892a4547aa234682458
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856588"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Recursos para migrar aplicaciones a Azure Active Directory

Recursos para ayudarle a migrar el acceso a la aplicación y la autenticación a Azure Active Directory (Azure AD).

| Resource  | Descripción  |
|:-----------|:-------------|
|[Migrar sus aplicaciones a Azure AD](https://aka.ms/migrateapps/whitepaper) | En este artículo se presentan las ventajas de la migración y se describe cómo planear la migración en cuatro fases bien definidas: detección, clasificación, migración y administración continua. Se le guiará sobre cómo debe pensar en el proceso y desglosar el proyecto en partes fáciles de consumir. Este documento incluye vínculos a recursos importantes que le ayudarán a lo largo de este proceso. |
|[Tutorial para desarrolladores: Guía de migración de aplicaciones de AD FS a Azure AD para desarrolladores](https://aka.ms/adfsplaybook) | Este conjunto de ejemplos de código y tutoriales complementarios de ASP.NET le ayudará a aprender a migrar con seguridad las aplicaciones integradas con Servicios de federación de Active Directory (AD FS) a Azure Active Directory (Azure AD). Este tutorial se centra en los desarrolladores que no solo necesitan obtener información sobre la configuración de aplicaciones en AD FS y Azure AD, sino que también son conscientes y están seguros de los cambios que su base de código necesitará en este proceso.|
| [Herramienta: script de preparación para la migración de Servicios de federación de Active Directory](https://aka.ms/migrateapps/adfstools) | Se trata de un script que puede ejecutar en el servidor local de Servicios de federación de Active Directory (AD FS) para determinar la preparación de las aplicaciones para la migración a Azure AD.|
| [Plan de implementación: migración de AD FS a la sincronización de hash de contraseña](https://aka.ms/ADFSTOPHSDPDownload) | Con la sincronización de hash de contraseña, se sincronizan los valores hash de las contraseñas de los usuarios de Active Directory local con Azure AD. Esto permite a Azure AD autenticar a los usuarios sin interactuar con la instancia local de Active Directory.|
| [Plan de implementación: migración de AD FS a autenticación de paso a través](https://aka.ms/ADFSTOPTADPDownload)|La autenticación de paso a través de Azure AD ayuda a los usuarios a iniciar sesión en aplicaciones locales y en la nube con la misma contraseña. Esta característica proporciona a los usuarios una mejor experiencia, puesto que tienen una contraseña menos que recordar. También reduce los costos del departamento de soporte técnico, ya que es menos probable que olviden cómo iniciar sesión si solo necesitan recordar una contraseña. Cuando los usuarios inician sesión con Azure AD, esta característica valida sus contraseñas directamente con la instancia de Active Directory local.|
| [Plan de implementación: habilitación del inicio de sesión único en una aplicación SaaS con Azure AD](https://aka.ms/SSODPDownload) | El inicio de sesión único (SSO) le ayuda a acceder a todas las aplicaciones y los recursos que necesita para hacer negocios, iniciando sesión una sola vez con una única cuenta de usuario. Por ejemplo, después de iniciar la sesión, el usuario puede cambiar entre Microsoft Office, Salesforce o Box sin necesidad de volver a autenticarse (por ejemplo, mediante una contraseña).
| [Plan de implementación: ampliación de las aplicaciones a Azure AD con el proxy de aplicación](https://aka.ms/AppProxyDPDownload)| Para proporcionar acceso a los portátiles de los empleados y otros dispositivos a las aplicaciones locales, siempre se han usado redes privadas virtuales (VPN) o redes perimetrales (DMZ). No obstante, estas soluciones no solo son complejas y difíciles de proteger, sino también costosas de configurar y administrar. Azure AD Application Proxy facilita el acceso a las aplicaciones locales. |
| [Planes de implementación](../fundamentals/active-directory-deployment-plans.md) | Encuentre más planes de implementación para implementar características como la autenticación multifactor, el acceso condicional, el aprovisionamiento de usuarios, SSO de conexión directa, el autoservicio de restablecimiento de contraseña y mucho más. |
| [Migración de aplicaciones de Symantec SiteMinder a Azure AD](https://azure.microsoft.com/mediahandler/files/resourcefiles/migrating-applications-from-symantec-siteminder-to-azure-active-directory/Migrating-applications-from-Symantec-SiteMinder-to-Azure-Active-Directory.pdf) | Obtenga instrucciones paso a paso sobre las opciones de integración y migración de aplicaciones con un ejemplo que le guiará a través de la migración de aplicaciones de Symantec SiteMinder a Azure AD. |

