---
title: 'Aprobación o rechazo de solicitudes de roles de Azure AD en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a aprobar o rechazar solicitudes para los roles de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ffb9e53172835ddcec574802f10a4a4bbbea0e1
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233068"
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

## <a name="approve-requests"></a>Aprobar solicitudes

1. Busque y seleccione la solicitud que desea aprobar. Aparece una página aprobar o denegar.

    ![Captura de pantalla que muestra la página "Aprobar solicitudes: roles de Azure AD".](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. En el cuadro **Justificación**, escriba la justificación empresarial.

1. Seleccione **Aprobar**. Recibirá una notificación de Azure de su aprobación.

    ![Notificación de aprobación que muestra que se ha aprobado la solicitud](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

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
