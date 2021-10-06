---
title: Introducción al control de acceso basado en rol de Azure Active Directory (RBAC)
description: Descripción de las partes de una asignación de roles y el ámbito restringido en Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 09/13/2021
ms.author: rolyon
ms.reviewer: abhijeetsinha
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21e1b91c10d687b0b82626372510dcaf2a0611ef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601812"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Introducción al control de acceso basado en rol en Azure Active Directory

En este artículo se describe el control de acceso basado en rol de Azure Active Directory (Azure AD). Los roles de Azure AD permiten conceder permisos específicos a los administradores respetando el principio de los privilegios mínimos. Los roles personalizados e integrados de Azure AD se basan en conceptos parecidos a los que se encuentran en el [sistema de control de acceso basado en rol para los recursos de Azure](../../role-based-access-control/overview.md) (roles de Azure). La [diferencia entre estos dos sistemas de control de acceso basados en rol](../../role-based-access-control/rbac-and-directory-admin-roles.md) es:

- El control de rol de Azure AD accede a recursos de Azure AD como los usuarios, los grupos y las aplicaciones con Graph API.
- El control de rol de Azure accede a recursos de Azure como las máquinas virtuales o el almacenamiento mediante Azure Resource Manager.

Ambos sistemas contienen definiciones de rol y asignaciones de roles que se usan igual. Sin embargo, los permisos de rol de Azure AD no se pueden usar en los roles personalizados de Azure ni viceversa.

## <a name="understand-azure-ad-role-based-access-control"></a>Información sobre el control de acceso basado en rol de Azure AD
Azure AD admite dos tipos de definiciones de roles:
* [Roles integrados](./permissions-reference.md)
* [Roles personalizados](./custom-create.md)

Los roles integrados son roles predefinidos que tienen un conjunto fijo de permisos. Estas definiciones de roles no se pueden modificar. Azure AD admite muchos [roles integrados](./permissions-reference.md) y la lista está creciendo. Para redondear los bordes y cumplir los sofisticados requisitos, Azure AD también admite [roles personalizados](./custom-create.md). La concesión de permisos mediante roles personalizados de Azure AD es un proceso de dos pasos que implica la creación de una definición de roles personalizados y su asignación mediante una asignación de roles. Una definición de roles personalizada es una colección de permisos que se agregan desde una lista preestablecida. Estos permisos son los mismos que se usan en los roles integrados.  

Una vez que se haya creado la definición de roles personalizados (o mediante un rol integrado), puede asignarla a un usuario mediante la creación de una asignación de roles. Una asignación de roles concede al usuario los permisos de una definición de roles en un ámbito específico. Este proceso de dos pasos permite crear una sola definición de roles y asignarla muchas veces en distintos ámbitos. Un ámbito define el conjunto de recursos de Azure AD a los que tiene acceso el miembro del rol. El ámbito más común es el de toda la organización. Un rol personalizado se puede asignar en el ámbito de toda la organización, lo que significa que el miembro del rol tiene los permisos de rol en todos los recursos de la organización. También se puede asignar un rol personalizado en un ámbito de objeto. Un ejemplo del ámbito de objeto sería una aplicación única. Se puede asignar el mismo rol a un usuario en todas las aplicaciones de la organización y, luego, a otro usuario que solo tenga un ámbito de la aplicación de informes de gastos de Contoso.  

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Cómo determina Azure AD si un usuario tiene acceso a un recurso

Los siguientes son los pasos de alto nivel que Azure AD usa para determinar si tiene acceso a un recurso de administración. Use esta información para solucionar los problemas de acceso.

1. Un usuario (o una entidad de servicio) adquiere un token para el punto de conexión de Microsoft Graph o Azure AD Graph.
1. El usuario realiza una llamada API a Azure Active Directory (Azure AD) mediante Microsoft Graph o Azure AD Graph con el token emitido.
1. En función de las circunstancias, Azure AD realiza una de las acciones siguientes:
   - Evalúa la pertenencia del rol del usuario en función de la [notificación de wids](../develop/access-tokens.md) en el token de acceso del usuario.
   - Recupera todas las asignaciones de roles que se aplican al usuario, ya sea directamente o mediante la pertenencia a grupos, en el recurso en el que se realiza la acción.
1. Azure AD determina si la acción en la llamada de API se incluye en los roles que tiene el usuario para este recurso.
1. Si el usuario no tiene un rol con la acción en el ámbito solicitado, no se le concede acceso. En caso contrario, se concede el acceso.

## <a name="role-assignment"></a>Asignación de roles

Una asignación de roles es un recurso de Azure AD que asocia una *definición de rol* a una *entidad de seguridad* en un *ámbito* determinado para conceder acceso a los recursos de Azure AD. El acceso se concede mediante la creación de una asignación de roles y se revoca al quitar una asignación de roles. Básicamente, una asignación de roles consta de tres elementos:

- Entidad de seguridad: una identidad que obtiene los permisos. Podría ser un usuario, grupo o entidad de servicio. 
- Definición de roles: una colección de permisos. 
- Ámbito: una manera de restringir dónde se aplican esos permisos.

Puede [crear asignaciones de roles](manage-roles-portal.md) mediante Azure Portal, Azure AD PowerShell y Graph API. También puede [enumerar las asignaciones de roles](view-assignments.md).

El diagrama siguiente muestra un ejemplo de una asignación de roles. En este ejemplo, se ha asignado a Chris el rol personalizado de administrador del registro de aplicaciones en el ámbito del registro de la aplicación del generador de widgets de Contoso. Esta asignación concede a Chris los permisos del rol de administrador del registro de aplicaciones solo en este registro de aplicación específico.

![La asignación de roles es la manera en que se aplican los permisos y consta de tres partes.](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entidad de seguridad

Una entidad de seguridad representa al usuario o a la entidad de servicio a quien se le va a asignar el acceso a los recursos de Azure AD. Un usuario es un individuo que tiene un perfil de usuario en Azure Active Directory. Un grupo es un grupo de seguridad o de Microsoft 365 con la propiedad "isAssignableToRole" establecida en "true" (actualmente en versión preliminar). Una entidad de servicio es una identidad creada para su uso con aplicaciones, servicios hospedados y herramientas automatizadas para acceder a los recursos de Azure AD.

### <a name="role-definition"></a>Definición de roles

Una definición de roles, o rol, es una colección de permisos. En una definición de roles se muestran las operaciones que se pueden realizar en los recursos de Azure AD, como crear, leer, actualizar y eliminar. Hay dos tipos de roles en Azure AD:

- Roles integrados que Microsoft crea y que no se pueden modificar.
- Roles personalizados que su organización crea y administra.

### <a name="scope"></a>Ámbito

Un ámbito es una manera de limitar las acciones permitidas a un conjunto determinado de recursos como parte de una asignación de roles. Por ejemplo, si quiere asignar un rol personalizado a un desarrollador, pero solo para administrar un registro de aplicación específico, puede incluir el registro de aplicación específico como ámbito en la asignación de roles.

Cuando asigna un rol, puede especificar uno de los siguientes tipos de ámbito:

- Inquilino
- [Unidad administrativa](administrative-units.md)
- Recurso de Azure AD

Si especifica un recurso de Azure AD como ámbito, puede ser uno de los siguientes:

- Grupos de Azure AD
- Aplicaciones empresariales
- Registros de aplicación

Para más información, consulte [Asignación de roles de Azure AD en distintos ámbitos](assign-roles-different-scopes.md).

## <a name="license-requirements"></a>Requisitos de licencia

El uso de roles integrados en Azure AD es gratis, mientras que los roles personalizados requieren una licencia de Azure AD Premium P1. Para obtener la licencia correcta para sus requisitos, consulte [Comparación de las características con disponibilidad general de las ediciones Gratis y Prémium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

## <a name="next-steps"></a>Pasos siguientes

- [Descripción de los roles de Azure AD](concept-understand-roles.md)
- [Asignación de roles de Azure AD a usuarios](manage-roles-portal.md)
- [Crear y asignar un rol personalizado](custom-create.md)
