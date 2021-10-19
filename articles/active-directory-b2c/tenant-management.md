---
title: Administración de Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Obtenga información sobre cómo administrar el inquilino de Azure Active Directory B2C. Descubra qué características de Azure AD son compatibles con Azure AD B2C, cómo usar roles de administrador para administrar recursos y cómo agregar cuentas profesionales y usuarios invitados a su inquilino de Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7e364b3ce5231370643f9c9ea730dec22ab9022a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615949"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Administración del inquilino de Azure Active Directory B2C

En Azure Active Directory B2C (Azure AD B2C), un inquilino representa el directorio de usuarios consumidores. Cada inquilino de Azure AD B2C es distinto e independiente de cualquier inquilino de Azure AD B2C. Un inquilino de Azure AD B2C es diferente de un inquilino de Azure Active Directory, que es posible que ya tenga. En este artículo, aprenderá a administrar su inquilino de Azure AD B2C.

## <a name="prerequisites"></a>Prerrequisitos
- Si aún no ha creado su propio [inquilino de Azure AD B2C](tutorial-create-tenant.md), cree una ahora. Puede usar un inquilino de Azure AD B2C existente.
- Conocer las [cuentas de usuario en Azure AD B2C](user-overview.md).
- Conocer el [uso de roles para controlar el acceso a los recursos](roles-resource-access-control.md).


## <a name="add-an-administrator-work-account"></a>Adición de un administrador (cuenta profesional)

Para crear una cuenta administrativa, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con permisos de administrador de roles con privilegios o de administrador global.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En **Servicios de Azure**, seleccione **Azure AD B2C**. O bien, use el cuadro de búsqueda para buscar y seleccionar **Azure AD B2C**.
1. En **Administrar**, seleccione **Usuarios**.
1. Seleccione **Nuevo usuario**.
1. En la página **Usuario**, escriba información para este usuario:

   - **Nombre**. Necesario. Nombre y apellidos del nuevo usuario. Por ejemplo, *Mary Parker*.
   - **Nombre de usuario**. Necesario. Nombre de usuario del nuevo usuario. Por ejemplo, `mary@contoso.com`.
     La parte del dominio del nombre de usuario debe usar el nombre de dominio predeterminado inicial, *\<yourdomainname>.onmicrosoft.com*.
   - **Grupos**. Si quiere, puede agregar el usuario a uno o varios de los grupos existentes. También puede agregar el usuario a grupos en un momento posterior. 
   - **Rol del directorio**. Si necesita permisos administrativos de Azure AD para el usuario, puede agregarlos a un rol de Azure AD. Puede asignar el rol de administrador global al usuario, o uno o varios de los otros roles de administrador limitados de Azure AD. Para más información sobre la asignación de roles, vea [Uso de roles para controlar el acceso a los recursos](roles-resource-access-control.md).
   - **Información del trabajo**. Puede agregar más información sobre el usuario aquí o hacerlo más adelante. 

1. Copie la contraseña generada automáticamente proporcionada en el cuadro de texto **Contraseña**. Deberá proporcionar esta contraseña al usuario para iniciar sesión por primera vez.
1. Seleccione **Crear**.

El usuario se crea y se agrega al inquilino de Azure AD B2C. Es preferible tener al menos una cuenta profesional nativa del inquilino de Azure AD B2C asignado al rol de administrador global. Esta cuenta se puede considerar una cuenta de emergencia.

## <a name="invite-an-administrator-guest-account"></a>Invitación de un administrador (cuenta de invitado)

También puede invitar a un nuevo usuario invitado para administrar el inquilino. La cuenta de invitado es la opción preferida cuando la organización también tiene Azure AD, porque el ciclo de vida de esta identidad se puede administrar externamente. 

Para invitar a un usuario, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con permisos de administrador de roles con privilegios o de administrador global.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En **Servicios de Azure**, seleccione **Azure AD B2C**. O bien, use el cuadro de búsqueda para buscar y seleccionar **Azure AD B2C**.
1. En **Administrar**, seleccione **Usuarios**.
1. Seleccione **New guest account** (Nueva cuenta de invitado).
1. En la página **Usuario**, escriba información para este usuario:

   - **Nombre**. Necesario. Nombre y apellidos del nuevo usuario. Por ejemplo, *Mary Parker*.
   - **Dirección de correo electrónico**. Necesario. Dirección de correo electrónico del usuario al que quiera invitar. Por ejemplo, `mary@contoso.com`.   
   - **Mensaje personal**: agregue un mensaje personal que se incluirá en el correo electrónico de invitación.
   - **Grupos**. Si quiere, puede agregar el usuario a uno o varios de los grupos existentes. También puede agregar el usuario a grupos en un momento posterior.
   - **Rol del directorio**. Si necesita permisos administrativos de Azure AD para el usuario, puede agregarlos a un rol de Azure AD. Puede asignar el rol de administrador global al usuario, o uno o varios de los otros roles de administrador limitados de Azure AD. Para más información sobre la asignación de roles, vea [Uso de roles para controlar el acceso a los recursos](roles-resource-access-control.md).
   - **Información del trabajo**. Puede agregar más información sobre el usuario aquí o hacerlo más adelante.

1. Seleccione **Crear**.

Se envía un correo electrónico de invitación al usuario. El usuario debe aceptar la invitación para poder iniciar sesión.

### <a name="resend-the-invitation-email"></a>Reenvío del correo electrónico de invitación

Si el invitado no recibió el correo electrónico de invitación o la invitación expiró, puede reenviarla. Como alternativa al correo electrónico de invitación, puede proporcionar al invitado un vínculo directo para aceptar la invitación. Para reenviar la invitación y obtener el vínculo directo:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En **Servicios de Azure**, seleccione **Azure AD B2C**. O bien, use el cuadro de búsqueda para buscar y seleccionar **Azure AD B2C**.
1. En **Administrar**, seleccione **Usuarios**.
1. Busque y seleccione el usuario al que desea reenviar la invitación.
1. En la página **Usuario | Perfil**, en **Identidad**, seleccione **(Administrar)** .
        ![Captura de pantalla en la que se muestra cómo reenviar el correo electrónico de invitación de la cuenta de invitado.](./media/tenant-management/guest-account-resend-invite.png)

1. En **¿Quiere volver a enviar la invitación?** , seleccione **Sí**. Cuando aparezca el mensaje **¿Seguro que quiere volver a enviar una invitación?** , seleccione **Sí**.
1. Azure AD B2C envía la invitación. También puede copiar la dirección URL de invitación y proporcionarla directamente al invitado.
    
    ![Captura de pantalla en la que se muestra cómo obtener la dirección URL de invitación.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Adición de una asignación de roles

Puede asignar un rol al [crear un usuario](#add-an-administrator-work-account) o al [invitar a un usuario externo](#invite-an-administrator-guest-account). Puede agregar un rol, cambiarlo o quitarlo para un usuario:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con permisos de administrador de roles con privilegios o de administrador global.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En **Servicios de Azure**, seleccione **Azure AD B2C**. O bien, use el cuadro de búsqueda para buscar y seleccionar **Azure AD B2C**.
1. En **Administrar**, seleccione **Usuarios**.
1. Seleccione el usuario cuyo rol desea modificar. Después, seleccione **Roles asignados**.
1. Seleccione **Agregar asignaciones**, seleccione el rol que asignará (por ejemplo, *Administrador de aplicaciones*) y, a continuación, elija **Agregar**.

## <a name="remove-a-role-assignment"></a>Eliminación de una asignación de rol

Si necesita quitar una asignación de roles de un usuario, siga estos pasos:

1. Seleccione **Azure AD B2C**, seleccione **Usuarios** y, después, busque y seleccione el usuario.
1. Seleccione **Roles asignados**. Seleccione el rol que desea quitar, por ejemplo, *Administrador de aplicaciones* y, después, seleccione **Quitar asignación**.

## <a name="review-administrator-account-role-assignments"></a>Revisión de las asignaciones de roles de la cuenta de administrador

Como parte de un proceso de auditoría, normalmente se revisa qué usuarios están asignados a roles específicos en el directorio de Azure AD B2C. Siga estos pasos para auditar qué usuarios tienen asignados roles con privilegios actualmente.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con permisos de administrador de roles con privilegios o de administrador global.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En **Servicios de Azure**, seleccione **Azure AD B2C**. O bien, use el cuadro de búsqueda para buscar y seleccionar **Azure AD B2C**.
1. En **Administrar**, seleccione **Roles y administradores**.
1. Seleccione un rol, como **Administrador global**. En la página **Rol | Asignaciones**, se muestran los usuarios que tienen dicho rol.

## <a name="delete-an-administrator-account"></a>Eliminación de una cuenta de administrador

Para eliminar un usuario existente, debe tener una asignación de roles de *Administrador global*. Los administradores globales pueden eliminar cualquier usuario, incluidos otros administradores. Los *administradores de usuarios* pueden eliminar cualquier usuario que no sea administrador.

1. En el directorio de Azure AD B2C, seleccione **Usuarios** y, a continuación, seleccione el usuario que desea eliminar.
1. Seleccione **Eliminar** y, después, **Sí** para confirmar la eliminación.

El usuario se elimina y ya no aparece en la página **Users - All users** (Usuarios: Todos los usuarios). El usuario se puede ver en la página **Usuarios eliminados** durante los próximos 30 días y puede restaurarse durante ese tiempo. Para más información sobre cómo restaurar un usuario, consulte [Restauración o eliminación de un usuario recientemente eliminado mediante Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Protección de cuentas administrativas

Se recomienda proteger todas las cuentas de administrador con la autenticación multifactor (MFA) para mayor seguridad. MFA es un proceso de verificación de identidad durante el inicio de sesión que solicita al usuario una forma más de identificación, como un código de verificación en su dispositivo móvil o una solicitud en su aplicación Microsoft Authenticator.

![Métodos de autenticación en uso en la captura de pantalla de inicio de sesión](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Puede habilitar los [valores predeterminados de seguridad de Azure AD](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) para forzar a todas las cuentas administrativas a usar MFA.

## <a name="get-your-tenant-name"></a>Obtención del nombre del inquilino

Para obtener el nombre del inquilino de Azure AD B2C, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. En **Información general**, copie el **nombre de dominio**.

![Captura de pantalla que muestra cómo obtener el nombre del inquilino de Azure AD B2C.](./media/tenant-management/get-azure-ad-b2c-tenant-name.png)  

## <a name="get-your-tenant-id"></a>Obtención del identificador del inquilino

Para obtener el identificador del inquilino de Azure AD B2C, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure Active Directory**.
1. En **Información general**, copie el **identificador de inquilino**.

![Captura de pantalla que muestra cómo obtener el identificador del inquilino de Azure AD B2C.](./media/tenant-management/get-azure-ad-b2c-tenant-id.png)  

## <a name="next-steps"></a>Pasos siguientes

- [Limpieza de recursos y eliminación del inquilino](tutorial-delete-tenant.md)
