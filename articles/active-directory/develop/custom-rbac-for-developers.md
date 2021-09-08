---
title: 'Control de acceso basado en rol (RBAC) personalizado para desarrolladores de aplicaciones: Plataforma de identidad de Microsoft'
description: Obtenga información sobre qué es RBAC personalizado y por qué es importante implementarlo en las aplicaciones.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/28/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: john.garland, maggie.marxen, ian.bennett, marsma
ms.openlocfilehash: f1378d1e011deaddf793dea9bebc7b099bedde9e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737307"
---
# <a name="role-based-access-control-for-application-developers"></a>Control de acceso basado en rol para desarrolladores de aplicaciones

El control de acceso basado en rol (RBAC) permite que determinados usuarios o grupos tengan permisos específicos con respecto a los recursos a los que tienen acceso, qué pueden hacer con esos recursos y quién los administra. En este artículo se explica el control de acceso basado en rol específico de la aplicación.

> [!NOTE]
> El control de acceso basado en rol de la aplicación difiere del [control de acceso basado en rol de Azure](../../role-based-access-control/overview.md) y del [control de acceso basado en rol de Azure AD](../roles/custom-overview.md#understand-azure-ad-role-based-access-control). Los roles personalizados de Azure y los roles integrados forman parte de RBAC de Azure, lo cual le permite administrar los recursos de Azure. El RBAC de Azure AD permite administrar recursos de Azure AD.



## <a name="what-are-roles"></a>¿Qué son los roles?

El control de acceso basado en rol (RBAC) es un mecanismo popular para exigir la autorización en las aplicaciones. Al usar RBAC, un desarrollador de aplicaciones define roles en lugar de autorizar usuarios o grupos individuales. Un administrador, después, puede asignar roles a diferentes usuarios y grupos para controlar quién tiene acceso a qué contenido y función.

El RBAC le ayuda, como desarrollador de aplicaciones, a administrar recursos y lo que los usuarios pueden hacer con esos recursos. También permite a un desarrollador de aplicaciones controlar a qué áreas de una aplicación tienen acceso los usuarios. Aunque los administradores pueden controlar qué usuarios tienen acceso a una aplicación mediante la propiedad *Asignación de usuarios requerida*, los desarrolladores deben tener en cuenta a los usuarios específicos dentro de la aplicación y qué pueden hacer dentro de esta.

Como desarrollador de aplicaciones, en primer lugar debe crear una definición de roles dentro de la sección de registro de la aplicación en el centro de administración de Azure AD. La definición de roles incluye un valor que se devuelve para los usuarios que están asignados a ese rol. Después, un desarrollador puede usar este valor para implementar la lógica de aplicación a fin de determinar lo que esos usuarios pueden o no pueden hacer en una aplicación.

## <a name="options-for-adding-rbac-to-apps"></a>Opciones para agregar el RBAC a aplicaciones

Hay varias consideraciones que deben administrarse al incluir la autorización de control de acceso basado en rol en una aplicación. Entre ellas se incluyen las siguientes:
- Definir los roles necesarios para las necesidades de autorización de una aplicación. 
- Aplicar, almacenar y recuperar los roles pertinentes para los usuarios autenticados. 
- Afectar al comportamiento deseado de la aplicación en función de los roles asignados al usuario actual. 

Una vez definidos los roles, la Plataforma de identidad de Microsoft admite varias soluciones diferentes que se pueden usar a fin de aplicar, almacenar y recuperar información de roles para los usuarios autenticados. Estas soluciones incluyen roles de aplicación, grupos de Azure AD y el uso de almacenes de datos personalizados para la información de rol de usuario.

Los desarrolladores tienen la flexibilidad de proporcionar su propia implementación para saber cómo se interpretarán las asignaciones de roles como permisos de aplicación. Esto puede implicar la utilización de middleware u otra función que proporciona la plataforma de sus aplicaciones o bibliotecas relacionadas. Las aplicaciones normalmente recibirán información del rol de usuario como notificaciones y decidirán los permisos de usuario en función de estas.

### <a name="app-roles"></a>Roles de aplicación

Azure AD admite la declaración de roles de aplicación para un registro de aplicación. Cuando un usuario inicia sesión en una aplicación, Azure AD incluirá una [notificación de roles](./access-tokens.md#payload-claims) para cada rol que se haya concedido al usuario para esa aplicación. Las aplicaciones que reciben tokens que contienen estas notificaciones pueden usar esta información para determinar qué permisos puede ejercer el usuario en función de los roles que se le asignan.

### <a name="groups"></a>Grupos

Los desarrolladores también pueden usar [grupos de Azure AD](../fundamentals/active-directory-manage-groups.md) para implementar RBAC en sus aplicaciones, donde las pertenencias de los usuarios a grupos específicos se interpretan como pertenencias a roles. Al usar grupos de Azure AD, Azure AD incluirá una [notificación de grupos](./access-tokens.md#payload-claims) que incluirá los identificadores de todos los grupos a los que el usuario está asignado en el inquilino actual de Azure AD. Las aplicaciones que reciben tokens que contienen estas notificaciones pueden usar esta información para determinar qué permisos puede ejercer el usuario en función de los roles que se le asignan.

> [!IMPORTANT]
> Al trabajar con grupos, los desarrolladores deben ser conscientes del concepto de una [notificación de uso por encima del límite](./access-tokens.md#payload-claims). De manera predeterminada, si un usuario es miembro de más grupos que el máximo de uso por encima del límite (150 para los tokens SAML, 200 para los tokens JWT y 6 si se usa el flujo implícito), Azure AD no emitirá una notificación de grupos en el token. En su lugar, incluirá una "notificación de uso por encima del límite" en el token que indica que el consumidor del token tendrá que consultar Graph API para recuperar las pertenencias a grupos del usuario. Para obtener más información sobre cómo trabajar con notificaciones de uso por encima del límite, vea [Notificaciones en tokens de acceso](./access-tokens.md#claims-in-access-tokens). Es posible emitir únicamente grupos asignados a una aplicación, aunque la [asignación basada en grupos](../manage-apps/assign-user-or-group-access-portal.md) requiere la edición Azure Active Directory Premium P1 o P2.

### <a name="custom-data-store"></a>Almacén de datos personalizado

Tanto los roles como los grupos de aplicaciones almacenan información sobre las asignaciones de usuario en el directorio de Azure AD. Otra opción a fin de administrar la información de rol de usuario que está disponible para los desarrolladores es mantener la información fuera del directorio, en un almacén de datos personalizado. Por ejemplo, en una base de datos SQL Database, en Azure Table Storage o en Azure Cosmos DB Table API.

Usar el almacenamiento personalizado permite a los desarrolladores una personalización y un control adicionales sobre cómo asignar roles a los usuarios y cómo representarlos. Pero una mayor flexibilidad también conlleva más responsabilidad. Por ejemplo, actualmente no hay ningún mecanismo disponible para incluir esta información en los tokens devueltos desde Azure AD. Si los desarrolladores mantienen información de rol en un almacén de datos personalizado, necesitarán que las aplicaciones recuperen los roles. Por lo general, esto se hace mediante puntos de extensibilidad definidos en el middleware disponible para la plataforma que se usa a fin de desarrollar la aplicación. Además, los desarrolladores son responsables de proteger correctamente el almacén de datos personalizado.

> [!NOTE]
> Con [directivas personalizadas de Azure AD B2C](../../active-directory-b2c/custom-policy-overview.md) es posible interactuar con almacenes de datos personalizados e incluir notificaciones personalizadas en un token.

## <a name="choosing-an-approach"></a>Elección de un enfoque

En general, los roles de aplicación son la solución recomendada. Los roles de aplicación proporcionan el modelo de programación más sencillo y están hechos a propósito para implementaciones de RBAC, pero hay requisitos específicos de la aplicación que pueden indicar que un enfoque distinto podría ser una mejor solución.

Los desarrolladores pueden usar los roles de aplicación a fin de controlar si un usuario puede iniciar sesión en una aplicación o si una aplicación puede obtener un token de acceso para una API web. Los desarrolladores prefieren los roles de aplicación más que los grupos de Azure AD cuando quieren describir y controlar los parámetros de autorización en sus propias aplicaciones. Por ejemplo, una aplicación que usa grupos para la autorización se interrumpirá en el inquilino siguiente, ya que tanto el identificador como el nombre del grupo podrían ser distintos. Una aplicación que usa roles de aplicación sigue siendo segura. De hecho, la asignación de grupos a roles de aplicación se usa mucho con las aplicaciones SaaS por las mismas razones.

Aunque se pueden usar roles de aplicación o grupos para la autorización, las diferencias clave entre ellos pueden influir en cuál es la mejor solución para un escenario determinado.

|          |Roles de aplicación |Grupos de Azure AD |Almacén de datos personalizado|
|----------|-----------|------------|-----------------|
|**Modelo de programación** |**La más sencilla**. Son específicos de una aplicación y se definen en el registro de la aplicación. Se mueven con la aplicación.|**Más compleja**. Los Id. de grupo varían entre inquilinos y es posible que sea necesario tener en cuenta las notificaciones de uso por encima del límite. Los grupos no son específicos de una aplicación, sino de un inquilino de Azure AD.|**La más compleja**. Los desarrolladores deben implementar los medios por los que la información de rol se almacena y recupera.|
|**Los valores de rol son estáticos entre inquilinos de Azure AD**|Sí  |No |Depende de la implementación.|
|**Los valores de rol se pueden usar en varias aplicaciones**|No. A menos que la configuración del rol se duplique en cada registro de aplicación.|Sí |Sí |
|**Información almacenada en el directorio**|Sí  |Sí |No |
|**La información se entrega mediante tokens**|Sí (notificación de roles).  |Sí* (notificación de grupos). |No. Se recupera en tiempo de ejecución mediante código personalizado. |
|**Duración**|Reside en el registro de aplicación en el directorio. Se quita cuando se elimina el registro de aplicación.|Reside en el directorio. Permanecen intactos incluso si se quita el registro de aplicación. |Reside en el almacén de datos personalizado. No está vinculado al registro de aplicación.|


> [!NOTE]
> Sí*: en el caso de un uso por encima del límite, es posible que sea necesario recuperar las *notificaciones de grupos* en tiempo de ejecución.

## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos para agregar roles de aplicación a una aplicación y recibirlos en el token](./howto-add-app-roles-in-azure-ad-apps.md).
- [Registro de una aplicación en la Plataforma de identidad de Microsoft](./quickstart-register-app.md).
- [Procedimientos recomendados de seguridad para la administración de identidades y el control de acceso en Azure](../../security/fundamentals/identity-management-best-practices.md).