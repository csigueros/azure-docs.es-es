---
title: 'Usuarios y grupos de una directiva de acceso condicional: Azure Active Directory'
description: Quiénes son los usuarios y los grupos de una directiva de acceso condicional de Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5e49e3e545e7536d9f0d9bc49b7fd791ea52b8d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627641"
---
# <a name="conditional-access-users-and-groups"></a>Acceso condicional: Usuarios y grupos

Una directiva de acceso condicional debe incluir una asignación de usuario como una de las señales del proceso de toma de decisiones. Se pueden incluir o excluir usuarios de las directivas de acceso condicional. Azure Active Directory evalúa todas las directivas y se asegura de que se cumplan todos los requisitos antes de conceder acceso al usuario. 

> [!VIDEO https://www.youtube.com/embed/5DsW1hB3Jqs]

## <a name="include-users"></a>Incluir usuarios

Esta lista de usuarios incluye normalmente a todos los usuarios a los que se dirige una organización en una directiva de acceso condicional. 

Las siguientes opciones están disponibles para su inclusión al crear una directiva de acceso condicional.

- None
   - Ningún usuario seleccionado
- Todos los usuarios
   - Todos los usuarios que existen en el directorio, incluidos los invitados B2B.
- Seleccionar Usuarios y grupos
   - Todos los usuarios externos e invitados
      - Esta selección incluye todos los invitados B2B y usuarios externos, incluido cualquier usuario con el atributo `user type` establecido en `guest`. Esta selección también se aplica a cualquier usuario externo que haya iniciado sesión desde una organización diferente, como un proveedor de soluciones en la nube (CSP). 
   - Roles de directorio
      - Permite a los administradores seleccionar roles de directorio integrados específicos de Azure AD que se usan para determinar la asignación de directivas. Por ejemplo, las organizaciones pueden crear una directiva más restrictiva en los usuarios a los que se ha asignado el rol de administrador global. No se admiten otros tipos de roles, incluidos los roles con ámbito de unidad administrativa y los roles personalizados.
   - Usuarios y grupos
      - Permite dirigirse a conjuntos específicos de usuarios. Por ejemplo, las organizaciones pueden seleccionar un grupo que contenga todos los miembros del departamento de recursos humanos cuando tenga una aplicación de Recursos Humanos seleccionada como aplicación en la nube. Un grupo puede ser cualquier tipo de grupo en Azure AD, como grupos de distribución y de seguridad dinámicos o asignados. La directiva se aplicará a los usuarios y grupos anidados.

> [!IMPORTANT]
> Al seleccionar qué usuarios y grupos se incluyen en una directiva de acceso condicional, existe un límite en cuanto al número de usuarios individuales que se pueden agregar directamente a dicha directiva. Si se debe agregar directamente una gran cantidad de usuarios individuales a una directiva de acceso condicional, se recomienda colocar los usuarios en un grupo y asignar el grupo a la directiva en su lugar.

> [!WARNING]
> Si los usuarios o grupos son miembros de más de 2048 grupos, es posible que se bloquee su acceso. Este límite se aplica a la pertenencia a grupos directa y anidada.

> [!WARNING]
> Las directivas de acceso condicional no admiten usuarios asignados a un rol de directorio [con el ámbito de una unidad administrativa](../roles/admin-units-assign-roles.md) o roles de directorio con un ámbito directo de un objeto, como mediante [roles personalizados](../roles/custom-create.md).

## <a name="exclude-users"></a>Excluir usuarios

Cuando las organizaciones incluyen y excluyen un usuario o grupo, estos se excluyen de la directiva, ya que las acciones de exclusión invalidan las de inclusión en la directiva. Las exclusiones se usan normalmente para cuentas de acceso de emergencia. En los siguientes artículos encontrará más información sobre las cuentas de acceso de emergencia y por qué son importantes: 

* [Administración de cuentas de acceso de emergencia en Azure AD](../roles/security-emergency-access.md)
* [Crear una estrategia de administración de control de acceso resistente con Azure Active Directory](../authentication/concept-resilient-controls.md)

Las siguientes opciones están disponibles para su exclusión al crear una directiva de acceso condicional.

- Todos los usuarios externos e invitados
   - Esta selección incluye todos los invitados B2B y usuarios externos, incluido cualquier usuario con el atributo `user type` establecido en `guest`. Esta selección también se aplica a cualquier usuario externo que haya iniciado sesión desde una organización diferente, como un proveedor de soluciones en la nube (CSP). 
- Roles de directorio
   - Permite a los administradores seleccionar roles de directorio específicos de Azure AD que se usan para determinar la asignación. Por ejemplo, las organizaciones pueden crear una directiva más restrictiva en los usuarios a los que se ha asignado el rol de administrador global.
- Usuarios y grupos
   - Permite dirigirse a conjuntos específicos de usuarios. Por ejemplo, las organizaciones pueden seleccionar un grupo que contenga todos los miembros del departamento de recursos humanos cuando tenga una aplicación de Recursos Humanos seleccionada como aplicación en la nube. Un grupo puede ser cualquier tipo de grupo en Azure AD, como grupos de distribución y de seguridad dinámicos o asignados.

### <a name="preventing-administrator-lockout"></a>Impedir el bloqueo de administrador

Para evitar que un administrador se bloquee fuera de su directorio al crear una directiva que se aplica a **todos los usuarios** y a **todas las aplicaciones**, verá la siguiente advertencia.

> No se bloquee. Se recomienda aplicar primero una directiva a un pequeño conjunto de usuarios para comprobar que se comporta según lo esperado. También se recomienda excluir al menos un administrador de esta directiva. Esto garantiza que sigue teniendo acceso y puede actualizar una directiva si es necesario un cambio. Revise los usuarios y las aplicaciones afectados.

De forma predeterminada, la directiva proporcionará una opción para excluir al usuario actual de la directiva, pero el administrador puede invalidar este valor predeterminado, como se muestra en la siguiente imagen. 

![Advertencia, no se bloquee.](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

Si se encuentra en ese estado,[¿qué debe hacer si está bloqueado en Azure Portal?](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)

## <a name="next-steps"></a>Pasos siguientes

- [Acceso condicional: Aplicaciones o acciones en la nube](concept-conditional-access-cloud-apps.md)

- [Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)
