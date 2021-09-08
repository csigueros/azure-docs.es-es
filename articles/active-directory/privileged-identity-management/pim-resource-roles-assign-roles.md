---
title: 'Asignación de roles de recursos de Azure en Privileged Identity Management: Azure Active Directory | Microsoft Docs'
description: Aprenda a asignar roles de recursos de Azure en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/15/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c50d62d5c8f24ed25258305411f9ed045098c7f
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112232838"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Asignación de roles de recursos de Azure en Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) puede administrar los roles de recurso integrados de Azure, así como los roles personalizados, entre ellos los siguientes:

- Propietario
- Administrador de acceso de usuario
- Colaborador
- Administrador de seguridad
- Administrador de seguridad

> [!NOTE]
> Los usuarios o los miembros de un grupo asignados a los roles de suscripción Propietario o Administrador de acceso de usuario y los administradores globales de Azure AD que permiten la administración de suscripciones en Azure AD tienen permisos de administrador de recursos de forma predeterminada. Estos administradores pueden asignar roles, configurar opciones de rol y revisar el acceso con Privileged Identity Management a los recursos de Azure. Un usuario no puede administrar Privileged Identity Management para recursos sin permisos de administrador de recursos. Consulte la lista de [roles integrados de Azure](../../role-based-access-control/built-in-roles.md).

En Privileged Identity Management se admiten roles de Azure integrados y personalizados. Para más información sobre los roles personalizados de Azure, consulte [Roles personalizados de Azure](../../role-based-access-control/custom-roles.md).

## <a name="role-assignment-conditions"></a>Condiciones de asignación de roles

Puede usar la versión preliminar del control de acceso basado en atributos de Azure (Azure ABAC) para establecer condiciones de recursos en las asignaciones de roles aptos mediante Privileged Identity Management (PIM). Con PIM, los usuarios finales deben activar una asignación de roles apta para obtener permiso para realizar determinadas acciones. El uso de las condiciones de Azure ABAC en PIM permite no solo limitar los permisos de los roles de un usuario a un recurso mediante condiciones específicas, sino también usar PIM para proteger la asignación de roles con una configuración del límite de tiempo, un flujo de trabajo de aprobación y un registro de auditoría, entre otros. Para más información, consulte el artículo sobre la [versión preliminar pública del control de acceso basado en atributos de Azure](../../role-based-access-control/conditions-overview.md).

## <a name="assign-a-role"></a>Asignar un rol

Siga estos pasos para hacer que un usuario sea elegible para un rol de directorio de Azure AD.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con permisos de rol de Administrador de acceso de usuario o Propietario.

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Azure resources** (Recursos de Azure).

1. Use el filtro de recursos para buscar los recursos administrados que está buscando.

    ![Lista de recursos de Azure para administrar](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Seleccione el recurso que desea administrar para abrir la página de información general de recursos.

1. En **Administrar**, seleccione **Roles** para ver la lista de roles de los recursos de Azure.

    ![Roles de recursos de Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Seleccione **Agregar asignaciones** para abrir el panel **Agregar asignaciones**.

1. Elija **Seleccionar un rol** para abrir la página **Seleccionar un rol**.

    ![Panel Nueva asignación](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Seleccione el rol que quiera asignar y luego haga clic en **Seleccionar**.

    Se abre el panel **Seleccionar un miembro o grupo**.

1. Seleccione el miembro o grupo que quiera asignar al rol y luego haga clic en **Seleccionar**.

    ![Panel Seleccionar un miembro o grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. En la pestaña **Configuración**, en la lista **Tipo de asignación**, seleccione **Apto** o **Activo**.

    ![Panel Configuración de pertenencia](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management para recursos de Azure ofrece dos tipos de asignación distintos:

    - Las asignaciones tipo **Apto** requieren que el miembro del rol realice una acción para usar el rol. Entre las acciones se puede incluir realizar una comprobación de autenticación multifactor (MFA), proporcionar una justificación de negocios o solicitar la aprobación de los aprobadores designados.

    - Las asignaciones tipo **Activo** no requieren que el miembro realice ninguna acción para usar el rol. Los miembros asignados como activos tienen siempre los privilegios asignados al rol.

1. Para especificar una duración de asignación específica, cambie las fechas y horas de inicio y finalización.

1. Cuando termine, seleccione **Asignar**.

1. Una vez creada la nueva asignación de roles, se muestra una notificación de estado.

    ![Nueva asignación: notificación](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Actualizar o quitar una asignación de roles existente

Siga estos pasos para actualizar o quiotar una asignación de roles existente.

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Azure resources** (Recursos de Azure).

1. Seleccione el recurso que desea administrar para abrir su página de información general.

1. En **Administrar**, seleccione **Roles** para ver la lista de roles de los recursos de Azure.

    ![Roles de recursos de Azure: seleccionar rol](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Seleccione el rol que quiera actualizar o quitar.

1. Busque la asignación de roles en las pestañas **Roles elegibles** o **Roles activos**.

    ![Actualizar o quitar la asignación de roles](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Para agregar o actualizar una condición para refinar el acceso a los recursos de Azure, seleccione **Agregar** o **Ver o editar** en la columna **Condición** de la asignación de roles. Actualmente, los roles Propietario de datos de Storage Blob, Lector de datos de Storage Blob y Colaborador de datos de Storage Blob en Privileged Identity Management son los únicos que se admiten como parte de la [versión preliminar pública del control de acceso basado en atributos de Azure](../../role-based-access-control/conditions-overview.md).

    ![Actualización o eliminación de atributos para el control de acceso](./media/pim-resource-roles-assign-roles/resources-abac-update-remove.png)

1. Seleccione **Actualizar** o **Quitar** para actualizar o quitar la asignación de roles.

    Para obtener información sobre cómo ampliar una asignación de roles, consulte [Ampliación o renovación de roles de recursos de Azure en Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Pasos siguientes

- [Ampliación o renovación de roles de recursos de Azure en Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Configuración de las opciones de rol de recursos de Azure en Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Asignación de roles de Azure AD en Privileged Identity Management](pim-how-to-add-role-to-user.md)
