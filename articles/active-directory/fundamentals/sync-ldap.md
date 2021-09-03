---
title: Sincronización de LDAP con Azure Active Directory
description: Guía de arquitectura para lograr la sincronización de LDAP con Azure Active Directory.
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
ms.openlocfilehash: 224b0685b922e6ddd833637ddc1b628f0479ec70
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112200823"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Sincronización de LDAP con Azure Active Directory

El protocolo ligero de acceso a directorio (LDAP) es un protocolo del servicio de directorio que se ejecuta en la pila de TCP/IP. Proporciona un mecanismo que se utiliza para buscar, modificar y conectarse a directorios de Internet. El servicio de directorio de LDAP se basa en un modelo cliente-servidor y su función es habilitar el acceso a un directorio existente. Muchas empresas dependen de servidores LDAP locales para almacenar usuarios y grupos para sus aplicaciones empresariales críticas. 

Azure Active Directory (Azure AD) pueden reemplazar la sincronización LDAP con Azure AD Connect. El servicio de sincronización de Azure AD Connect realiza todas las operaciones relacionadas con la sincronización de los datos de identidad entre los entornos locales y Azure AD. 

## <a name="use-when"></a>Cuándo se utiliza

Debe sincronizar los datos de identidad entre los directorios de LDAP v3 locales y Azure AD. 

![Diagrama de arquitectura](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Componentes del sistema

* **Usuario**: Accede a una aplicación que se basa en el uso de un directorio LDAP v3 para ordenar usuarios y contraseñas.

* **Explorador web**: Componente con el que el usuario interactúa para acceder a la dirección URL externa de la aplicación.

* **Aplicación web**: Aplicación con dependencias en directorios LDAP v3.

* **Azure AD**: Azure AD sincroniza la información de identidad (usuarios, grupos y contraseñas) de los directorios LDAP locales de la organización a través de Azure AD Connect. 

* **Azure AD Connect**: Herramienta para la conexión de infraestructuras de identidad locales con Microsoft Azure AD. El asistente y las experiencias guiadas ayudan a implementar y configurar los requisitos previos y los componentes necesarios para la conexión. 

* **Conector personalizado**: Un conector de LDAP genérico le permite integrar el servicio de sincronización de Azure AD Connect con un servidor LDAP v3. Se basa en Azure AD Connect.

* **Active Directory**: Servicio de directorio incluido en la mayoría de los sistemas operativos Windows Server. Los servidores que ejecutan los servicios de directorio de Active Directory se denominan controladores de dominio, y autentican y autorizan a todos los usuarios y equipos de un dominio de Windows.

* **Servidor LDAP v3**: Directorio compatible con el protocolo LDAP, que almacena usuarios corporativos y contraseñas usados para la autenticación de servicios de directorio.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Implementación de la sincronización LDAP con Azure AD

* [Herramientas para la integración de directorios de identidades híbridas](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Hoja de ruta de instalación de Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md) 

* [Información general y creación de un conector LDAP](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > Para implementar el conector LDAP se necesita una configuración avanzada y este conector se proporciona con compatibilidad limitada. Para la configuración de este conector es necesario estar familiarizado con Microsoft Identity Manager y el directorio LDAP específico. 
   >
   > Se recomienda que los clientes que tengan que implementar esta configuración en un entorno de producción trabajen con un asociado como Microsoft Consulting Services para obtener ayuda, instrucciones y soporte técnico para esta configuración.
