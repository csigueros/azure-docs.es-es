---
title: 'Asignación de roles de Azure AD en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a asignar roles de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/03/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.custom: subject-rbac-steps
ms.openlocfilehash: a741ce7fff528fbe1f4120f4138a88d7b6e2e915
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233014"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Asignación de roles de Azure AD en Privileged Identity Management

Con Azure Active Directory (Azure AD), un administrador global puede realizar asignaciones de roles de administrador de Azure AD **permanentes**. Estas asignaciones de roles se pueden crear mediante [Azure Portal](../roles/permissions-reference.md) o mediante [comandos de PowerShell](/powershell/module/azuread#directory_roles).

El servicio Azure AD Privileged Identity Management (PIM) permite también a los administradores de roles con privilegios realizar asignaciones de roles de administrador permanentes. Además, los administradores de rol con privilegios pueden hacer que los usuarios sean **aptos** para roles de administrador de Azure AD. Un administrador apto puede activar el rol cuando lo necesite y, cuando termina, sus permisos caducan.

En Privileged Identity Management se admiten roles de Azure AD integrados y personalizados. Para obtener más información sobre roles personalizados de Azure AD, vea [Control de acceso basado en rol en Azure Active Directory](../roles/custom-overview.md).

## <a name="assign-a-role"></a>Asignar un rol

Siga estos pasos para hacer que un usuario sea elegible para un rol de administrador de Azure AD.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con un usuario que sea miembro del rol [Administrador de roles con privilegios](../roles/permissions-reference.md#privileged-role-administrator).

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Roles de Azure AD**.

1. Seleccione **Roles** para ver la lista de roles de los permisos de Azure AD.

    ![Captura de pantalla de la página "Roles" con la acción "Agregar asignaciones" seleccionada.](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Seleccione **Agregar asignaciones** para abrir la página **Agregar asignaciones**.

1. Elija **Seleccionar un rol** para abrir la página **Seleccionar un rol**.

    ![Panel Nueva asignación](./media/pim-how-to-add-role-to-user/select-role.png)

1. Seleccione el rol que desea asignar, seleccione un miembro al que desee asignar el rol y luego, seleccione **Siguiente**.

1. En la lista **Tipo de asignación** en el panel **Configuración de pertenencia**, seleccione **Apto** o **Activo**.

    - Las asignaciones tipo **Apto** requieren que el miembro del rol realice una acción para usar el rol. Entre las acciones se puede incluir realizar una comprobación de autenticación multifactor (MFA), proporcionar una justificación de negocios o solicitar la aprobación de los aprobadores designados.

    - Las asignaciones tipo **Activo** no requieren que el miembro realice ninguna acción para usar el rol. Los miembros asignados como activos tienen siempre los privilegios asignados al rol.

1. Para especificar una duración de asignación específica, agregue un cuadro de fecha y hora de inicio y de finalización. Cuando termine, seleccione **Asignar** para crear la nueva asignación de roles.

    ![Configuración de pertenencias: fecha y hora](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Una vez asignado el rol, se muestra una notificación de estado de la asignación.

    ![Nueva asignación: notificación](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Asignación de un rol con ámbito restringido

Para determinados roles, el ámbito de los permisos concedidos se puede restringir a una sola unidad de administración, entidad de servicio o aplicación. Este procedimiento es un ejemplo si se asigna un rol con el ámbito de una unidad administrativa. Para obtener una lista de los roles que admiten la determinación del ámbito mediante una unidad administrativa, consulte [Asignación de roles con ámbito a una unidad administrativa](../roles/admin-units-assign-roles.md). Esta característica se está implementando actualmente en las organizaciones de Azure AD.

1. Inicie sesión en el [centro de administración de Azure Active Directory](https://aad.portal.azure.com) con permisos de administrador de roles con privilegios.

1. Seleccione **Azure Active Directory** > **Roles y administradores**.

1. Seleccione el rol **Administrador de usuarios**.

    ![El comando Agregar asignación está disponible cuando se abre un rol en el portal.](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Seleccione **Agregar asignaciones**.

    ![Cuando un rol admite la restricción de ámbito, puede seleccionar un ámbito.](./media/pim-how-to-add-role-to-user/add-scope.png)

1. En la página **Agregar asignaciones**, puede hacer lo siguiente:

   - Seleccionar el usuario o el grupo que se va a asignar al rol
   - Seleccionar el ámbito del rol (en este caso, unidades administrativas)
   - Seleccionar una unidad administrativa para el ámbito

Para más información sobre la creación de unidades administrativas, consulte [Adición y eliminación de unidades administrativas](../roles/admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Actualizar o quitar una asignación de roles existente

Siga estos pasos para actualizar o quiotar una asignación de roles existente. **Solo clientes con licencia de Azure AD P2**: No asigne un grupo como activo a un rol a través de Azure AD y Privileged Identity Management (PIM). Para obtener una explicación detallada, consulte [Problemas conocidos](../roles/groups-concept.md#known-issues).

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Roles de Azure AD**.

1. Seleccione **Roles** para ver la lista de roles de Azure AD.

1. Seleccione el rol que quiera actualizar o quitar.

1. Busque la asignación de roles en las pestañas **Roles elegibles** o **Roles activos**.

    ![Actualizar o quitar la asignación de roles](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Seleccione **Actualizar** o **Quitar** para actualizar o quitar la asignación de roles.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración del rol de administrador de Azure AD en Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Asignación de roles de recursos de Azure en Privileged Identity Management](pim-resource-roles-assign-roles.md)