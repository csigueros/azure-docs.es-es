---
title: ¿Cuál es la diferencia entre los grupos de acceso con privilegios y los grupos a los que se pueden asignar roles? - Azure AD | Microsoft Docs
description: Obtenga información sobre cómo diferenciar entre los grupos de acceso con privilegios y los grupos a los que se pueden asignar roles en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/02/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5f9d968c114c28dcff8e247435c40960040c4e9
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396786"
---
# <a name="whats-the-difference-between-privileged-access-groups-and-role-assignable-groups"></a>¿Cuál es la diferencia entre los grupos de acceso con privilegios y los grupos a los que se pueden asignar roles?

Privileged Identity Management (PIM) admite la capacidad de habilitar el acceso con privilegios en grupos a los que se pueden asignar roles. Pero dado que se requiere que haya un grupo al que se pueden asignar roles disponible para crear un grupo de acceso con privilegios, en este artículo se explican las diferencias y cómo aprovecharlas.

## <a name="what-are-azure-ad-role-assignable-groups"></a>¿Qué son los grupos a los que se pueden asignar roles de Azure AD?

Azure AD le permite asignar un grupo de seguridad de Azure AD en la nube a un rol de Azure AD. Los administradores globales y los administradores de roles con privilegios deben crear un nuevo grupo de seguridad y permitir que se le asignen roles en el momento de la creación. Solo los usuarios con roles Administrador global, Administrador de roles con privilegios o Propietario del grupo pueden cambiar la pertenencia del grupo. Además, ningún otro usuario puede restablecer la contraseña de los usuarios que son miembros del grupo. Esta característica ayuda a evitar que los administradores cambien los roles a otro con más privilegios sin pasar por un procedimiento de solicitud y aprobación.

## <a name="what-are-privileged-access-groups"></a>¿Qué son los grupos de acceso con privilegios?

Los grupos de acceso con privilegios permiten a los usuarios ascender al rol de propietario o miembro de un grupo de seguridad de Azure AD. Esta característica permite configurar flujos de trabajo Just-In-Time no solo para roles de Azure AD y Azure en lotes, y también permite escenarios Just-In-Time para otros casos de uso como Azure SQL, Azure Key Vault, Intune u otros roles de aplicación. Para más información, consulte [Funcionalidades de grupos de acceso con privilegios](groups-features.md).

>[!Note]
>En el caso de los grupos de acceso con privilegios que se usan para elevar a roles de Azure AD, Microsoft recomienda requerir un proceso de aprobación para las asignaciones de miembros elegibles. Las asignaciones que se pueden activar sin aprobación pueden plantearle un riesgo de seguridad por parte de administradores con menos privilegios. Por ejemplo, el administrador del departamento de soporte técnico tiene permiso para restablecer las contraseñas de un usuario que reúna las características para ello.

## <a name="when-to-use-each-type-of-group"></a>Cuándo usar cada tipo de grupo

Puede configurar el acceso Just-In-Time a permisos y roles más allá de Azure AD y recursos de Azure. Si tiene otros recursos cuya autorización se puede conectar a un grupo de seguridad de Azure AD (para Azure Key Vault, Intune, Azure SQL u otras aplicaciones y servicios), debe habilitar el acceso con privilegios en el grupo y determinar cuáles usuarios son aptos para pertenecer al grupo.

Si desea asignar un grupo a un rol de Azure AD o recurso de Azure y requerir elevación a través de un proceso de PIM, hay dos maneras de hacerlo:

- **Asignar el grupo de manera persistente a un rol**. A continuación, conceda a los usuarios acceso de miembro apto al grupo en PIM. Los usuarios aptos deben entonces activar su pertenencia para acceder al grupo que está asignado permanentemente al rol. Esta ruta de acceso requiere que se habilite un grupo al que se pueden asignar roles en PIM como grupo de acceso con privilegios para el rol de Azure AD.
- **Asigne el grupo como apto para un rol** a través de PIM. Todos los miembros del grupo deben activar su asignación para obtener acceso al rol. Esta ruta requiere un grupo al que se pueden asignar roles para el rol de Azure AD y un grupo de seguridad para los recursos de Azure.

    ![Diagrama que muestra dos maneras de asignar roles mediante grupos de acceso con privilegios en PIM.](./media/concept-privileged-access-versus-role-assignable/concept-privileged-access.png)

Cualquiera de estos métodos funcionará para el escenario de un extremo a otro. Se recomienda usar el primer método en la mayoría de los casos. Solo debe usar el segundo método si está intentando:

- Asignar un grupo a varios roles de Azure AD o recursos de Azure y hacer que los usuarios se activen una vez para obtener acceso a varios roles.
- Mantener distintas directivas de activación para que distintos conjuntos de usuarios para acceder a un rol de Azure AD o de recursos de Azure. Por ejemplo, si desea que algunos usuarios pasen por una aprobación antes de convertirse en Administrador global y permitir al mismo tiempo que otros usuarios se aprueben automáticamente, puede configurar dos grupos de acceso con privilegios, asignar ambos de forma persistente (una asignación "permanente" en Privileged Identity Management) al rol de Administrador global y luego utilizar una directiva de activación diferente para el rol de miembro de cada grupo.

## <a name="next-steps"></a>Pasos siguientes

- [Aprobación o rechazo de solicitudes para los roles de Azure AD](azure-ad-pim-approval-workflow.md)
- [Aprobación o rechazo de solicitudes de roles de recursos de Azure](pim-resource-roles-approval-workflow.md)
- [Aprobación de solicitudes de activación para miembros y propietarios de un grupo de acceso con privilegios (versión preliminar)](groups-approval-workflow.md)
