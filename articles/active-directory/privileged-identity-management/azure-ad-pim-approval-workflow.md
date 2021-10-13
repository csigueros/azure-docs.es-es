---
title: 'Aprobación o rechazo de solicitudes de roles de Azure AD en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a aprobar o rechazar solicitudes para los roles de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80965c0a1ff0ba14586ac99f08833f3a02a3a608
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667238"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aprobación o rechazo de solicitudes para los roles de Azure AD en Privileged Identity Management

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), puede configurar roles para requerir la aprobación para la activación y elegir uno o varios usuarios o grupos como aprobadores delegados. Los aprobadores delegados tienen 24 horas para aprobar las solicitudes. Si no se aprueba una solicitud en un plazo de 24 horas, el usuario apto debe volver a enviar una nueva solicitud. El período de tiempo de aprobación de 24 horas no es configurable.

## <a name="view-pending-requests"></a>Ver solicitudes en espera

Como aprobador delegado, recibirá una notificación por correo electrónico cuando una solicitud de rol de Azure AD está pendiente de su aprobación. Puede ver estas solicitudes pendientes en Privileged Identity Management.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Aprobar solicitudes**.

    ![Aprobar solicitudes: página de recursos que muestra la solicitud de revisión de roles de Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    En la sección **Solicitudes de activación de roles**, verá una lista de solicitudes pendientes de su aprobación.

## <a name="view-pending-requests-using-graph-api"></a>Visualización de solicitudes pendientes mediante Graph API

### <a name="http-request"></a>Solicitud HTTP

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests/filterByCurrentUser(on='approver')?$filter=status eq 'PendingApproval' 
````

### <a name="http-response"></a>Respuesta HTTP

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleAssignmentScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleAssignmentScheduleRequest", 
            "id": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "status": "PendingApproval", 
            "createdDateTime": "2021-07-15T19:57:17.76Z", 
            "completedDateTime": "2021-07-15T19:57:17.537Z", 
            "approvalId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "customData": null, 
            "action": "SelfActivate", 
            "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
            "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b", 
            "directoryScopeId": "/", 
            "appScopeId": null, 
            "isValidationOnly": false, 
            "targetScheduleId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "justification": "test", 
            "createdBy": { 
                "application": null, 
                "device": null, 
                "user": { 
                    "displayName": null, 
                    "id": "d96ea738-3b95-4ae7-9e19-78a083066d5b" 
                } 
            }, 
            "scheduleInfo": { 
                "startDateTime": null, 
                "recurrence": null, 
                "expiration": { 
                    "type": "afterDuration", 
                    "endDateTime": null, 
                    "duration": "PT5H30M" 
                } 
            }, 
            "ticketInfo": { 
                "ticketNumber": null, 
                "ticketSystem": null 
            } 
        } 
    ] 
} 
````

## <a name="approve-requests"></a>Aprobar solicitudes

1. Busque y seleccione la solicitud que desea aprobar. Aparece una página aprobar o denegar.

    ![Captura de pantalla que muestra la página "Aprobar solicitudes: roles de Azure AD".](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. En el cuadro **Justificación**, escriba la justificación empresarial.

1. Seleccione **Aprobar**. Recibirá una notificación de Azure de su aprobación.

    ![Notificación de aprobación que muestra que se ha aprobado la solicitud](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

## <a name="approve-pending-requests-using-graph-api"></a>Aprobación de solicitudes pendientes mediante Graph API

### <a name="get-ids-for-the-steps-that-require-approval"></a>Obtención de los id. para los pasos que requieren aprobación

En el caso de una solicitud de activación específica, este comando obtiene todos los pasos de aprobación que es necesario aprobar. Actualmente no se admiten aprobaciones de varios pasos.

#### <a name="http-request"></a>Solicitud HTTP

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID> 
````

#### <a name="http-response"></a>Respuesta HTTP

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals/$entity", 
    "id": "<request-ID-GUID>",
    "steps@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals('<request-ID-GUID>')/steps", 
    "steps": [ 
        { 
            "id": "<approval-step-ID-GUID>", 
            "displayName": null, 
            "reviewedDateTime": null, 
            "reviewResult": "NotReviewed", 
            "status": "InProgress", 
            "assignedToMe": true, 
            "justification": "", 
            "reviewedBy": null 
        } 
    ] 
} 
````

### <a name="approve-the-activation-request-step"></a>Aprobación del paso de solicitud de activación

#### <a name="http-request"></a>Solicitud HTTP

````HTTP
PATCH 
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID>/steps/<approval-step-ID-GUID> 
{ 
    "reviewResult": "Approve", 
    "justification": "abcdefg" 
} 
 ````

#### <a name="http-response"></a>Respuesta HTTP

Las llamadas PATCH correctas generan una respuesta vacía.

## <a name="deny-requests"></a>Denegar solicitudes

1. Busque y seleccione la solicitud que desea denegar. Aparece una página aprobar o denegar.

    ![Aprobar solicitudes: panel de aprobación o denegación con detalles y cuadro Justificación](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. En el cuadro **Justificación**, escriba la justificación empresarial.

1. Seleccione **Denegar**. Aparecerá una notificación con su denegación.

## <a name="workflow-notifications"></a>Notificaciones de flujo de trabajo

A continuación proporcionamos información sobre las notificaciones de flujo de trabajo:

- Los aprobadores reciben una notificación por correo cuando una solicitud de un rol está pendiente de su revisión. Las notificaciones por correo electrónico incluyen un vínculo directo a la solicitud donde el aprobador puede aprobarla o rechazarla.
- Las solicitudes las resuelve el primer aprobador que aprueba o rechaza.
- Cuando un aprobador responde a la solicitud, se notifica a todos los aprobadores de la acción.
- Los administradores globales y los administradores de roles con privilegios reciben una notificación cuando un usuario aprobado se activa en su rol.

>[!NOTE]
>Un administrador global o un administrador de roles con privilegios que cree que un usuario aprobado no debe estar activo puede quitar la asignación de roles activa en Privileged Identity Management. Aunque los administradores no reciben notificaciones de solicitudes pendientes a menos que sean aprobadores, pueden ver y cancelar todas las solicitudes pendientes de todos los usuarios, para lo cual deben dirigirse a las solicitudes pendientes en Privileged Identity Management.

## <a name="next-steps"></a>Pasos siguientes

- [Notificaciones por correo electrónico en Privileged Identity Management](pim-email-notifications.md)
- [Aprobación o rechazo de solicitudes para los roles de Azure en Privileged Identity Management](pim-resource-roles-approval-workflow.md)
