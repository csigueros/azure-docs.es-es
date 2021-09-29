---
title: Conceptos básicos de autorización | Azure
titleSuffix: Microsoft identity platform
description: Conozca los conceptos básicos sobre la autorización en Plataforma de identidad de Microsoft.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: a3710d4af1b65b1220db8938b35cf9d6cb516579
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734721"
---
# <a name="authorization-basics"></a>Conceptos básicos de autorización

La **autorización** (en ocasiones abreviada como *AuthZ*) se utiliza para configurar permisos que se usan para evaluar el acceso a recursos o funcionalidades.  Por el contrario, la **autenticación** (abreviada a veces como *AuthN*) se centra en demostrar que una entidad como un usuario o servicio es quien dice ser.

La autorización puede incluir la especificación de a qué funcionalidad (o recursos) se le permite tener acceso a una entidad o a qué datos puede acceder esa entidad y qué puede hacer con esos datos. A eso se le suele denominar *control de acceso*.

> [!NOTE]
> La autenticación y la autorización son conceptos que no se limitan solo a los usuarios. Los servicios o las aplicaciones demonio se suelen crear para realizar solicitudes de recursos como ellos mismos en lugar de en nombre de un usuario específico. Al hablar sobre estos temas, el término "entidad" se usa para hacer referencia a un usuario o a una aplicación.


## <a name="authorization-approaches"></a>Enfoques de autorización

Hay varios enfoques comunes para administrar la autorización. El [control de acceso basado en roles](./custom-rbac-for-developers.md) es actualmente el enfoque más común que usa la Plataforma de identidad de Microsoft.


### <a name="authentication-as-authorization"></a>Autenticación como autorización 

Posiblemente, la forma más sencilla de autorización es conceder o denegar el acceso en función de si se ha autenticado la entidad que realiza una solicitud. Si el solicitante puede demostrar que es quien dice ser, puede acceder a los recursos o funcionalidades protegidos.

### <a name="access-control-lists"></a>Listas de control de acceso

La autorización mediante listas de control de acceso (ACL) implica el mantenimiento de listas explícitas de entidades específicas que tienen o no acceso a un recurso o funcionalidad. Las ACL ofrecen un control más preciso sobre la autenticación como autorización, pero son difíciles de administrar a medida que aumenta el número de entidades.

### <a name="role-based-access-control"></a>Control de acceso basado en rol 

El control de acceso basado en roles (RBAC) es posiblemente el enfoque más común para aplicar la autorización en las aplicaciones. Al usar RBAC, los roles se definen para describir los tipos de actividades que una entidad puede realizar. Un desarrollador de aplicaciones concede acceso a roles en lugar de a entidades individuales. A continuación, un administrador puede asignar roles a distintas entidades para controlar cuáles tienen acceso a qué recursos y funcionalidades.

En implementaciones avanzadas de RBAC, los roles se pueden asignar a colecciones de permisos, donde un permiso describe una acción o actividad concreta que se puede realizar. Después, los roles se configuran como combinaciones de permisos. Para procesar el conjunto general de permisos de las entidades para una aplicación, debe formar una intersección con los permisos concedidos a los distintos roles a los que se asigna la entidad. Un buen ejemplo de este enfoque es la implementación de RBAC que gobierna el acceso a los recursos de las suscripciones de Azure.

> [!NOTE]
> [El RBAC de aplicación](./custom-rbac-for-developers.md) difiere del [RBAC de Azure](../../role-based-access-control/overview.md) y del [RBAC de Azure AD](../roles/custom-overview.md#understand-azure-ad-role-based-access-control). Los roles personalizados de Azure y los roles integrados forman parte de RBAC de Azure, lo cual le ayuda a administrar los recursos de Azure. El RBAC de Azure AD permite administrar recursos de Azure AD.

### <a name="attribute-based-access-control"></a>Control de acceso basado en atributos 

El control de acceso basado en atributos (ABAC) es un mecanismo de control de acceso más específico. En este enfoque, las reglas se aplican a los atributos de la entidad, a los recursos a los que se accede y al entorno actual para determinar si se permite el acceso a algunos recursos o funcionalidades. Un ejemplo podría ser permitir que solo los usuarios que sean administradores accedan a los archivos identificados con una etiqueta de metadatos de "administradores solo durante el horario laboral" de 9 a.m. a 5 p.m. en días laborables. En este caso, el acceso viene determinado por el examen del atributo del usuario (estado como administrador), el atributo del recurso (etiqueta de metadatos en un archivo) y también un atributo de entorno (la hora actual).

Una ventaja de ABAC es que se puede lograr un control de acceso más específico y dinámico mediante evaluaciones de reglas y condiciones sin necesidad de crear un gran número de roles muy específicos y asignaciones de RBAC.

Un método para obtener el control de acceso basado en atributos con Azure Active Directory es usar [grupos dinámicos](../enterprise-users/groups-create-rule.md). Los grupos dinámicos permiten a los administradores asignar dinámicamente usuarios a grupos en función de atributos de usuario específicos con los valores deseados.  Por ejemplo, se podría crear un grupo Autores en el que todos los usuarios con el puesto Autor se asignen dinámicamente al grupo Autores.  Los grupos dinámicos se pueden usar en combinación con RBAC para la autorización allí donde se asignan roles a grupos y se asignan dinámicamente usuarios a grupos.

[Azure ABAC](../../role-based-access-control/conditions-overview.md) es un ejemplo de una solución de ABAC que está disponible actualmente. Azure ABAC se basa en Azure RBAC con la adición de condiciones de asignación de roles basadas en atributos en el contexto de acciones específicas. 

## <a name="implementing-authorization"></a>Implementación de la autorización

La lógica de autorización a menudo se implementa dentro de las aplicaciones o soluciones donde se requiere control de acceso. En muchos casos, las plataformas de desarrollo de aplicaciones ofrecen middleware u otras soluciones de API que simplifican la implementación de la autorización. Entre los ejemplos se incluye el uso de [AuthorizeAttribute](/aspnet/core/security/authorization/simple?view=aspnetcore-5.0&preserve-view=true) en ASP.NET o de [Route Guards](./scenario-spa-sign-in.md?tabs=angular2#sign-in-with-a-pop-up-window) en Angular.

Para los enfoques de autorización que se basan en información sobre la entidad autenticada, una aplicación evaluará la información intercambiada durante la autenticación. Por ejemplo, mediante la información proporcionada dentro de un [token de seguridad](./security-tokens.md). Para la información no contenida en un token de seguridad, una aplicación podría realizar llamadas adicionales a recursos externos.

No es estrictamente necesario que los desarrolladores inserten la lógica de autorización completamente dentro de sus aplicaciones. En su lugar, se pueden usar servicios de autorización dedicados para centralizar la implementación y administración de autorizaciones.


## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre la implementación personalizada del control de acceso basado en roles en aplicaciones, consulte [Control de acceso basado en rol para desarrolladores de aplicaciones](./custom-rbac-for-developers.md).
- Para conocer el proceso de registrar la aplicación para que se pueda integrar con la plataforma de identidad de Microsoft, consulte [Modelo de aplicación](./application-model.md).
- Para obtener un ejemplo de configuración de la autorización simple basada en autenticación, consulte [Configuración de una aplicación de App Service o Azure Functions para usar el inicio de sesión de Azure AD](../../app-service/configure-authentication-provider-aad.md).