---
title: 'Renovación de asignaciones de roles de Azure AD en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a ampliar o renovar asignaciones de roles de Azure Active Directory en Privileged Identity Management (PIM) de Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 08/06/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2988d846001876185d377672db1910e783aa3f8
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398145"
---
# <a name="extend-or-renew-azure-ad-role-assignments-in-privileged-identity-management"></a>Ampliación o renovación de asignaciones de roles de Azure AD en Privileged Identity Management

Privileged Identity Management (PIM) de Azure Active Directory (Azure AD) proporciona controles para administrar el ciclo de vida de acceso y asignación de los roles de Azure AD. Los administradores pueden asignar roles mediante las propiedades de fecha y hora de inicio y finalización. Cuando el fin de la asignación se aproxima, Privileged Identity Management envía notificaciones por correo electrónico a los usuarios o grupos afectados. También envía notificaciones por correo electrónico a los administradores de Azure AD para asegurarse de que se mantenga el acceso adecuado. Las asignaciones pueden renovarse y permanecer visibles en un estado expirado durante un máximo de 30 días, incluso si el acceso no se extiende.

## <a name="who-can-extend-and-renew"></a>¿Quién puede ampliar y renovar?

Solo los administradores globales o los administradores de roles con privilegios pueden ampliar o renovar asignaciones de roles de Azure AD. El usuario o grupo afectado puede solicitar que se amplíen los roles que están a punto de expirar, así como que se renueven los que ya han expirado.

## <a name="when-are-notifications-sent"></a>¿Cuándo se envían las notificaciones?

Privileged Identity Management envía notificaciones por correo electrónico tanto a los administradores como a los usuarios o grupos afectados de los roles que expiran en 14 días y un día antes de la expiración. También, envía otro correo electrónico cuando una asignación expira oficialmente.

Los administradores reciben notificaciones cuando un usuario o grupo asignado a un rol que va a expirar, o que ha expirado, solicita la ampliación o renovación. Cuando un administrador resuelve una solicitud como aprobada o denegada, la decisión se comunica a todos los demás administradores. Luego, se informa de la decisión al usuario o grupo que ha realizado la solicitud.

## <a name="extend-role-assignments"></a>Extensión de asignaciones de roles

En los pasos siguientes se describe el proceso de la solicitud, resolución o administración de una extensión o renovación de una asignación de roles.

### <a name="self-extend-expiring-assignments"></a>Ampliación automática de asignaciones que van a expirar

Los usuarios o grupos asignados a un rol pueden ampliar las asignaciones de roles que van a expirar directamente desde la pestaña **Apto** o **Activo** de la página **Mis roles**, en **Roles de Azure AD** o desde la página **Mis roles** de nivel superior del portal de Privileged Identity Management. Los usuarios o grupos pueden solicitar la ampliación de las asignaciones de roles aptas y activas que expiren en los próximos 14 días.

![Roles de Azure AD: página Mis roles que muestra los roles aptos con una columna de acción](./media/pim-how-to-renew-extend/pim-extend-link-in-portal.png)

Cuando la fecha y hora de finalización de la asignación es anterior a 14 días, el botón **Extender** se convierte en un vínculo activo en la interfaz de usuario. En el ejemplo siguiente, se supone que la fecha actual es el 27 de marzo.

![Columna de acción con vínculos para activar o extender](./media/pim-how-to-renew-extend/pim-extend-within-fourteen.png)

Para solicitar una extensión de la asignación de este rol, seleccione **Extender** para abrir el formulario de solicitud.

![Panel Extender la asignación de roles con un cuadro de motivo](./media/pim-how-to-renew-extend/extend-role-assignment-request.png)

Especifique un motivo para la solicitud de extensión y, luego, seleccione **Extender**.

>[!NOTE]
>Se recomienda incluir los detalles de por qué es necesaria la extensión y de cuánto tiempo debe ser (si se tiene acceso a esta información).

Los administradores reciben una notificación por correo electrónico para revisar la solicitud de ampliación. Si ya se ha enviado una solicitud para la ampliación, aparecerá una notificación de Azure en el portal.

![Notificación que explica que ya hay una extensión de asignación de roles pendiente](./media/pim-how-to-renew-extend/extend-notification.png)

Vaya a la página **Solicitudes pendientes** para ver el estado de la solicitud o para cancelarla.

![Roles de Azure AD: página Solicitudes pendientes en la que se enumeran las solicitudes pendientes solicitadas y un vínculo a Cancelar](./media/pim-how-to-renew-extend/pending-requests.png)

### <a name="admin-approved-extension"></a>Ampliación aprobada por el administrador

Cuando un usuario o grupo envía una solicitud para ampliar una asignación de roles, los administradores reciben una notificación por correo electrónico con los detalles de la asignación original y el motivo de la solicitud. La notificación incluye un vínculo directo a la solicitud para que el administrador la apruebe o deniegue.

Además de usar el vínculo del correo electrónico, los administradores pueden aprobar o rechazar las solicitudes en el portal de administración de Privileged Identity Management; para ello, deben seleccionar **Aprobar solicitudes** en el panel izquierdo.

![Roles de Azure AD: página de aprobación de solicitudes que muestra solicitudes y vínculos para aprobar o denegar](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

Cuando un administrador selecciona **Aprobar** o **Denegar**, se muestran los detalles de la solicitud, junto con un campo para especificar una justificación comercial para los registros de auditoría.

![Solicitud de aprobación de la asignación de roles con el motivo del solicitante, el tipo de asignación, la hora de inicio, la hora de finalización y el motivo](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

Al aprobar una solicitud para ampliar la asignación de roles, los administradores pueden elegir una nueva fecha de inicio y finalización y un tipo de asignación. Cambiar el tipo de asignación puede ser necesario si el administrador quiere proporcionar acceso limitado para completar una tarea específica (por ejemplo, un día). En este ejemplo, el administrador puede cambiar la asignación de **Elegible** a **Activa**. Esto quiere decir que puede brindar acceso al solicitante sin requerirle que la activación.

### <a name="admin-initiated-extension"></a>Ampliación iniciada por el administrador

Si un usuario asignado a un rol no solicita una ampliación para la asignación de roles, un administrador puede ampliar una asignación en nombre del usuario. Las ampliaciones administrativas de la asignación de roles no requieren aprobación, pero se envían notificaciones a todos los administradores restantes después de haber ampliado el rol.

Para ampliar una asignación de roles, vaya a la vista de asignaciones o roles en Privileged Identity Management. Busque la asignación que requiere una ampliación. Luego, seleccione **Extender** en la columna de acción.

![Roles de Azure AD: página Asignaciones que enumera los roles aptos con vínculos para ampliar](./media/pim-how-to-renew-extend/extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovación de asignaciones de roles

Aunque conceptualmente el proceso para solicitar una extensión es similar, el proceso para renovar una asignación de rol que ha expirado es diferente. Mediante los siguientes pasos, tanto las asignaciones como los administradores pueden renovar el acceso a los roles que hayan expirado cuando sea necesario.

### <a name="self-renew"></a>Renovación automática

Los usuarios que ya no tengan acceso a los recursos pueden tener acceso durante un período de hasta 30 días al historial de asignaciones expiradas. Para ello, vaya a **Mis roles** en el panel izquierdo, y luego, seleccione la pestaña **Roles expirados** en la sección Roles de Azure AD.

![Página Mis roles: pestaña Roles expirados](./media/pim-how-to-renew-extend/renew-from-myroles.png)

El valor predeterminado de la lista de roles que se muestra es **Roles elegibles**. Seleccione los roles asignados **aptos** o **activos**.

Para solicitar la renovación de cualquiera de las asignaciones de roles de la lista, seleccione la acción **Renovar**. A continuación, indique un motivo para la solicitud. Resulta útil indicar una duración, además de cualquier otro contexto adicional o justificación comercial que ayude al administrador de recursos a decidir si la aprueba o la deniega.

![Panel Renovar asignación de roles con el cuadro de motivo](./media/pim-how-to-renew-extend/renew-request-form.png)

Después de haber enviado la solicitud, se notifica a los administradores que hay una solicitud pendiente para renovar una asignación de roles.

### <a name="admin-approves"></a>Aprobaciones de los administradores

Los administradores de Azure AD pueden acceder a la solicitud de renovación desde el vínculo de la notificación por correo electrónico o bien pueden acceder a Privileged Identity Management en Azure Portal y seleccionar **Aprobar solicitudes** en PIM.

![Roles de Azure AD: página de aprobación de solicitudes que muestra solicitudes y vínculos para aprobar o denegar](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

Cuando un administrador selecciona **Aprobar** o **Denegar**, se muestran los detalles de la solicitud, junto con un campo para especificar una justificación comercial para los registros de auditoría.

![Solicitud de aprobación de la asignación de roles con el motivo del solicitante, el tipo de asignación, la hora de inicio, la hora de finalización y el motivo](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

Al aprobar una solicitud para renovar la asignación de roles, los administradores deben especificar una nueva fecha de inicio y finalización y un tipo de asignación.

### <a name="admin-renew"></a>Renovación por parte de los administradores

También pueden renovar las asignaciones de roles expirados en la pestaña de roles **Expirados** de un rol de Azure AD. Para ver una lista de todas las asignaciones de roles que han expirado, en la pantalla **Asignaciones**, seleccione **Roles expirados**.

![Roles de Azure AD: página Asignaciones que enumera los roles expirados con vínculos para renovar](./media/pim-how-to-renew-extend/renew-from-assignments-pane.png)

## <a name="next-steps"></a>Pasos siguientes

- [Aprobación o rechazo de solicitudes para los roles de Azure AD en Privileged Identity Management](azure-ad-pim-approval-workflow.md)
- [Configuración del rol de Azure AD en Privileged Identity Management](pim-how-to-change-default-settings.md)
