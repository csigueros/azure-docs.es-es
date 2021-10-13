---
title: 'Renovación de asignaciones de roles de recursos de Azure en PIM: Azure AD | Microsoft Docs'
description: Aprenda a ampliar o renovar asignaciones de roles de recursos de Azure en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d252c5fa264650999af7a14e4bf1cfaa976f6b
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669309"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Ampliación o renovación de asignaciones de roles de recursos de Azure en Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) proporciona controles para administrar el ciclo de vida de acceso y asignación de los recursos de Azure. Los administradores pueden asignar roles mediante las propiedades de fecha y hora de inicio y finalización. Cuando el fin de la asignación se aproxima, Privileged Identity Management envía notificaciones por correo electrónico a los usuarios o grupos afectados. También envía notificaciones por correo electrónico a los administradores del recurso para asegurarse de que se mantenga el acceso adecuado. Las asignaciones pueden renovarse y permanecer visibles en un estado expirado durante un máximo de 30 días, incluso si el acceso no se extiende.

## <a name="who-can-extend-and-renew"></a>¿Quién puede ampliar y renovar?

Las asignaciones de roles solo pueden ampliarlas o renovarlas los administradores del recurso. El usuario o grupo afectado puede solicitar que se amplíen los roles que están a punto de expirar, así como que se renueven los que ya han expirado.

## <a name="when-are-notifications-sent"></a>¿Cuándo se envían las notificaciones?

Privileged Identity Management envía notificaciones por correo electrónico tanto a los administradores como a los usuarios o grupos afectados de los roles que expiran en 14 días y un día antes de la expiración. Envía un correo electrónico adicional cuando una asignación ha expirado oficialmente.

Los administradores reciben notificaciones cuando un usuario o grupo asignado a un rol que va a expirar, o que ha expirado, solicita la ampliación o renovación. Cuando un administrador específico resuelve la solicitud, se envía a los otros administradores una notificación de la decisión de resolución (aprobada o denegada). Luego, se informa de la decisión al usuario o grupo que ha realizado la solicitud.

## <a name="extend-role-assignments"></a>Extensión de asignaciones de roles

En los pasos siguientes se describe el proceso de la solicitud, resolución o administración de una extensión o renovación de una asignación de roles.

### <a name="self-extend-expiring-assignments"></a>Ampliación automática de asignaciones que van a expirar

Los usuarios o grupos asignados a un rol pueden ampliar las asignaciones de roles que van a expirar directamente desde la pestaña **Válidas** o **Activas** de la página **Mis roles** de un recurso y desde la página de nivel superior **Mis roles** del portal de Privileged Identity Management. Los usuarios o grupos pueden solicitar la ampliación de roles válidos y activos (asignados) que expiren en los próximos 14 días.

![Recursos de Azure: página Mis roles que muestra los roles elegibles con una columna de acción](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Cuando la fecha y hora de finalización de la asignación es anterior a 14 días, el botón para **Extender** se convierte en un vínculo activo en la interfaz de usuario. En el ejemplo siguiente, se supone que la fecha actual es el 27 de marzo.

![Columna de acción con vínculos para activar o extender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Para solicitar una extensión de la asignación de este rol, seleccione **Extender** para abrir el formulario de solicitud.

![Panel Extender la asignación de roles con un cuadro de motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Para ver información acerca de la asignación original, expanda **Detalles de asignación**. Especifique un motivo para la solicitud de extensión y, luego, seleccione **Extender**.

>[!NOTE]
>Se recomienda incluir los detalles de por qué es necesaria la extensión y de cuánto tiempo debe ser (si se tiene acceso a esta información).

![Panel Extender la asignación de roles con los detalles de asignación expandidos](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

En cuestión de minutos, los administradores de recursos reciben una notificación por correo electrónico en la que se les solicita que revisen la solicitud de ampliación. Si ya se ha enviado una solicitud para la ampliación, aparecerá una notificación de Azure en el portal.

![Notificación que explica que ya hay una extensión de asignación de roles pendiente](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vaya a la página **Solicitudes pendientes** para ver el estado de la solicitud o para cancelarla.

![Recursos de Azure: página de solicitudes pendiente que muestra todas las solicitudes pendientes y un vínculo para cancelarlas](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Ampliación aprobada por el administrador

Cuando un usuario o grupo envía una solicitud para ampliar la asignación de un rol, los administradores de recursos reciben una notificación por correo electrónico con los detalles de la asignación original y el motivo de la solicitud. La notificación incluye un vínculo directo a la solicitud para que el administrador la apruebe o deniegue.

Además de usar el vínculo del correo electrónico, los administradores pueden aprobar o rechazar las solicitudes en el portal de administración de Privileged Identity Management; para ello, deben seleccionar **Aprobar solicitudes** en el panel izquierdo.

![Recursos de Azure: página de aprobación de solicitudes que muestra solicitudes y vínculos para aprobar o denegar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Cuando un administrador selecciona **Aprobar** o **Denegar**, se muestran los detalles de la solicitud, junto con un campo para especificar una justificación comercial para los registros de auditoría.

![Solicitud de aprobación de la asignación de roles con el motivo del solicitante, el tipo de asignación, la hora de inicio, la hora de finalización y el motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Al aprobar una solicitud para extender la asignación de roles, los administradores de recursos pueden elegir una nueva fecha de inicio y finalización y un tipo de asignación. Cambiar el tipo de asignación puede ser necesario si el administrador quiere proporcionar acceso limitado para completar una tarea específica (por ejemplo, un día). En este ejemplo, el administrador puede cambiar la asignación de **Elegible** a **Activa**. Esto quiere decir que puede brindar acceso al solicitante sin requerirle que la activación.

### <a name="admin-initiated-extension"></a>Ampliación iniciada por el administrador

Si un usuario asignado a un rol no solicita una ampliación para la asignación de roles, un administrador puede ampliar una asignación en nombre del usuario. Las ampliaciones administrativas de la asignación de roles no requieren aprobación, pero se envían notificaciones a todos los administradores restantes después de haber ampliado el rol.

Para ampliar una asignación de roles, vaya a la vista del rol de recurso o la asignación en Privileged Identity Management. Busque la asignación que requiere una ampliación. Luego, seleccione **Extender** en la columna de acción.

![Recursos de Azure: página de asignaciones que muestra los roles válidos con vínculos para ampliar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovación de asignaciones de roles

Aunque conceptualmente el proceso para solicitar una extensión es similar, el proceso para renovar una asignación de rol que ha expirado es diferente. Mediante los siguientes pasos, tanto las asignaciones como los administradores pueden renovar el acceso a los roles que hayan expirado cuando sea necesario.

### <a name="self-renew"></a>Renovación automática

Los usuarios que ya no tengan acceso a los recursos pueden tener acceso durante un período de hasta 30 días al historial de asignaciones expiradas. Para ello, vaya a **Mis roles** en el panel izquierdo y luego seleccione la pestaña **Roles expirados** en la sección de roles de recursos de Azure.

![Página Mis roles: pestaña Roles expirados](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

El valor predeterminado de la lista de roles que se muestra es **Roles elegibles**. Utilice el menú desplegable para alternar entre los roles asignados Elegibles y Activos.

Para solicitar la renovación de cualquiera de las asignaciones de roles de la lista, seleccione la acción **Renovar**. A continuación, indique un motivo para la solicitud. Resulta útil indicar una duración, además de cualquier contexto adicional o una justificación comercial que ayude al administrador de recursos a la hora de decidir si la aprueba o la deniega.

![Panel Renovar asignación de roles con el cuadro de motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Después de haber enviado la solicitud, se notifica a los administradores de recursos de que hay una solicitud pendiente para renovar la asignación de un rol.

### <a name="admin-approves"></a>Aprobaciones de los administradores

Los administradores de recursos pueden acceder a la solicitud de renovación desde el vínculo de la notificación por correo electrónico o bien pueden acceder a Privileged Identity Management en Azure Portal y seleccionar **Aprobar solicitudes** en el panel izquierdo.

![Recursos de Azure: página de aprobación de solicitudes que muestra solicitudes y vínculos para aprobar o denegar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Cuando un administrador selecciona **Aprobar** o **Denegar**, se muestran los detalles de la solicitud, junto con un campo para especificar una justificación comercial para los registros de auditoría.

![Solicitud de aprobación de la asignación de roles con el motivo del solicitante, el tipo de asignación, la hora de inicio, la hora de finalización y el motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Al aprobar una solicitud para renovar la asignación de roles, los administradores de recursos deben especificar una nueva fecha de inicio y finalización y un tipo de asignación.

### <a name="admin-renew"></a>Renovación por parte de los administradores

Los administradores de recursos pueden renovar las asignaciones de roles expirados en la pestaña **Miembros** del menú de navegación izquierdo de un recurso. También pueden renovar las asignaciones de roles expirados en la pestaña de roles **Expirados** de un rol de recursos.

En la pantalla **Miembros**, seleccione **Roles expirados** para ver una lista de todas las asignaciones de roles que han expirado.

![Recursos de Azure: página de miembros que muestra los roles expirados con vínculos para renovar](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Pasos siguientes

- [Aprobación o rechazo de solicitudes para los roles de Azure en Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Configuración de las opciones de rol de recursos de Azure en Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
