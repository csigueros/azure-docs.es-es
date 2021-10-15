---
title: 'Asignación de roles de recursos de Azure en Privileged Identity Management: Azure Active Directory | Microsoft Docs'
description: Aprenda a asignar roles de recursos de Azure en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 09/28/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0837fc83bec560f12c31da1d9f0001b5bdd7768f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669936"
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

Puede usar la versión preliminar del control de acceso basado en atributos de Azure (Azure ABAC) para establecer condiciones de recursos en las asignaciones de roles aptos mediante Privileged Identity Management (PIM). Con PIM, los usuarios finales deben activar una asignación de roles apta para obtener permiso para realizar determinadas acciones. El uso de las condiciones de control de acceso basado en atributos en PIM permite no solo limitar los permisos de los roles de un usuario a un recurso mediante condiciones específicas, sino también usar PIM para proteger la asignación de roles con una configuración del límite de tiempo, un flujo de trabajo de aprobación y un registro de auditoría, entre otros. Para más información, consulte el artículo sobre la [versión preliminar pública del control de acceso basado en atributos de Azure](../../role-based-access-control/conditions-overview.md).

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

## <a name="assign-a-role-using-arm-api"></a>Asignación de un rol mediante ARM API

Privileged Identity Management admite los comandos de Azure Resource Manager (ARM) API para administrar los roles de los recursos de Azure, como se documenta en la [referencia de ARM API de PIM](/rest/api/authorization/roleeligibilityschedulerequests). Para obtener los permisos necesarios para usar PIM API, consulte [Descripción de las API de Privileged Identity Management](pim-apis.md).

A continuación se muestra una solicitud HTTP de ejemplo para crear una asignación apta para un rol de Azure.

### <a name="request"></a>Solicitud

````HTTP
PUT https://management.azure.com/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleEligibilityScheduleRequests/64caffb6-55c0-4deb-a585-68e948ea1ad6?api-version=2020-10-01-preview
````

### <a name="request-body"></a>Cuerpo de la solicitud

````JSON
{
  "properties": {
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "requestType": "AdminAssign",
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:31:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "P365D"
      }
    },
    "condition": "@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:ContainerName] StringEqualsIgnoreCase 'foo_storage_container'",
    "conditionVersion": "1.0"
  }
}
````

### <a name="response"></a>Response

Código de estado: 201

````HTTP
{
  "properties": {
    "targetRoleEligibilityScheduleId": "b1477448-2cc6-4ceb-93b4-54a202a89413",
    "targetRoleEligibilityScheduleInstanceId": null,
    "scope": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "principalType": "User",
    "requestType": "AdminAssign",
    "status": "Provisioned",
    "approvalId": null,
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:31:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "P365D"
      }
    },
    "ticketInfo": {
      "ticketNumber": null,
      "ticketSystem": null
    },
    "justification": null,
    "requestorId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "createdOn": "2020-09-09T21:32:27.91Z",
    "condition": "@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:ContainerName] StringEqualsIgnoreCase 'foo_storage_container'",
    "conditionVersion": "1.0",
    "expandedProperties": {
      "scope": {
        "id": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
        "displayName": "Pay-As-You-Go",
        "type": "subscription"
      },
      "roleDefinition": {
        "id": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
        "displayName": "Contributor",
        "type": "BuiltInRole"
      },
      "principal": {
        "id": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
        "displayName": "User Account",
        "email": "user@my-tenant.com",
        "type": "User"
      }
    }
  },
  "name": "64caffb6-55c0-4deb-a585-68e948ea1ad6",
  "id": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/RoleEligibilityScheduleRequests/64caffb6-55c0-4deb-a585-68e948ea1ad6",
  "type": "Microsoft.Authorization/RoleEligibilityScheduleRequests"
}
````

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
