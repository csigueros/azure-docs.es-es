---
title: 'Asignación de roles de Azure a usuarios invitados externos mediante Azure Portal: RBAC de Azure'
description: Aprenda a conceder acceso a recursos de Azure para los usuarios externos a una organización mediante Azure Portal y el control de acceso basado en rol (RBAC) de Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/28/2021
ms.author: rolyon
ms.custom: it-pro,subject-rbac-steps
ms.openlocfilehash: 175beacc486c4b59919bf20300bbd06f7b9aa1a7
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988608"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Asignación de roles de Azure a usuarios invitados externos mediante Azure Portal

El [control de acceso basado en rol (RBAC) de Azure](overview.md) permite una mejor administración de la seguridad para organizaciones grandes y para PYMES que trabajan con colaboradores externos, proveedores o autónomos que necesitan tener acceso a recursos específicos de su entorno, pero no necesariamente a toda la infraestructura ni a los ámbitos relacionados con la facturación. Puede usar las funcionalidades de [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) para colaborar con usuarios invitados externos y puede usar RBAC para conceder solo los permisos que los usuarios invitados necesitan en su entorno.

## <a name="prerequisites"></a>Requisitos previos

Para asignar roles de Azure o quitar asignaciones de roles, debe tener:

- Permisos `Microsoft.Authorization/roleAssignments/write` y `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de acceso de usuarios](built-in-roles.md#user-access-administrator) o [propietario](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>¿Cuándo invitará a los usuarios invitados?

A continuación se describen algunos escenarios de ejemplo en los que podría invitar a usuarios invitados a su organización y concederles permisos:

- Permitir que un proveedor externo por cuenta propia que solo tiene una cuenta de correo electrónico acceda a los recursos de Azure para la realización de un proyecto
- Permitir que un asociado externo administre determinados recursos o una suscripción completa.
- Permitir que los ingenieros del servicio de soporte técnico que no están en su organización (por ejemplo, el soporte técnico de Microsoft) accedan temporalmente a los recursos de Azure para solucionar problemas

## <a name="permission-differences-between-member-users-and-guest-users"></a>Diferencias de permisos entre usuarios miembros y usuarios invitados

Los miembros nativos de un directorio (usuarios miembros) tienen permisos diferentes que los usuarios invitados de otro directorio como invitado de colaboración B2B (usuarios invitados). Por ejemplo, los usuarios miembros puede leer casi toda la información del directorio, mientras que los usuarios invitados tienen permisos de directorio restringidos. Para más información sobre los usuarios miembros y los usuarios invitados, consulte [¿Cuales son los permisos de usuario predeterminados en Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Adición de un usuario invitado a su directorio

Siga estos pasos para agregar un usuario invitado a su directorio mediante la página de Azure Active Directory.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Asegúrese de que la configuración de colaboración externa de la organización se configure de forma que le permita invitar a otros usuarios. Para más información, consulte [Habilitación de la colaboración externa B2B y administración de quién puede invitar a otros usuarios](../active-directory/external-identities/delegate-invitations.md).

1. Haga clic en **Azure Active Directory** > **Usuarios** > **Nuevo usuario invitado**.

    ![Captura de pantalla de la característica Nuevo usuario invitado en Azure Portal.](./media/role-assignments-external-users/invite-guest-user.png)

1. Siga los pasos para agregar un nuevo usuario invitado. Para más información, consulte [Incorporación de usuarios de colaboración B2B de Azure Active Directory en Azure Portal](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory).

Después de agregar un usuario invitado al directorio, puede enviarle un vínculo directo a una aplicación compartida, o bien, el propio usuario invitado puede hacer clic en el vínculo de aceptación de la invitación en el correo electrónico de invitación.

![Captura de pantalla del correo electrónico de invitación de usuario invitado.](./media/role-assignments-external-users/invite-email.png)

Para que el usuario invitado pueda acceder a su directorio, debe completar el proceso de invitación.

![Captura de pantalla de la revisión de los permisos de la invitación del usuario invitado.](./media/role-assignments-external-users/invite-review-permissions.png)

Para más información sobre el proceso de invitación, consulte [Canje de invitación de colaboración B2B de Azure Active Directory](../active-directory/external-identities/redemption-experience.md).

## <a name="assign-a-role-to-a-guest-user"></a>Asignación de un rol a un usuario invitado

En RBAC, para conceder acceso es preciso asignar un rol. Para asignar un rol a un usuario invitado, siga los [mismos pasos](role-assignments-portal.md) que para un usuario, un grupo, una entidad de servicio o una identidad administrada miembro. Siga estos pasos para asignar un rol a un usuario invitado en distintos ámbitos.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el cuadro de búsqueda de la parte superior, busque el ámbito al que quiere conceder acceso. Por ejemplo, busque **Grupos de administración**, **Suscripciones**, **Grupos de recursos** o un recurso específico.

1. Haga clic en el recurso específico de ese ámbito.

1. Haga clic en **Control de acceso (IAM).**

    A continuación se muestra un ejemplo de la página Control de acceso (IAM) de un grupo de recursos.

    ![Captura de pantalla de la página Control de acceso (IAM) de un grupo de recursos en la experiencia con la versión preliminar.](./media/shared/rg-access-control.png)

1. Haga clic en la pestaña **Asignaciones de roles** para ver todas las asignaciones de roles en este ámbito.

1. Haga clic en **Agregar** > **Agregar asignación de roles (versión preliminar)** .

    Si no tiene permisos para asignar roles, la opción Agregar asignación de roles se deshabilitará.

    ![Captura de pantalla del menú Agregar > Agregar asignación de roles en la experiencia con la versión preliminar.](./media/shared/add-role-assignment-menu-preview.png)

    Se abre la página Agregar asignación de roles.

1. En la pestaña **Rol**, seleccione un rol como **Colaborador de la máquina virtual**.

   ![Captura de pantalla de la página Agregar asignación de roles con la pestaña Roles de la experiencia de versión preliminar.](./media/shared/roles.png)

1. En la pestaña **Miembros**, seleccione **Usuario, grupo o entidad de servicio**.

   ![Captura de pantalla de la página Agregar asignación de roles con la pestaña Miembros de la experiencia de versión preliminar.](./media/shared/members.png)

1. Haga clic en **Seleccionar miembros**.

1. Busque y seleccione el usuario invitado. Si no ve el usuario en la lista, puede escribir en el cuadro **Seleccionar** a fin de buscar nombres para mostrar y direcciones de correo electrónico en el directorio.

    Puede escribir en el cuadro **Seleccionar** para buscar en el directorio por nombres para mostrar o direcciones de correo electrónico.

    ![Captura de pantalla del panel Seleccionar miembros de la experiencia de versión preliminar.](./media/role-assignments-external-users/select-members.png)

1. Haga clic en **Seleccionar** para agregar al usuario invitado a la lista Miembros.

1. En la pestaña **Revisar y asignar**, haga clic en **Revisar y asignar**.

    Transcurridos unos instantes, al usuario invitado se le asigna el rol en el ámbito seleccionado.

    ![Captura de pantalla de la asignación del rol Colaborador de la máquina virtual.](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>Asignación de un rol a un usuario invitado que todavía no está en el directorio

Para asignar un rol a un usuario invitado, siga los [mismos pasos](role-assignments-portal.md) que para un usuario, un grupo, una entidad de servicio o una identidad administrada miembro.

Si el usuario invitado todavía no está en el directorio, puede invitarlo directamente desde el panel Seleccionar miembros.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el cuadro de búsqueda de la parte superior, busque el ámbito al que quiere conceder acceso. Por ejemplo, busque **Grupos de administración**, **Suscripciones**, **Grupos de recursos** o un recurso específico.

1. Haga clic en el recurso específico de ese ámbito.

1. Haga clic en **Control de acceso (IAM).**

1. Haga clic en **Agregar** > **Agregar asignación de roles (versión preliminar)** .

    Si no tiene permisos para asignar roles, la opción Agregar asignación de roles se deshabilitará.

    ![Captura de pantalla del menú Agregar > Agregar asignación de roles en la experiencia con la versión preliminar.](./media/shared/add-role-assignment-menu-preview.png)

    Se abre la página Agregar asignación de roles.

1. En la pestaña **Rol**, seleccione un rol como **Colaborador de la máquina virtual**.

1. En la pestaña **Miembros**, seleccione **Usuario, grupo o entidad de servicio**.

   ![Captura de pantalla de la página Agregar asignación de roles con la pestaña Miembros de la experiencia de versión preliminar.](./media/shared/members.png)

1. Haga clic en **Seleccionar miembros**.

1. En el cuadro **Seleccionar**, escriba la dirección de correo electrónico de la persona a la que quiera invitar y seleccione esa persona.

    ![Captura de pantalla de Invitar a un usuario invitado en el panel Seleccionar miembros.](./media/role-assignments-external-users/select-members-new-guest.png)

1. Haga clic en **Seleccionar** para agregar al usuario invitado a la lista Miembros.

1. En la pestaña **Review + assign** (Revisar y asignar), haga clic en **Review + assign** (Revisar y asignar) para agregar el usuario invitado al directorio, asignar el rol y enviar una invitación.

    Transcurridos unos instantes, verá una notificación de la asignación de roles e información sobre la invitación.

    ![Captura de pantalla de la asignación de roles y la notificación de usuario invitado.](./media/role-assignments-external-users/invited-user-notification.png)

1. Para invitar manualmente al usuario invitado, haga clic con el botón derecho y copie el vínculo de invitación de la notificación. No haga clic en el vínculo de invitación porque eso inicia el proceso de invitación.

    El vínculo de invitación tendrá el siguiente formato:

    `https://login.microsoftonline.com/redeem?rd=https%3a%2f%2finvitations.microsoft.com%2fredeem%2f%3ftenant%3d0000...`

1. Envíe el vínculo de invitación al usuario invitado para completar el proceso de invitación.

    Para más información sobre el proceso de invitación, consulte [Canje de invitación de colaboración B2B de Azure Active Directory](../active-directory/external-identities/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Eliminación de un usuario invitado del directorio

Antes de eliminar un usuario invitado de un directorio, primero debe eliminar las asignaciones de roles de dicho usuario invitado. Siga estos pasos para eliminar un usuario invitado de un directorio.

1. Abra **Control de acceso (IAM)** en un ámbito como, por ejemplo, grupo de administración, suscripción, grupo de recursos o recurso, en el que el usuario invitado tenga una asignación de roles.

1. Haga clic en la pestaña **Asignaciones de roles** para ver todas las asignaciones de roles.

1. En la lista de asignaciones de roles, agregue una marca de verificación junto al usuario invitado con la asignación de roles que quiera eliminar.

   ![Captura de pantalla de la asignación de roles seleccionada para eliminar.](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Haga clic en **Quitar**.

   ![Captura de pantalla del mensaje Quitar asignación de roles.](./media/shared/remove-role-assignment.png)

1. En el mensaje de eliminación de asignación de roles que aparece, haga clic en **Sí**.

1. En la barra de navegación izquierda, haga clic en **Azure Active Directory** > **Usuarios**.

1. Haga clic en el usuario invitado que desea eliminar.

1. Haga clic en **Eliminar**.

   ![Captura de pantalla de la eliminación del usuario invitado.](./media/role-assignments-external-users/delete-guest-user.png)

1. En el mensaje de eliminación que aparece, haga clic en **Sí**.

## <a name="troubleshoot"></a>Solución de problemas

### <a name="guest-user-cannot-browse-the-directory"></a>El usuario invitado no puede examinar el directorio

Los usuarios invitados tienen permisos de directorio restringidos. Por ejemplo, los usuarios invitados no pueden examinar el directorio y no pueden buscar grupos o aplicaciones. Para más información, consulte [¿Cuáles son los permisos de usuario predeterminados en Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![Captura de pantalla del usuario invitado que no puede examinar los usuarios de un directorio.](./media/role-assignments-external-users/directory-no-users.png)

Si un usuario invitado necesita privilegios adicionales en el directorio, puede asignarle un rol de Azure AD. Si realmente desea que un usuario invitado tenga acceso de lectura completo al directorio, puede agregar el usuario invitado al rol [Lectores de directorio](../active-directory/roles/permissions-reference.md#directory-readers) en Azure AD. Para más información, consulte [Incorporación de usuarios de colaboración B2B de Azure Active Directory en Azure Portal](../active-directory/external-identities/add-users-administrator.md).

![Captura de pantalla de la asignación del rol Lectores de directorios.](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>El usuario invitado no puede examinar usuarios, grupos o entidades de servicio para asignar roles

Los usuarios invitados tienen permisos de directorio restringidos. Incluso si un usuario invitado es [Propietario](built-in-roles.md#owner) en un ámbito, si intenta asignar un rol para conceder acceso a otra persona, no podrá examinar la lista de usuarios, grupos ni entidades de servicio.

![Captura de pantalla del usuario invitado que no puede examinar las entidades de seguridad para asignar roles.](./media/role-assignments-external-users/directory-no-browse.png)

Si el usuario invitado conoce el nombre de inicio de sesión exacto de alguien del directorio, puede conceder acceso. Si realmente desea que un usuario invitado tenga acceso de lectura completo al directorio, puede agregar el usuario invitado al rol [Lectores de directorio](../active-directory/roles/permissions-reference.md#directory-readers) en Azure AD. Para más información, consulte [Incorporación de usuarios de colaboración B2B de Azure Active Directory en Azure Portal](../active-directory/external-identities/add-users-administrator.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>El usuario invitado no puede registrar aplicaciones ni crear entidades de servicio

Los usuarios invitados tienen permisos de directorio restringidos. Si un usuario invitado necesita poder registrar aplicaciones o crear entidades de servicio, puede agregar el usuario invitado al rol [Desarrollador de aplicaciones](../active-directory/roles/permissions-reference.md#application-developer) en Azure AD. Para más información, consulte [Incorporación de usuarios de colaboración B2B de Azure Active Directory en Azure Portal](../active-directory/external-identities/add-users-administrator.md).

![Captura de pantalla del usuario invitado que no puede registrar aplicaciones.](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>El usuario invitado no ve el nuevo directorio

Si a un usuario invitado se le ha concedido acceso a un directorio, pero no ve el nuevo directorio en Azure Portal cuando intenta cambiar en su página **Directorios**, asegúrese de que el usuario invitado haya completado el proceso de invitación. Para más información sobre el proceso de invitación, consulte [Canje de invitación de colaboración B2B de Azure Active Directory](../active-directory/external-identities/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>El usuario invitado no ve los recursos

Si a un usuario invitado se le ha concedido acceso a un directorio, pero no ve los recursos a los que se le ha concedido acceso en Azure Portal, asegúrese de que el usuario invitado ha seleccionado el directorio correcto. Un usuario invitado podría tener acceso a varios directorios. Para cambiar de directorio, en la parte superior izquierda, haga clic en **Configuración** > **Directorios** y después en el directorio adecuado.

![Captura de pantalla de la sección Directorios de configuración de Azure Portal.](./media/role-assignments-external-users/directory-switch.png)

## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de usuarios de colaboración B2B de Azure Active Directory en Azure Portal](../active-directory/external-identities/add-users-administrator.md)
- [Propiedades de un usuario de colaboración B2B de Azure Active Directory](../active-directory/external-identities/user-properties.md)
- [Elementos del correo electrónico de invitación para la colaboración B2B: Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [Adición de un usuario invitado como coadministrador](classic-administrators.md#add-a-guest-user-as-a-co-administrator)