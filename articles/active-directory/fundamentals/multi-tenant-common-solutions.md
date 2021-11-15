---
title: Soluciones comunes para la administración de usuarios multiinquilino en Azure Active Directory
description: Conozca los diferentes patrones que se utilizan para configurar el acceso de usuario entre inquilinos de Azure Active Directory con cuentas de invitado.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48412e7ad5c7ff90f1e5b89542f55b57fec4a534
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270161"
---
# <a name="common-solutions-for-multi-tenant-user-management"></a>Soluciones comunes para la administración de usuarios multiinquilino

Nuestros clientes han resuelto dos desafíos específicos mediante las herramientas actuales. A continuación, se detallan sus soluciones. Siempre que sea posible, Microsoft recomienda un inquilino único y trabaja en herramientas para resolver estos desafíos de manera más sencilla. Si el inquilino único no funciona en su escenario, estas soluciones les han servido a los clientes actuales. 

## <a name="automatic-user-lifecycle-management-and-resource-allocation-across-tenants"></a>Administración automática del ciclo de vida del usuario y asignación de recursos entre inquilinos

Un cliente adquiere a un competidor con el que anteriormente tenía una relación empresarial cercana. Las organizaciones conservarán sus identidades corporativas.

### <a name="current-state"></a>Estado actual

Actualmente, las organizaciones sincronizan los usuarios de cada una de ellas como objetos de correo electrónico de contacto a fin de que se muestren en los directorios de ambas. 

* Cada inquilino de recurso tiene un objeto de contacto de correo electrónico habilitado para todos los usuarios del otro inquilino.

* No se puede acceder a las aplicaciones entre los inquilinos.

### <a name="goals"></a>Objetivos

Estos eran los objetivos del cliente:

* Cada usuario debía seguir apareciendo en la GAL de cada organización.

   * Los cambios del ciclo de vida de las cuentas de usuario en el inquilino principal se reflejan automáticamente en la GAL del inquilino de recurso. 

   * Los cambios de atributos en los inquilinos principales (por ejemplo, departamento, nombre y dirección SMTP) se reflejan automáticamente en la GAL del inquilino de recurso y en la GAL inicial.

* Los usuarios pueden acceder a aplicaciones y recursos en el inquilino de recurso.

* Los usuarios pueden atender las solicitudes de acceso a los recursos sin ayuda de nadie.

### <a name="solution-architecture"></a>Arquitectura de la solución 

Las organizaciones usarán una arquitectura punto a punto con un motor de sincronización como MIM.

![Ejemplo de una arquitectura punto a punto](media/multi-tenant-common-solutions/point-to-point-sync.png)

Para crear los objetos de usuario, el administrador de cada inquilino hace lo siguiente:

1. Se asegura de que la base de datos de usuarios está actualizada.

1. [Implementa y configura MIM](/microsoft-identity-manager/microsoft-identity-manager-deploy).

   1. Direcciona los objetos de contacto existentes.

   1. Crea objetos de miembro externo B2B para los miembros del otro inquilino.

   1. Sincroniza los atributos de objeto de usuario.

1. Implementa y configura paquetes de acceso de [administración de derechos](../governance/entitlement-management-overview.md).

   1. Recursos que se van a compartir

   1. Directivas de revisión de acceso y expiración

## <a name="sharing-on-premises-apps-across-tenants"></a>Uso compartido de aplicaciones locales entre inquilinos

Este cliente, con varias organizaciones del mismo nivel, necesita compartir aplicaciones locales desde uno de los inquilinos.

### <a name="current-state"></a>Estado actual

Varias organizaciones del mismo nivel sincronizan usuarios invitados B2B en una topología de malla, lo que permite la asignación de recursos a sus aplicaciones en la nube entre inquilinos. Actualmente, ellos:

* Comparten aplicaciones en Azure AD.

* Garantizan que la administración del ciclo de vida del usuario en el inquilino de recurso se automatiza en función del inquilino principal. Es decir, se reflejan las incorporaciones, modificaciones y eliminaciones.

* Solo los usuarios miembros de la empresa A acceden a las aplicaciones locales de la empresa A.

![Escenario multiinquilino](media/multi-tenant-user-management-scenarios/mesh.png)

### <a name="goals"></a>Objetivos

Junto con la funcionalidad actual, les gustaría:

* Brindar a los usuarios invitados externos acceso a los recursos locales de la empresa A. 

* Aplicaciones con autenticación SAML

* Aplicaciones con Autenticación integrada de Windows y Kerberos

### <a name="solution-architecture"></a>Arquitectura de la solución

Actualmente, la empresa A proporciona SSO a las aplicaciones locales para sus propios miembros a través de Azure Application Proxy.

![Ejemplo de acceso a las aplicaciones](media/multi-tenant-common-solutions/app-access-scenario.png)

Para permitir que los usuarios invitados accedan a las mismas aplicaciones locales, los administradores del inquilino A harán lo siguiente:

1. [Configurar el acceso a las aplicaciones SAML](../external-identities/hybrid-cloud-to-on-premises.md#access-to-saml-apps).

2. [Configurar el acceso a otras aplicaciones](../external-identities/hybrid-cloud-to-on-premises.md#access-to-iwa-and-kcd-apps).

3. Crear usuarios invitados locales a través de [MIM](../external-identities/hybrid-cloud-to-on-premises.md#create-b2b-guest-user-objects-through-mim) o [PowerShell](https://www.microsoft.com/en-us/download/details.aspx?id=51495).

Para más información sobre la colaboración B2B, consulte:

[Conceder a los usuarios B2B de Azure AD acceso a los recursos locales](../external-identities/hybrid-cloud-to-on-premises.md)

[Colaboración B2B de Azure Active Directory para organizaciones híbridas](../external-identities/hybrid-organizations.md)

## <a name="next-steps"></a>Pasos siguientes
[Introducción a la administración de usuarios multiinquilino](multi-tenant-user-management-introduction.md)

[Escenarios de administración de usuarios finales multiinquilino](multi-tenant-user-management-scenarios.md)

[Consideraciones comunes multiinquilino](multi-tenant-common-considerations.md)
