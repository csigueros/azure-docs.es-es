---
title: Configuración de la administración de usuarios multiinquilino en Azure Active Directory
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
ms.openlocfilehash: 379135bc446dbef6a2145a6339b5ddd318a5f579
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269975"
---
# <a name="multi-tenant-user-management"></a>Administración de usuarios multiinquilino 

El aprovisionamiento de usuarios en un inquilino único de Azure Active Directory (Azure AD) proporciona una vista unificada de los recursos y un conjunto único de directivas y controles. Este enfoque permite una administración coherente del ciclo de vida de los usuarios. 

**Cuando sea posible, Microsoft recomienda un usuario único**. Sin embargo, no siempre es posible la consolidación inmediata en un inquilino de Azure AD único. Las organizaciones multiinquilino pueden abarcar dos o más inquilinos de Azure AD. Esto puede dar lugar a requisitos únicos de administración y colaboración entre inquilinos.

Las organizaciones pueden tener requisitos de administración de identidades y acceso (IAM) que se ven complicados por:

* combinaciones, adquisiciones y desinversiones

* colaboración entre nubes públicas, soberanas o regionales

* estructuras organizativas o políticas que prohíben la consolidación en un inquilino de Azure AD único

En estas instrucciones también se proporcionan lineamientos que le permiten alcanzar un estado coherente de la administración del ciclo de vida de los usuarios. Es decir, aprovisionar, administrar y desaprovisionar usuarios en distintos inquilinos mediante las herramientas disponibles con Azure. Específicamente, mediante la [colaboración B2B de Azure AD](../external-identities/what-is-b2b.md).

## <a name="azure-ad-b2b-collaboration"></a>Colaboración B2B de Azure AD

La colaboración de Azure AD le permite compartir de manera segura las aplicaciones y servicios de su empresa con usuarios invitados externos. Los usuarios pueden provenir de cualquier organización. La colaboración B2B de Azure AD lo ayuda a mantener el control sobre el acceso a los datos y el entorno de TI. También se puede utilizar para proporcionar acceso de invitado a usuarios internos. Tradicionalmente, el acceso de usuario invitado B2B se usan para autorizar acceso a usuarios externos que no están administrados por su propia organización. Sin embargo, el acceso de usuario invitado también se puede usar para administrar el acceso en varios inquilinos administrados por su organización. Si bien no es realmente una solución B2B, la colaboración B2B de Azure AD se puede usar para administrar usuarios internos en un escenario multiinquilino.

Puede visitar los vínculos siguientes para información adicional sobre la colaboración B2B de Azure AD:

| Artículo| Descripción |
| - |-|
| **Artículos conceptuales**|  |
| [Procedimientos recomendados de B2B](../external-identities/b2b-fundamentals.md)| Recomendaciones para lograr la experiencia más fluida para usuarios y administradores.|  
| [Uso compartido externo de B2B y Office 365](../external-identities/o365-external-user.md)| Explica las similitudes y diferencias entre el uso compartido de recursos a través de B2B, Office 365 y SharePoint/OneDrive.|  
| [Propiedades en un usuario de colaboración B2B de Azure AD](../external-identities/user-properties.md)| Describe las propiedades y los estados del objeto de usuario invitado B2B en Azure Active Directory (Azure AD). La descripción proporciona detalles antes y después del canje de invitación.|  
| [Tokens de usuario de B2B](../external-identities/user-token.md)| Proporciona ejemplos de los tokens de portador para B2B de un usuario invitado B2B.|  
| [Acceso condicional para B2B](../external-identities/conditional-access.md)| Describe cómo funcionan el acceso condicional y MFA para los usuarios invitados.|  
| **Artículos de procedimientos**|  |
| [Uso de PowerShell para invitar en bloque a usuarios de colaboración de Azure AD B2B](../external-identities/bulk-invite-powershell.md)| Aprenderá a usar PowerShell para enviar invitaciones de forma masiva a usuarios externos.|
| [Aplicación de la autenticación multifactor en usuarios invitados B2B](../external-identities/b2b-tutorial-require-mfa.md)|Use directivas de MFA y acceso condicional para aplicar los niveles de autenticación de inquilino, aplicación o usuario invitado individual. |
| [Autenticación con código de acceso de un solo uso por correo electrónico](../external-identities/one-time-passcode.md)| La característica de código de acceso de un solo uso de correo electrónico autentica los usuarios invitados de B2B cuando no pueden autenticarse a través de otros medios, como Azure AD, una cuenta de Microsoft (MSA) o federación de Google.|

## <a name="terminology"></a>Terminología

En este contenido se utilizan los términos siguientes:

* **Inquilino de recursos**: el inquilino de Azure AD que contiene los recursos que los usuarios quieren compartir con otros.

* **Inquilino inicial**: el inquilino de Azure AD que contiene los usuarios que requieren acceso a los recursos del inquilino de recursos.

* **Administración del ciclo de vida de los usuarios**: el proceso de aprovisionar, administrar y desaprovisionar el acceso de los usuarios a los recursos.

* **GAL unificada**: todos los usuarios de cada inquilino pueden ver a los usuarios de cada organización en su lista de direcciones global (GAL).

## <a name="deciding-how-to-meet-your-requirements"></a>Cumplimiento de los requisitos

Los requisitos únicos de la organización determinarán la estrategia para administrar los usuarios entre distintos inquilinos. A fin de crear una estrategia eficaz, debe considerar:

* Cantidad de inquilinos

* Tipo de organización

* Topologías actuales

* Necesidades específicas de sincronización de usuarios 

### <a name="common-requirements"></a>Requisitos comunes

Inicialmente, muchas organizaciones se centran en los requisitos que desean para una colaboración inmediata. A veces conocidos como requisitos del Día uno, estos requisitos se centran en permitir que los usuarios finales se combinen sin problemas y sin interrumpir su capacidad de generar valor para la empresa. A medida que defina los requisitos administrativos y del Día uno, considere la posibilidad de incluir estos objetivos: 

| Categoría de requisito| Necesidades comunes|
| ------------ | - |
| **Requisitos de comunicaciones**|  |
| Lista de direcciones global unificada| Cada usuario puede ver a todos los demás usuarios de la GAL en su inquilino inicial. |
| Información de disponibilidad| Permitir que los usuarios puedan descubrir la disponibilidad de los demás. Puede hacerlo con las [relaciones de la organización en Exchange Online](/exchange/sharing/organization-relationships/create-an-organization-relationship).|
| Chat y presencia| Permitir que los usuarios determinen la presencia de otros usuarios e inicien la mensajería instantánea. Esto se puede configurar a través del [acceso externo en Microsoft Teams](/microsoftteams/manage-external-access).|
| Reserva de recursos como salas de reuniones| Permitir que los usuarios reserven salas de conferencias u otros recursos de la organización. Actualmente, no es posible reservar salas de conferencia entre inquilinos.|
‎Dominio de correo electrónico único| Permitir que todos los usuarios envíen y reciban correo electrónico proveniente de un dominio de correo electrónico único; por ejemplo, *users@contoso.com* . Actualmente, el envío requiere una solución de reescritura de direcciones de terceros.|
| **Requisitos de acceso**|  |
| Acceso a documentos| Permitir que los usuarios compartan documentos de SharePoint, OneDrive y Teams. |
| Administración| Permitir que los administradores se hagan cargo de la configuración de suscripciones y servicios implementados en varios inquilinos. |
| Acceso a las aplicaciones| Permitir que los usuarios finales accedan a aplicaciones en toda la organización. |
| Inicio de sesión único| Permitir que los usuarios accedan a recursos de toda la organización sin necesidad de escribir más credenciales.|

### <a name="patterns-for-account-creation"></a>Patrones para la creación de cuentas 

Existen varios mecanismos disponibles para crear y administrar el ciclo de vida de las cuentas de usuarios invitados. Microsoft distingue tres patrones comunes. Puede usar estos patrones para ayudar a definir e implementar los requisitos. Elija el patrón que mejor se alinea con el escenario en cuestión y, luego, céntrese en sus detalles.

| Mechanism |  Descripción | Mejor en estas circunstancias |
| - | - | - |
| [Iniciado por el usuario](multi-tenant-user-management-scenarios.md#end-user-initiated-scenario) | Los administradores de inquilinos de recursos delegan la capacidad de invitar a usuarios invitados al inquilino, una aplicación o un recurso a los usuarios del inquilino del recurso. Los usuarios del inquilino inicial reciben una invitación o se suscriben de manera individual. |  <li>Los usuarios necesitan un acceso improvisado a los recursos. <li>No es necesaria la sincronización automática de los atributos de usuario.<li>No se necesita una GAL unificada. |
|[Incluido en script](multi-tenant-user-management-scenarios.md#scripted-scenario) | Los administradores de inquilinos de recursos implementan un proceso de extracción con script para automatizar la detección y el aprovisionamiento de usuarios invitados a fin de admitir los escenarios de uso compartido. |  <li>No hay más de dos inquilinos.<li>No es necesaria la sincronización automática de los atributos de usuario.<li>Los usuarios necesitan acceso preconfigurado (no improvisado) a los recursos.|
|[Automatizado](multi-tenant-user-management-scenarios.md#automated-scenario)|Los administradores de inquilinos de recursos usan un sistema de aprovisionamiento de identidades para automatizar los procesos de aprovisionamiento y desaprovisionamiento. |  <li>Se debe automatizar la administración completa del ciclo de vida de la identidad con aprovisionamiento y desaprovisionamiento.<li>La sincronización de atributos es necesaria para rellenar los detalles de la GAL y admitir escenarios de derechos dinámicos.<li>Los usuarios necesitan acceso preconfigurado (no improvisado) a los recursos el "Día uno".|

  
## <a name="next-steps"></a>Pasos siguientes

[Escenarios de administración de usuarios multiinquilino](multi-tenant-user-management-scenarios.md)

[Consideraciones comunes multiinquilino](multi-tenant-common-considerations.md)

[Soluciones comunes multiinquilino](multi-tenant-common-solutions.md)
