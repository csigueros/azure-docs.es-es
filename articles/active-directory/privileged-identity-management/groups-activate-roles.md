---
title: 'Activar roles de grupo de acceso privilegiado en PIM: Azure AD | Microsoft Docs'
description: Aprenda a activar sus roles de grupo de acceso con privilegios en Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 3a2088357859ec3f77e7a482ba1507f86e9fdf92
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668246"
---
# <a name="activate-my-privileged-access-group-roles-in-privileged-identity-management"></a>Activar mis roles de grupo de acceso con privilegios en Privileged Identity Management

Use Privileged Identity Management (PIM) para permitir que los miembros de rol aptos para grupos de acceso con privilegios programen la activación de rol para una fecha y hora concretas. Asimismo, también pueden seleccionar una duración de activación de hasta el valor máximo configurado por los administradores.

Este artículo va dirigido a los miembros aptos que desean activar su rol de grupo de acceso con privilegios en Privileged Identity Management.

## <a name="activate-a-role"></a>Activación de un rol

Cuando necesite asumir un rol de grupo de acceso con privilegios, puede solicitar la activación mediante la opción de navegación **Mis roles** en Privileged Identity Management.

1. [Inicie sesión en el portal de Azure AD](https://aad.portal.azure.com) con permisos de administrador global o de propietario de grupo.

1. Abra [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Seleccione **Grupos de acceso con privilegios (versión preliminar)** y, a continuación, seleccione **Activar rol** para abrir la página **Mis roles** para grupos de acceso con privilegios.

    ![Página Roles de acceso con privilegios en PIM](./media/groups-activate-roles/groups-select-group.png)

1. En la página **Mis roles,** seleccione **Activar** en la fila de la asignación apta que desea activar.

    ![Activar vínculo en la fila de asignación de roles apta](./media/groups-activate-roles/groups-activate-link.png)

1. Si el rol requiere la autenticación multifactor, seleccione **Compruebe su identidad antes de proceder**. Solo tiene que autenticarse una vez por sesión.

    ![Comprobación de mi identidad con MFA antes de la activación del rol](./media/groups-activate-roles/groups-my-roles-mfa.png)

1. Seleccione **Comprobar mi identidad** y siga las instrucciones para proporcionar comprobación de seguridad adicional.

    ![Pantalla para proporcionar una comprobación de seguridad, como un código PIN](./media/groups-activate-roles/groups-mfa-enter-code.png)

1. Si es necesario, especifique una hora de inicio de activación personalizada. El miembro o propietario se activará solo después de la hora seleccionada.

1. En el campo **Razón**, escriba el motivo de la solicitud de activación.

    ![Activar página con duración y justificación](./media/groups-activate-roles/groups-activate-page.png)

1. Seleccione **Activar**.

Si el [rol requiere aprobación](pim-resource-roles-approval-workflow.md) para activarse, aparece una notificación de Azure en la esquina superior del explorador que le informa de que la solicitud está pendiente de aprobación.

## <a name="view-the-status-of-your-requests"></a>Visualización del estado de las solicitudes

Puede ver el estado de las solicitudes pendientes de activación.

1. Abra Azure AD Privileged Identity Management.

1. Seleccione **Mis solicitudes** para ver una lista de los roles de Azure AD y las solicitudes de rol de grupo de acceso con privilegios.

1. Desplácese a la derecha, si es necesario, para ver la columna **Estado de solicitud**.

## <a name="cancel-a-pending-request"></a>Cancelación de una solicitud pendiente

Si no necesita activar un rol que requiera aprobación, puede cancelar una solicitud pendiente en cualquier momento.

1. Abra Azure AD Privileged Identity Management.

1. Seleccione **Mis solicitudes**.

1. En el rol que desea cancelar, seleccione el vínculo **Cancelar**.

    Al seleccionar **Cancelar**, se cancelará la solicitud. Para volver a activar el rol, tendrá que enviar una nueva solicitud de activación.

## <a name="troubleshoot"></a>Solución de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permisos no concedidos después de activar un rol

Al activar un rol en Privileged Identity Management, la activación podría no propagarse al instante a todos los portales que requieren el rol con privilegios. A veces, incluso si el cambio se propaga, el almacenamiento en caché web en un portal puede provocar que el cambio no surta efecto de inmediato. Esto es lo que debe de hacer si se retrasa la activación.

1. Cierre sesión en Azure Portal y vuelva a iniciar sesión.
1. En Privileged Identity Management, compruebe que aparece como miembro del rol.

## <a name="next-steps"></a>Pasos siguientes

- [Extender o renovar roles de grupo de acceso con privilegios en Privileged Identity Management](groups-renew-extend.md)
- [Asignar mis roles de grupo de acceso con privilegios en Privileged Identity Management](groups-assign-member-owner.md)
