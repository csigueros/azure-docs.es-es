---
title: 'Permisos de administración de grupos para roles personalizados de Azure AD (versión preliminar): Azure Active Directory'
description: Permisos de administración de grupos para roles personalizados de Azure AD (versión preliminar) en Azure Portal, PowerShell o Microsoft Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 10/26/2021
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.openlocfilehash: 5e768d9661a7030fb75d8089a6a49e9d5022d612
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057217"
---
# <a name="group-management-permissions-for-azure-ad-custom-roles-preview"></a>Permisos de administración de grupos para roles personalizados de Azure AD (versión preliminar)

> [!IMPORTANT]
> Los permisos de administración de grupos para roles personalizados de Azure AD se encuentran actualmente en VERSIÓN PRELIMINAR.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Los permisos de administración de grupos se pueden usar en definiciones de roles personalizados en Azure Active Directory (Azure AD) para conceder accesos específicos, como los siguientes:

- Administrar propiedades de grupo como nombre y descripción
- Administrar miembros y propietarios
- Crear o eliminar grupos
- Leer registros de auditoría
- Administrar un tipo específico de grupo

En este artículo se enumeran los permisos que puede usar en los roles personalizados para distintos escenarios de administración de grupos. Para obtener información sobre cómo crear roles personalizados, vea [Creación y asignación de un rol personalizado](custom-create.md).

> [!NOTE]
> La asignación de roles personalizados en un ámbito de grupo mediante Azure Portal está disponible actualmente **solo** para Azure AD Premium P1.

## <a name="how-to-interpret-group-management-permissions"></a>Interpretación de los permisos de administración de grupos

La interpretación de los permisos de administración de grupos ayuda a comprender lo que significan los diferentes subtipos de permiso.

> [!div class="mx-tableFixed"]
> | Subtipo de permiso | Descripción del subtipo de permiso |
> | --- | --- |
> | groups | Administrar grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | groups.unified | Administrar grupos de Microsoft 365 de tipo de pertenencia dinámica y asignada, excepto los grupos a los que se pueden asignar roles |
> | groups.unified.assignedMembership | Administrar grupos de Microsoft 365 solo de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |
> | groups.security | Administrar grupos de seguridad de tipo de pertenencia dinámica y asignada, excepto los grupos a los que se pueden asignar roles |
> | groups.security.assignedMembership | Administrar grupos de seguridad solo de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |

La tabla siguiente tiene permisos de ejemplo para actualizar los miembros del grupo de diferentes subtipos. 

> [!div class="mx-tableFixed"]
> | Ejemplo de permiso | Descripción del permiso |
> | --- | --- |
> | microsoft.directory/**groups**/members/update | Actualizar los miembros de los grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/**groups.unified**/members/update | Actualizar los miembros de los grupos de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/**groups.unified.assignedMembership**/members/update | Actualizar miembros de grupos de Microsoft 365 de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/**groups.security**/members/update | Actualizar los miembros de los grupos de seguridad, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/**groups.security.assignedMembership**/members/update | Actualizar miembros de grupos de seguridad de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |

## <a name="read-group-information"></a>Lectura de información de grupos

Los permisos siguientes están disponibles para leer propiedades, miembros y propietarios de grupos.

> [!div class="mx-tableFixed"]
> | Permiso | Descripción |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/read | Lea todas las propiedades (incluidas las propiedades con privilegios) de los grupos de seguridad y de Microsoft 365, incluidos los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups/standard/read | Leer las propiedades estándar de los grupos de seguridad y de Microsoft 365, incluidos los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups/members/read | Leer los miembros de los grupos de seguridad y de Microsoft 365, incluidos los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups/memberOf/read | Leer la propiedad memberOf de los grupos de seguridad y de Microsoft 365, incluidos los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups/owners/read | Leer los propietarios de los grupos de seguridad y de Microsoft 365, incluidos los grupos a los que se puedan asignar roles |

## <a name="create-groups"></a>Creación de los grupos

Los permisos siguientes están disponibles para crear grupos de tipos diferentes.

> [!div class="mx-tableFixed"]
> | Permiso | Descripción |
> | ---------- | ----------- |
> | microsoft.directory/groups/create | Crear grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.unified/create | Crear grupos de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.unified.assignedMembership/create | Crear grupos de Microsoft 365 de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security/create | Crear grupos de seguridad, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security.assignedMembership/create | Crear grupos de seguridad de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups/createAsOwner | Crear grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles El creador se agrega como primer propietario. |
> | microsoft.directory/groups.unified/createAsOwner | Crear grupos de Microsoft 365, excepto los grupos a los que se pueden asignar roles El creador se agrega como primer propietario. |
> | microsoft.directory/groups.unified.assignedMembership/createAsOwner | Crear grupos de Microsoft 365 de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles El creador se agrega como primer propietario. |
> | microsoft.directory/groups.security/createAsOwner | Crear grupos de seguridad, excepto los grupos a los que se pueden asignar roles El creador se agrega como primer propietario. |
> | microsoft.directory/groups.security.assignedMembership/createAsOwner | Crear grupos de seguridad de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles El creador se agrega como primer propietario. |

## <a name="update-group-information"></a>Actualización de la información de grupos

Los permisos siguientes están disponibles para actualizar las propiedades y los miembros de los grupos.

> [!div class="mx-tableFixed"]
> | Permiso | Descripción |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/update | Actualizar todas las propiedades (incluidas las propiedades con privilegios) de los grupos de seguridad y de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.unified/allProperties/update | Actualizar todas las propiedades (incluidas las propiedades con privilegios) de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.unified.assignedMembership/allProperties/update | Actualizar todas las propiedades (incluidas las propiedades con privilegios) de los grupos de Microsoft 365 de tipo de pertenencia asignado, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.security/allProperties/update | Actualizar todas las propiedades (incluidas las propiedades con privilegios) de los grupos de seguridad, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security.assignedMembership/allProperties/update | Actualizar todas las propiedades (incluidas las propiedades con privilegios) de los grupos de seguridad de tipo de pertenencia asignado, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups/basic/update | Actualizar las propiedades básicas de los grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.unified/basic/update | Actualizar las propiedades básicas de los grupos de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.unified.assignedMembership/basic/update | Actualizar las propiedades básicas de los grupos de Microsoft 365 de tipo de pertenencia asignado, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.security/basic/update | Actualizar las propiedades básicas de los grupos de seguridad, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security.assignedMembership/basic/update | Actualizar las propiedades básicas de los grupos de seguridad de tipo de pertenencia asignado, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups/classification/update | Actualizar la propiedad de clasificación de los grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.unified/classification/update | Actualizar la propiedad de clasificación de los grupos de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.unified.assignedMembership/classification/update | Actualizar la propiedad de clasificación de los grupos de Microsoft 365 de tipo de pertenencia asignado, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.security/classification/update | Actualizar la propiedad de clasificación de los grupos de seguridad, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security.assignedMembership/classification/update | Actualizar la propiedad de clasificación de los grupos de seguridad de tipo de pertenencia asignado, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups/dynamicMembershipRule/update | Actualizar la regla de membresía dinámica de los grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.unified/dynamicMembershipRule/update | Actualizar la regla de membresía dinámica de los grupos de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Actualizar la regla de pertenencia dinámica de grupos de seguridad, excepto los grupos a los que se pueden asignar roles. |
> | microsoft.directory/groups/members/update | Actualizar los miembros de los grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.unified/members/update | Actualizar los miembros de los grupos de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Actualizar miembros de grupos de Microsoft 365 de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security/members/update | Actualizar los miembros de los grupos de seguridad, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security.assignedMembership/members/update | Actualizar miembros de grupos de seguridad de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |

## <a name="update-members-of-different-group-types"></a>Actualización de miembros de diferentes tipos de grupo

Los permisos siguientes están disponibles para actualizar miembros de diferentes tipos de grupo.

> [!div class="mx-tableFixed"]
> | Permiso | Descripción |
> | ---------- | ----------- |
> | microsoft.directory/groups/members/update | Actualizar los miembros de los grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.unified/members/update | Actualizar los miembros de los grupos de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Actualizar miembros de grupos de Microsoft 365 de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security/members/update | Actualizar los miembros de los grupos de seguridad, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security.assignedMembership/members/update | Actualizar miembros de grupos de seguridad de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |
 
## <a name="delete-groups"></a>Eliminación de grupos

Los permisos siguientes están disponibles para eliminar grupos.

> [!div class="mx-tableFixed"]
> | Permiso | Descripción |
> | ---------- | ----------- |
> | microsoft.directory/groups/delete | Eliminar grupos de seguridad y de Microsoft 365, excepto los grupos a los que se puedan asignar roles |
> | microsoft.directory/groups.unified/members/update | Actualizar los miembros de los grupos de Microsoft 365, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.unified.assignedMembership/members/update | Actualizar miembros de grupos de Microsoft 365 de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security/members/update | Actualizar los miembros de los grupos de seguridad, excepto los grupos a los que se pueden asignar roles |
> | microsoft.directory/groups.security.assignedMembership/members/update | Actualizar miembros de grupos de seguridad de tipo de pertenencia asignada, excepto los grupos a los que se pueden asignar roles |

## <a name="next-steps"></a>Pasos siguientes

- [Creación y asignación de un rol personalizado en Azure Active Directory](custom-create.md)
- [Enumeración de asignaciones de roles de Azure AD](view-assignments.md)
