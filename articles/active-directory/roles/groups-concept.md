---
title: 'Uso de grupos de Azure AD para administrar asignaciones de roles: Azure Active Directory'
description: Use grupos de Azure AD para simplificar la administración de asignaciones de roles en Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 07/30/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bebba34e8d323bc60d83151e7b211388776b7b76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741695"
---
# <a name="use-azure-ad-groups-to-manage-role-assignments"></a>Uso de grupos de Azure AD para administrar asignaciones de roles

Azure Active Directory (Azure AD) le permite seleccionar grupos de Azure AD para las asignaciones de roles. La asignación de roles a grupos puede simplificar la administración de las asignaciones de roles en Azure AD con el mínimo esfuerzo de los administradores globales y los administradores de roles con privilegios.

## <a name="why-assign-roles-to-groups"></a>Motivo para asignar roles a grupos

Considere el ejemplo en el que la empresa Contoso ha contratado a personas en distintas zonas geográficas para administrar y restablecer las contraseñas de los empleados de su organización de Azure AD. En lugar de solicitar a un administrador de roles con privilegios o a un administrador global que asignen el rol de administrador del departamento de soporte técnico a cada persona individualmente, pueden crear un grupo Contoso_Helpdesk_Administrators y asignarle el rol. Cuando los usuarios se unen al grupo, se les asigna el rol de forma indirecta. Después, el flujo de trabajo de gobernanza existente puede encargarse del proceso de aprobación y la auditoría de la pertenencia al grupo para asegurarse de que solo los usuarios legítimos sean miembros del grupo, a fin de asignarles el rol de administrador del departamento de soporte técnico.

## <a name="how-role-assignments-to-groups-work"></a>Funcionamiento de las asignaciones de roles a grupos

Para asignar un rol a un grupo, debe crear un grupo de seguridad o de Microsoft 365 con la propiedad `isAssignableToRole` establecida en `true`. En Azure Portal, establezca la opción **Los roles de Azure AD se pueden asignar a un grupo** en **Sí**. En cualquier caso, puede asignar al grupo uno o varios roles de Azure AD de la misma manera que asigna roles a los usuarios.

![Captura de pantalla de la página Roles y administradores](./media/groups-concept/role-assignable-group.png)

## <a name="restrictions-for-role-assignable-groups"></a>Restricciones para grupos a los que se pueden asignar roles

Los grupos a los que se pueden asignar roles tienen las restricciones siguientes:

- Solo puede establecer la propiedad `isAssignableToRole` o la opción **Los roles de Azure AD se pueden asignar a un grupo** para los nuevos grupos.
- La propiedad `isAssignableToRole` es **inmutable**. Una vez que se crea un grupo con esta propiedad establecida, ya no se puede modificar.
- No se puede convertir un grupo existente en un grupo al que se pueden asignar roles.
- Se puede crear un máximo de 300 grupos a los que se puedan asignar roles en una sola organización de Azure AD (inquilino).

## <a name="how-are-role-assignable-groups-protected"></a>Maneras de proteger grupos a los que se pueden asignar roles

Si se asigna un rol a un grupo, cualquier administrador de TI que pueda administrar la pertenencia a grupos también podría administrar indirectamente la pertenencia a ese rol. Por ejemplo, supongamos que se asigna a un grupo llamado Contoso_User_Administrators el rol de administrador de usuarios. Un administrador de Exchange que puede modificar la pertenencia a grupos podría agregarse a sí mismo al grupo Contoso_User_Administrators y, de esta forma, convertirse en administrador de usuarios. Como puede ver, un administrador podría elevar sus privilegios de una forma no prevista.

Solo se puede asignar un rol a los grupos que tengan la propiedad `isAssignableToRole` establecida en `true` en el momento de la creación. Esta propiedad es inmutable. Una vez que se crea un grupo con esta propiedad establecida, ya no se puede modificar. No se puede establecer la propiedad en un grupo existente.

Los grupos a los que se pueden asignar roles están diseñados para ayudar a evitar posibles infracciones mediante las restricciones siguientes:

- Solo los administradores globales y los administradores que tengan un rol con privilegios pueden crear un grupo al que se pueden asignar roles.
- El tipo de pertenencia para los grupos a los que se pueden asignar roles debe ser Asignado y no puede ser un grupo dinámico de Azure AD. El rellenado automático de grupos dinámicos podría dar lugar a la adición de una cuenta no deseada al grupo y, por tanto, a la asignación del rol.
- De manera predeterminada, solo los administradores globales y los administradores de roles con privilegios pueden administrar la pertenencia de un grupo al que se pueden asignar roles, pero es posible delegar la administración de grupos a los que se pueden asignar roles agregando propietarios del grupo.
- El permiso RoleManagement.ReadWrite.All de Microsoft Graph es necesario para poder administrar la pertenencia de dichos grupos; Group.ReadWrite.All no funcionará.
- Para evitar la elevación de privilegios, solo un administrador de autenticación con privilegios o un administrador global pueden cambiar las credenciales o restablecer la MFA para los miembros y propietarios de un grupo al que se pueden asignar roles.
- No se admite la anidación de grupos. No se puede agregar un grupo como miembro de un grupo al que se pueden asignar roles.

## <a name="use-pim-to-make-a-group-eligible-for-a-role-assignment"></a>Uso de PIM para que un grupo sea apto para una asignación de roles

Si no desea que los miembros del grupo tengan acceso permanente al rol, puede usar [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) para que un grupo sea apto para una asignación de roles. Cada miembro del grupo es apto para activar la asignación de roles durante un tiempo fijo.

> [!Note]
> Debe usar una versión actualizada de PIM para poder asignar un rol de Azure AD a un grupo. Podría tener una versión anterior de PIM porque su organización de Azure AD utiliza la API de PIM. Envíe un correo electrónico a pim_preview@microsoft.com para trasladar la organización y actualizar la API. Para más información, vea [Roles y características de Azure AD en PIM](../privileged-identity-management/pim-configure.md).

## <a name="scenarios-not-supported"></a>Escenarios no admitidos

Los siguientes escenarios no se admiten:  

- Asignar roles de Azure AD (integrados o personalizados) a grupos locales

## <a name="known-issues"></a>Problemas conocidos

A continuación, de indican los problemas conocidos con los grupos a los que se pueden asignar roles:

- *Solo clientes con licencia de Azure AD P2*: incluso después de eliminar el grupo, aún se muestra un miembro apto del rol en la interfaz de usuario de PIM. Funcionalmente, no hay ningún problema. Se trata simplemente de un problema de caché de Azure Portal.  
- Use el nuevo [Centro de administración de Exchange](https://admin.exchange.microsoft.com/) para la asignación de roles mediante la pertenencia a grupos. El antiguo Centro de administración de Exchange no es compatible aún con esta característica. Los cmdlets de PowerShell de Exchange funcionarán según lo previsto.
- El portal de Azure Information Protection (el portal clásico) todavía no reconoce la pertenencia al rol mediante el grupo. Puede [migrar a la plataforma de etiquetado de confidencialidad unificada](/azure/information-protection/configure-policy-migrate-labels) y, a continuación, usar el Centro de seguridad y cumplimiento de Office 365 a fin de utilizar las asignaciones de grupo para administrar roles.
- [Apps Admin Center](https://config.office.com/) todavía no es compatible con esta característica. Asigne usuarios directamente al rol de administrador de aplicaciones de Office.

## <a name="license-requirements"></a>Requisitos de licencia

Necesita una licencia de Azure AD Premium P1 para usar esta característica. Para usar también Privileged Identity Management en la activación del rol Just-in-Time, se necesita una licencia de Azure AD Premium P2. Para obtener la licencia correcta para sus requisitos, consulte [Comparación de las características con disponibilidad general de las ediciones Gratis y Prémium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un grupo al que se pueden asignar roles](groups-create-eligible.md)
- [Asignación de roles de Azure AD a grupos](groups-assign-role.md)
