---
title: Aprobar solicitudes de roles de recursos de Azure en PIM - Azure AD | Microsoft Docs
description: Aprenda a aprobar o rechazar solicitudes para los roles de recursos de Azure en Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 42940c078424f86ed8e3bba43232edd180c6a2c8
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669385"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Aprobación o rechazo de solicitudes de roles de recursos de Azure en Privileged Identity Management

Con Privileged Identity Management (PIM) en Azure Active Directory (Azure AD), puede configurar roles para requerir la aprobación de la activación y elegir usuarios o grupos de la organización de Azure AD como aprobadores delegados. Se recomienda seleccionar dos o más aprobadores para cada rol a fin de reducir la carga de trabajo para el administrador de roles con privilegios. Los aprobadores delegados tienen 24 horas para aprobar las solicitudes. Si no se aprueba una solicitud en un plazo de 24 horas, el usuario apto debe volver a enviar una nueva solicitud. El período de tiempo de aprobación de 24 horas no es configurable.

Siga los pasos que se describen en este artículo para aprobar o denegar solicitudes para los roles de recursos de Azure.

## <a name="view-pending-requests"></a>Ver solicitudes en espera

Como aprobador delegado, recibirá una notificación por correo electrónico cuando una solicitud de rol de recursos de Azure está pendiente de su aprobación. Puede ver estas solicitudes pendientes en Privileged Identity Management.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Aprobar solicitudes**.

    ![Aprobar solicitudes: página de recursos de Azure que muestra la solicitud de revisión](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    En la sección **Solicitudes de activación de roles**, verá una lista de solicitudes pendientes de su aprobación.

## <a name="approve-requests"></a>Aprobar solicitudes

1. Busque y seleccione la solicitud que desea aprobar. Aparece una página aprobar o denegar.

    ![Aprobar solicitudes: panel de aprobación o denegación con detalles y cuadro Justificación](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. En el cuadro **Justificación**, escriba la justificación empresarial.

1. Seleccione **Aprobar**. Recibirá una notificación de Azure de su aprobación.

    ![Notificación de aprobación que muestra que se ha aprobado la solicitud](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

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
- Los administradores de recursos reciben una notificación cuando un usuario aprobado se activa en su rol.

>[!Note]
>Un administrador de recursos que cree que un usuario aprobado no debe estar activo puede quitar la asignación de roles activa en Privileged Identity Management. Aunque los administradores de recursos no reciben notificaciones de solicitudes pendientes a menos que sean aprobadores, pueden ver y cancelar solicitudes pendientes para todos los usuarios viendo solicitudes pendientes en Privileged Identity Management.

## <a name="next-steps"></a>Pasos siguientes

- [Ampliación o renovación de roles de recursos de Azure en Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Notificaciones por correo electrónico en Privileged Identity Management](pim-email-notifications.md)
- [Aprobación o rechazo de solicitudes para los roles de Azure AD en Privileged Identity Management](azure-ad-pim-approval-workflow.md)
