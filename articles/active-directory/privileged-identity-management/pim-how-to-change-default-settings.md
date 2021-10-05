---
title: 'Configuración de roles de Azure AD en PIM: Azure AD | Microsoft Docs'
description: Aprenda a configurar roles de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/14/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31d58536b1af26d037b3c53f04bcae5c9359ff47
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606710"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configuración de roles de Azure AD en Privileged Identity Management

Un administrador de roles con privilegios puede personalizar Privileged Identity Management (PIM) en su organización de Azure Active Directory (Azure AD); así, por ejemplo, puede cambiar la experiencia de un usuario que va a activar una asignación de roles válida.

## <a name="open-role-settings"></a>Apertura de la configuración de roles

Siga estos pasos para abrir la configuración de un rol de Azure AD.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con un usuario en el rol [Administrador de roles con privilegios](../roles/permissions-reference.md#privileged-role-administrator).

1. Abra **Azure AD Privileged Identity Management** &gt; **Roles de Azure AD** &gt; **Configuración de roles**.

    ![Página de configuración de roles que muestra los roles de Azure AD](./media/pim-how-to-change-default-settings/role-settings.png)

1. Seleccione el rol cuya configuración desea configurar.

    ![Página de detalles de configuración de roles que muestra varias opciones de asignación y activación](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Seleccione **Editar** para abrir la página Configuración de roles.

    ![Página de edición de configuración de roles con opciones para actualizar la configuración de asignación y activación](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    En el panel Configuración de roles de cada rol, hay varias opciones que puede configurar.

## <a name="assignment-duration"></a>Duración de la asignación

Puede elegir entre dos opciones de duración de asignación para cada tipo de asignación (Apto y Activo) cuando se configuran las opciones para un rol. Estas opciones se convierten en la duración máxima predeterminada cuando se asigna un usuario al rol en Privileged Identity Management.

Puede elegir uno de estas opciones de duración de asignación tipo **Apto**:

| Configuración | Descripción |
| --- | --- |
| Allow permanent eligible assignment (Permitir la asignación apta permanente) | Los administradores globales y los administradores de roles con privilegios pueden asignar una asignación elegible permanente. |
| Hacer que las asignaciones elegibles expiren después de | Los administradores globales y los administradores de roles con privilegios pueden requerir que todas las asignaciones elegibles tengan una fecha de inicio y finalización especificada. |

Además, puede elegir una de estas opciones de duración de asignación tipo **Activo**:

| Configuración | Descripción |
| --- | --- |
| Allow permanent active assignment (Permitir la asignación activa permanente) | Los administradores globales y los administradores de roles con privilegios pueden asignar una asignación activa permanente. |
| Hacer que las asignaciones activas expiren después de | Los administradores globales y los administradores de roles con privilegios pueden requerir que todas las asignaciones activas tengan una fecha de inicio y finalización especificada. |

> [!NOTE]
> Los administradores globales y los administradores de roles con privilegios pueden renovar todas las asignaciones que tienen una fecha de finalización específica. Además, los usuarios pueden iniciar solicitudes de autoservicio para [ampliar o renovar las asignaciones de roles](pim-resource-roles-renew-extend.md).

## <a name="require-multifactor-authentication"></a>Requiere autenticación multifactor

Privileged Identity Management proporciona la obligatoriedad de la autenticación multifactor de Azure AD en la activación y en la asignación activa.

### <a name="on-activation"></a>En la activación

Puede exigir que los usuarios que sean elegibles para un rol demuestren quiénes están usando Azure AD Multi-Factor Authentication antes de que se puedan activar. La autenticación multifactor garantiza que el usuario sea quien dice ser con certeza razonable. Aplicar esta opción protege los recursos críticos en situaciones en las que es posible que la cuenta de usuario se haya puesto en peligro.

Para exigir la autenticación multifactor para activar la asignación de roles, seleccione la opción **On activation, require Azure MFA** (En la activación, exigir Azure MFA) en la pestaña Activación de **Edit role setting** (Editar configuración de roles).

### <a name="on-active-assignment"></a>En la asignación activa

Esta opción requiere que los administradores completen una autenticación multifactor antes de crear una asignación de roles activa (en lugar de elegible). Privileged Identity Management no puede exigir la autenticación multifactor cuando el usuario utiliza su asignación de roles, porque ya está activa en el rol desde el momento en que se asigna.

Para requerir la autenticación multifactor al crear una asignación de roles activa, seleccione la opción **Requerir Azure Multi-Factor Authentication en la asignación activa** en la pestaña Asignación de la opción **Edit role setting** (Editar configuración de roles).

Para más información, consulte [Autenticación multifactor y Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duración máxima de la activación

Use control deslizante **Duración máxima de la activación (horas)** para establecer el tiempo máximo, en horas, que un rol permanece activo antes de expirar. Este valor puede oscilar entre una y 24 horas.

## <a name="require-justification"></a>Requerir justificación

Puede requerir que los usuarios escriban una justificación empresarial cuando se activen. Para requerir justificación, active la casilla **Requerir justificación para las asignaciones activas** o la casilla **Requerir justificación en las activaciones**.

## <a name="require-approval-to-activate"></a>Solicitud de aprobación para activar

Si se establecen varios aprobadores, la aprobación se completa en cuanto uno de ellos aprueba o deniega. No se puede forzar la aprobación de un segundo aprobador o posterior. Para solicitar aprobación a fin de activar un rol, siga estos pasos.

1. Active la casilla **Se requiere aprobación para activar**.

1. Seleccione **Seleccionar aprobadores**.

    ![Seleccionar un panel de usuarios o grupos para seleccionar aprobadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Seleccione al menos un usuario y haga clic en **Seleccionar**. Seleccione por lo menos un aprobador. Si no se seleccionan aprobadores específicos, los administradores globales o de roles con privilegios pasarán a ser los aprobadores predeterminados.

1. Para guardar los cambios, seleccione **Actualizar**.

## <a name="next-steps"></a>Pasos siguientes

- [Asignación de roles de Azure AD en Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configuración de alertas de seguridad para roles de Azure AD en Privileged Identity Management](pim-how-to-configure-security-alerts.md)
