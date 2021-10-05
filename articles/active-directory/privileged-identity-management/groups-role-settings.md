---
title: Configuración de los valores de los grupos de acceso con privilegios en PIM - Azure Active Directory | Microsoft Docs
description: Obtenga información sobre cómo configurar las opciones de los grupos a los que se pueden asignar roles en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 09/14/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c47530e3fd7626674297a2d910ff9c39722c228b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650845"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Configuración del grupo de acceso con privilegios (versión preliminar) en Privileged Identity Management

La configuración de roles es la configuración predeterminada que se aplica a las asignaciones de acceso privilegiado del propietario y del miembro del grupo en Privileged Identity Management (PIM). Siga estos pasos para configurar el flujo de trabajo de aprobación con el fin de especificar quién puede aprobar o denegar las solicitudes de elevación de privilegios.

## <a name="open-role-settings"></a>Apertura de la configuración de roles

Siga estos pasos para abrir la configuración de un rol de grupo de acceso con privilegios de Azure.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con un usuario que tenga el rol [Administrador global](../roles/permissions-reference.md#global-administrator) o que esté asignado como propietario del grupo.

1. Abra **Azure AD Privileged Identity Management**.

1. Seleccione **Acceso con privilegios (versión preliminar)** .

1. Seleccione el grupo que quiere administrar.

    ![Grupos de acceso con privilegios en función del nombre de grupo](./media/groups-role-settings/group-select.png)

1. Seleccione **Configuración**.

    ![Página de configuración que enumera las opciones de configuración del grupo seleccionado](./media/groups-role-settings/group-settings-select-role.png)

1. Seleccione el rol de propietario o miembro cuya configuración quiere ver o cambiar. Puede ver la configuración actual del rol en la página de **detalles de configuración del rol**.

    ![Página de detalles de configuración de roles que muestra varias opciones de asignación y activación](./media/groups-role-settings/group-role-setting-details.png)

1. Seleccione **Editar** para abrir la página de **configuración de la edición de roles**. La pestaña de **activación** le permite cambiar la configuración de activación de roles, incluyendo si quiere permitir asignaciones permanentes activas y válidas.

    ![Página Editar configuración de rol con la pestaña Activación abierta](./media/groups-role-settings/role-settings-activation-tab.png)

1. Seleccione la pestaña de **asignación** para abrir la pestaña de configuración de asignaciones. Esta configuración controla la configuración de asignaciones de Privileged Identity Management para este rol.

    ![Pestaña Asignación de roles en la página Configuración de rol](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Use la pestaña **Notificación** o el botón **Siguiente: Activación** situado en la parte inferior de la página para ir a la pestaña Configuración de notificaciones de este rol. Estas opciones controlan todas las notificaciones de correo electrónico relacionadas con este rol.

    ![Pestaña Notificaciones de rol en la página Configuración de rol](./media/groups-role-settings/role-settings-notification-tab.png)

1. Seleccione el botón **Actualizar** en cualquier momento para actualizar la configuración del rol.

En la pestaña **Notificaciones** de la página Configuración de roles, Privileged Identity Management permite realizar un control pormenorizado sobre quién recibe las notificaciones y qué notificaciones reciben.

- **Desactivación de un correo electrónico**<br>Si desactiva la casilla de destinatario predeterminado y elimina los destinatarios adicionales, puede desactivar correos electrónicos específicos.  
- **Limite de correos electrónicos a direcciones especificadas**<br>Si desactiva la casilla destinatario predeterminado, puede desactivar los mensajes de correo electrónico enviados a destinatarios predeterminados. Después puede agregar direcciones de correo electrónico adicionales como destinatarios adicionales. Si desea agregar más de una dirección de correo electrónico, sepárelas con un punto y coma (;).
- **Envío de correos electrónicos a destinatarios predeterminados y a destinatarios adicionales**<br>Si selecciona la casilla de destinatario predeterminado y agrega direcciones de correo electrónico para destinatarios adicionales, puede enviar correos electrónicos al destinatario predeterminado y a destinatarios adicionales.
- **Solo correo electrónico crítico**<br>Para cada tipo de correo electrónico, puede activar la casilla para recibir solo correos electrónicos críticos. Esto significa que Privileged Identity Management continuará enviando mensajes de correo electrónico a los destinatarios configurados solo cuando el correo electrónico requiera una acción inmediata. Por ejemplo, no se desencadenarán los mensajes de correo electrónico que pidan a los usuarios que amplíen su asignación de roles, pero sí los que requieran que los administradores aprueben una solicitud de ampliación.

## <a name="assignment-duration"></a>Duración de la asignación

Puede elegir entre dos opciones de duración de asignación para cada tipo de asignación (Apto y Activo) cuando se configuran las opciones para un rol. Estas opciones se convierten en la duración máxima predeterminada cuando se asigna un usuario al rol en Privileged Identity Management.

Puede elegir uno de estas opciones de duración de asignación tipo **Apto**:

| | Descripción |
| --- | --- |
| **Permitir asignación elegible permanente** | Los administradores de recursos pueden asignar una asignación válida permanente. |
| **Hacer que las asignaciones elegibles expiren después de** | Los administradores de recursos pueden requerir que todas las asignaciones elegibles tengan una fecha de inicio y finalización especificada. |

Además, puede elegir una de estas opciones de duración de asignación tipo **Activo**:

| | Descripción |
| --- | --- |
| **Permitir asignaciones activas permanentes** | Los administradores de recursos pueden asignar una asignación activa permanente. |
| **Hacer que las asignaciones activas expiren después de** | Los administradores de recursos pueden requerir que todas las asignaciones activas tengan una fecha de inicio y finalización especificada. |

> [!NOTE]
> Los administradores de recursos pueden renovar todas las asignaciones que tienen una fecha de finalización específica. Además, los usuarios pueden iniciar solicitudes de autoservicio para [ampliar o renovar las asignaciones de roles](pim-resource-roles-renew-extend.md).

## <a name="require-multifactor-authentication"></a>Requiere autenticación multifactor

Privileged Identity Management proporciona el cumplimiento opcional de Azure AD Multi-Factor Authentication en dos escenarios distintos.

### <a name="require-multifactor-authentication-on-active-assignment"></a>Requerir autenticación multifactor para las asignaciones activas

Esta opción requiere que los administradores completen una autenticación multifactor antes de crear una asignación de roles activa (en lugar de elegible). Privileged Identity Management no puede exigir la autenticación multifactor cuando el usuario utiliza su asignación de roles, porque ya está activa en el rol desde el momento en que se asigna.

Para requerir la autenticación multifactor al crear una asignación de roles activa, seleccione la casilla **Requerir autenticación multifactor para las asignaciones activas**.

### <a name="require-multifactor-authentication-on-activation"></a>Requerir autenticación multifactor durante la activación

Puede exigir que los usuarios que sean elegibles para un rol demuestren quiénes están usando Azure AD Multi-Factor Authentication antes de que se puedan activar. La autenticación multifactor garantiza que el usuario sea quien dice ser con certeza razonable. Aplicar esta opción protege los recursos críticos en situaciones en las que es posible que la cuenta de usuario se haya puesto en peligro.

Para requerir la autenticación multifactor antes de la activación, active la casilla **Requerir autenticación multifactor durante la activación**.

Para más información, consulte [Autenticación multifactor y Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duración máxima de la activación

Use control deslizante **Duración máxima de la activación (horas)** para establecer el tiempo máximo, en horas, que un rol permanece activo antes de expirar. Este valor puede oscilar entre una y 24 horas.

## <a name="require-justification"></a>Requerir justificación

Puede requerir que los usuarios escriban una justificación empresarial cuando se activen. Para requerir justificación, active la casilla **Requerir justificación para las asignaciones activas** o la casilla **Requerir justificación en las activaciones**.

## <a name="require-approval-to-activate"></a>Solicitud de aprobación para activar

Si desea solicitar aprobación para activar un rol, siga estos pasos.

1. Active la casilla **Se requiere aprobación para activar**.

1. Elija **Seleccionar aprobadores** para abrir la página **Seleccionar un miembro o grupo**.

    ![Seleccionar un panel de usuarios o grupos para seleccionar aprobadores](./media/groups-role-settings/group-settings-select-approvers.png)

1. Seleccione al menos un usuario o grupo y, luego, haga clic en **Seleccionar**. Puede agregar cualquier combinación de usuarios y grupos. Debe seleccionar al menos un aprobador. No hay aprobadores predeterminados.

    Su elección aparecerá en la lista de aprobadores seleccionados.

1. Una vez que haya especificado todos los valores de rol, seleccione **Actualizar** para guardar los cambios.

## <a name="next-steps"></a>Pasos siguientes

- [Asignación de la propiedad o la pertenencia a grupos de acceso con privilegios en PIM](groups-assign-member-owner.md)
