---
title: 'Activación de mis roles de Azure AD en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a activar los roles de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3926fb277251f7020539942c76d9c97f0ce46d75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736171"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Activación de mis roles de Azure AD en PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) simplifica la forma en que las empresas administran el acceso con privilegios a los recursos de Azure AD y otros servicios en línea de Microsoft, como Microsoft 365 o Microsoft Intune.  

Si se le ha considerado *elegible* para un rol administrativo, debe *activar* la asignación de roles cuando necesite realizar acciones con privilegios. Por ejemplo, si administra de vez en cuando características de Microsoft 365, es posible que los administradores de roles con privilegios de su organización no le hayan asignado el rol de administrador global permanente, ya que ese rol afecta también a otros servicios. En su lugar, podrían haberle hecho elegible para roles de Azure AD, como administrador de Exchange Online. Puede solicitar la activación de ese rol cuando necesite sus privilegios y tendrá control de administrador durante un período predeterminado.

Este artículo está dirigido a los administradores que necesitan activar su rol de Azure AD en Privileged Identity Management.

## <a name="activate-a-role"></a>Activación de un rol

Cuando necesite asumir un rol de Azure AD, puede solicitar la activación al abrir **Mis roles** en Privileged Identity Management.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Abra **Azure AD Privileged Identity Management**. Para información sobre cómo agregar el icono de Privileged Identity Management en el panel, consulte [Primer uso de PIM](pim-getting-started.md).

1. Seleccione **Mis roles** y, a continuación, seleccione **Roles de Azure AD** para ver una lista de los roles de Azure AD válidos.

    ![Mi página de roles en la que se muestran los roles que el usuario puede activar](./media/pim-how-to-activate-role/my-roles.png)

1. En la lista **Roles de Azure AD**, busque el rol que desea activar.

    ![Roles de Azure AD: mi lista de roles elegibles](./media/pim-how-to-activate-role/activate-link.png)

1. Seleccione **Activar** para abrir el Panel de activación.

    ![Roles de Azure AD: la página de activación contiene la duración y el ámbito](./media/pim-how-to-activate-role/activate-page.png)

1. Seleccione **Additional verification required**"** (Verificación adicional requerida) y siga las instrucciones para la verificación de seguridad adicional. Se le requiere que se autentique solo una vez por sesión.

    ![Pantalla para proporcionar una comprobación de seguridad, como un código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Después de la autenticación multifactor, seleccione **Activate before proceeding** (Activar antes de continuar).

    ![Comprobación de mi identidad mediante la autenticación multifactor antes de la activación del rol](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. Si desea especificar un ámbito reducido, seleccione **Ámbito** para abrir el panel de filtro. En el panel de filtro, puede especificar los recursos de Azure AD a los que necesita acceso. Se recomienda solicitar acceso al menor número de recursos que necesite.

1. Si es necesario, especifique una hora de inicio de activación personalizada. El rol de Azure AD se activaría después de la hora seleccionada.

1. En el campo **Razón**, escriba el motivo de la solicitud de activación.

1. Seleccione **Activar**.

    Si el [rol requiere aprobación](pim-resource-roles-approval-workflow.md) para activarse, aparecerá una notificación en la esquina superior del explorador que le informa de que la solicitud está pendiente de aprobación.

    ![La solicitud de activación está pendiente de notificación de aprobación](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>Visualización del estado de las solicitudes de nueva versión

Puede ver el estado de las solicitudes pendientes de activación.

1. Abra Azure AD Privileged Identity Management.

1. Seleccione **Mis solicitudes** para ver una lista de los roles de Azure AD y las solicitudes de rol de recursos de Azure.

    ![Mis solicitudes: página de Azure AD que muestra las solicitudes pendientes](./media/pim-how-to-activate-role/my-requests-page.png)

1. Desplácese a la derecha para ver la columna **Estado de solicitud**.

## <a name="cancel-a-pending-request-for-new-version"></a>Cancelación de una solicitud pendiente de nueva versión

Si no necesita activar un rol que requiera aprobación, puede cancelar una solicitud pendiente en cualquier momento.

1. Abra Azure AD Privileged Identity Management.

1. Seleccione **Mis solicitudes**.

1. En el rol que desea cancelar, seleccione el vínculo **Cancelar**.

    Al seleccionar cancelar, se cancelará la solicitud. Para volver a activar el rol, tendrá que enviar una nueva solicitud de activación.

   ![Mi lista de solicitudes con la acción Cancelar resaltada](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>Solución de problemas de nueva versión

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permisos no concedidos después de activar un rol

Al activar un rol en Privileged Identity Management, la activación podría no propagarse al instante a todos los portales que requieren el rol con privilegios. A veces, incluso si el cambio se propaga, el almacenamiento en caché web en un portal puede provocar que el cambio no surta efecto de inmediato. Si la activación se retrasa, cierre la sesión del portal en el que está intentando realizar la acción y vuelva a iniciarla. En Azure Portal, PIM cierra sesión y vuelva a iniciarla automáticamente.

## <a name="next-steps"></a>Pasos siguientes

- [Visualización del historial de auditoría de los roles de Azure AD](pim-how-to-use-audit-log.md)
