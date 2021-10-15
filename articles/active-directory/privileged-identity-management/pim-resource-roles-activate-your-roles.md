---
title: Active los roles de recursos de Azure en PIM - Azure AD | Microsoft Docs
description: Aprenda a activar sus roles de recursos de Azure en Azure AD Privileged Identity Management (PIM).
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
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c87f4d4baff8b19ea7512ac387525a31339e7294
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669062"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Active mis roles de recursos de Azure en Privileged Identity Management

Use Privileged Identity Management (PIM) para permitir que los miembros del rol válidos para los recursos de Azure programen la activación de una fecha y hora futuras. Asimismo, también pueden seleccionar una duración de activación específica dentro del valor máximo establecido (configurado por los administradores).

Este artículo es para los miembros que necesitan activar su rol de recurso de Azure en Privileged Identity Management.

## <a name="activate-a-role"></a>Activación de un rol

Cuando necesite asumir un rol de recurso de Azure, puede solicitar la activación mediante la opción de navegación **Mis roles** en Privileged Identity Management.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Abra **Azure AD Privileged Identity Management**. Para información sobre cómo agregar el icono de Privileged Identity Management en el panel, consulte [Primer uso de PIM](pim-getting-started.md).

1. Seleccione **Mis roles**.

    ![Mi página de roles en la que se muestran los roles que el usuario puede activar](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Seleccione **Roles de recursos de Azure** para ver una lista de los roles de recursos de Azure válidos.

    ![Mis roles: página de roles de recursos de Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. En la lista **Roles de recursos de Azure**, busque el rol que desea activar.

    ![Roles de recursos de Azure: mi lista de roles elegibles](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Seleccione **Activar** para abrir la Página de activación.

    ![El panel de activación abierto con el ámbito, la hora de inicio, la duración y el motivo](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Si el rol requiere la autenticación multifactor, seleccione **Compruebe su identidad antes de proceder**. Solo tiene que autenticarse una vez por sesión.

    ![Comprobación de mi identidad con MFA antes de la activación del rol](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Seleccione **Comprobar mi identidad** y siga las instrucciones para proporcionar comprobación de seguridad adicional.

    ![Pantalla para proporcionar una comprobación de seguridad, como un código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Si desea especificar un ámbito reducido, seleccione **Ámbito** para abrir el Panel filtro de recursos.

    Es una práctica recomendada solo solicitar acceso a los recursos que necesita. En el panel de filtro de recursos, puede especificar los grupos de recursos o recursos a los que necesita acceso.

    ![Activar: panel de filtros de recursos para especificar el ámbito](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Si es necesario, especifique una hora de inicio de activación personalizada. El miembro se activaría después de la hora seleccionada.

1. En el campo **Razón**, escriba el motivo de la solicitud de activación.

    ![Panel Activar completado con el ámbito, la hora de inicio, la duración y el motivo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Seleccione **Activar**.

    Si el [rol requiere aprobación](pim-resource-roles-approval-workflow.md) para activarse, aparecerá una notificación en la esquina superior del explorador que le informa de que la solicitud está pendiente de aprobación.

    ![La solicitud de activación está pendiente de notificación de aprobación](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="activate-a-role-with-arm-api"></a>Activación de un rol con ARM API

Privileged Identity Management admite los comandos de Azure Resource Manager (ARM) API para administrar los roles de los recursos de Azure, como se documenta en la [referencia de ARM API de PIM](/rest/api/authorization/roleeligibilityschedulerequests). Para obtener los permisos necesarios para usar PIM API, consulte [Descripción de las API de Privileged Identity Management](pim-apis.md).

A continuación se muestra una solicitud HTTP de ejemplo para activar una asignación apta para un rol de Azure.

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
    "requestType": "SelfActivate",
    "linkedRoleEligibilityScheduleId": "b1477448-2cc6-4ceb-93b4-54a202a89413",
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:35:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "PT8H"
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
    "targetRoleAssignmentScheduleId": "c9e264ff-3133-4776-a81a-ebc7c33c8ec6",
    "targetRoleAssignmentScheduleInstanceId": null,
    "scope": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "principalType": "User",
    "requestType": "SelfActivate",
    "status": "Provisioned",
    "approvalId": null,
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:35:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "PT8H"
      }
    },
    "ticketInfo": {
      "ticketNumber": null,
      "ticketSystem": null
    },
    "justification": null,
    "requestorId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "createdOn": "2020-09-09T21:35:27.91Z",
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
  "name": "fea7a502-9a96-4806-a26f-eee560e52045",
  "id": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/RoleAssignmentScheduleRequests/fea7a502-9a96-4806-a26f-eee560e52045",
  "type": "Microsoft.Authorization/RoleAssignmentScheduleRequests"
}
````

## <a name="view-the-status-of-your-requests"></a>Visualización del estado de las solicitudes

Puede ver el estado de las solicitudes pendientes de activación.

1. Abra Azure AD Privileged Identity Management.

1. Seleccione **Mis solicitudes** para ver una lista de los roles de Azure AD y las solicitudes de rol de recursos de Azure.

    ![Mis solicitudes: página de recursos de Azure que muestra las solicitudes pendientes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Desplácese a la derecha para ver la columna **Estado de solicitud**.

## <a name="cancel-a-pending-request"></a>Cancelación de una solicitud pendiente

Si no necesita activar un rol que requiera aprobación, puede cancelar una solicitud pendiente en cualquier momento.

1. Abra Azure AD Privileged Identity Management.

1. Seleccione **Mis solicitudes**.

1. En el rol que desea cancelar, seleccione el vínculo **Cancelar**.

    Al seleccionar cancelar, se cancelará la solicitud. Para volver a activar el rol, tendrá que enviar una nueva solicitud de activación.

   ![Mi lista de solicitudes con la acción Cancelar resaltada](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Solución de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permisos no concedidos después de activar un rol

Al activar un rol en Privileged Identity Management, la activación podría no propagarse al instante a todos los portales que requieren el rol con privilegios. A veces, incluso si el cambio se propaga, el almacenamiento en caché web en un portal puede provocar que el cambio no surta efecto de inmediato. Esto es lo que debe de hacer si se retrasa la activación.

1. Cierre sesión en Azure Portal y vuelva a iniciar sesión.
1. En Privileged Identity Management, compruebe que aparece como miembro del rol.

## <a name="next-steps"></a>Pasos siguientes

- [Ampliación o renovación de roles de recursos de Azure en Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Activar mis roles de Azure AD en Privileged Identity Management](pim-how-to-activate-role.md)
