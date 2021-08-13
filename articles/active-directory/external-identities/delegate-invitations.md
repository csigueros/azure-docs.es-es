---
title: 'Habilitación de los valores de colaboración externa B2B: Azure AD'
description: Obtenga información sobre cómo habilitar la colaboración externa B2B de Active Directory y administrar quién puede invitar a usuarios invitados. Uso del rol de invitador de usuarios invitados para delegar invitaciones.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fd3ef3ed0ad072bb0e9284892fa5455660d1c40
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963613"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Habilitación de la colaboración externa B2B y administración de quién puede invitar a otros usuarios

En este artículo se describe cómo habilitar la colaboración B2B de Azure Active Directory (Azure AD), designar quién puede tener invitados y determinar los permisos que tienen los usuarios invitados en Azure AD. 

De manera predeterminada, todos los usuarios e invitados del directorio pueden invitar a otros usuarios, incluso si no tienen asignado un rol de administrador. La configuración de colaboración externa le permite activar o desactivar las invitaciones de invitados para los distintos tipos de usuarios de la organización. También puede delegar las invitaciones a usuarios individuales mediante la asignación de roles que les permitan invitar a otros usuarios.

Azure AD le permite restringir qué usuarios invitados externos se pueden ver en el directorio de Azure AD. De forma predeterminada, los usuarios invitados tienen un nivel de permiso limitado que les impide enumerar usuarios, grupos u otros recursos de directorio, pero sí ver la pertenencia de grupos no ocultos. Una nueva opción en versión preliminar le permite restringir aún más el acceso de invitado, de modo que los invitados solo pueden ver su propia información de perfil. Para los detalles, consulte [Restricción de los permisos de acceso de invitado (versión preliminar)](../enterprise-users/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>Configuración de los valores de colaboración externa B2B

Gracias a la colaboración B2B de Azure AD, el administrador de inquilinos puede establecer las siguientes directivas de invitación:

- Desactivar invitaciones
- Solo los administradores y usuarios del rol Invitador de personas pueden invitar
- Los administradores, el rol Invitador de personas y los miembros pueden invitar
- Todos los usuarios, incluidos los invitados, pueden invitar

De manera predeterminada, todos los usuarios, incluidos los invitados, pueden invitar a usuarios.

### <a name="to-configure-external-collaboration-settings"></a>Para configurar los valores de colaboración externa B2B:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de inquilinos.
2. Seleccione **Azure Active Directory**.
3. Seleccione **External Identities** > **Configuración de colaboración externa**.

4. En **Restricciones de acceso de usuarios invitados (versión preliminar)** , elija el nivel de acceso que desea que tengan los usuarios invitados:
  
   - **Guest users have the same access as members (most inclusive)** (Los usuarios invitados tienen el mismo acceso que los miembros [principalmente inclusivo]): esta opción permite a los invitados tener el mismo acceso a los recursos de Azure AD y a los datos del directorio que los usuarios miembros.

   - **Guest users have limited access to properties and memberships of directory objects** (Los usuarios invitados tienen acceso limitado a las propiedades y pertenencias de los objetos de directorio): (predeterminada) Esta opción impide que los usuarios realicen determinadas tareas de directorio, como enumerar usuarios, grupos u otros recursos de directorio. Los invitados pueden ver la pertenencia de todos los grupos no ocultos.

   - **Guest user access is restricted to properties and memberships of their own directory objects (most restrictive)** (El acceso de los usuarios invitados está restringido a las propiedades y pertenencias de sus propios objetos de directorio [opción más restrictiva]): con esta configuración, los invitados solo pueden tener acceso a sus propios perfiles. No se permite a los invitados ver perfiles, grupos o pertenencias a grupos de otros usuarios.

5. En **Configuración de la invitación de usuarios**, elija la configuración adecuada:

    ![Configuración de la invitación de usuarios](./media/delegate-invitations/guest-invite-settings.png)

   - **Cualquier persona de la organización puede invitar a usuarios, incluidos los invitados y los usuarios que no son administradores (esta es la opción más inclusiva)** : para permitir que los invitados de la organización inviten a otros invitados, incluidos aquellos que no son miembros de una organización, seleccione este botón de radio.
   - **Los usuarios miembros y los usuarios asignados a roles de administrador específicos pueden invitar a usuarios, incluidos aquellos con permisos de miembro**: para permitir que los usuarios miembros y los usuarios que tienen roles de administrador específicos inviten a invitados, seleccione este botón de radio.
   - **Solo los usuarios asignados a roles de administrador específicos pueden invitar a otros usuarios**: para permitir que solo los usuarios con roles de administrador inviten a invitados, seleccione este botón de radio. Los roles de administrador incluyen [Administrador global](../roles/permissions-reference.md#global-administrator), [Administrador de usuarios](../roles/permissions-reference.md#user-administrator) e [Invitador de usuarios invitados](../roles/permissions-reference.md#guest-inviter).
   - **Nadie de la organización puede invitar a usuarios, ni siquiera los administradores (esta es la opción más restrictiva)** : para denegar a todos los usuarios de la organización la invitación de invitados, seleccione este botón de radio.
     > [!NOTE]
     > Si **Los miembros pueden invitar** está establecido en **No** y **Los administradores y los usuarios del rol de invitador de personas pueden invitar** está establecido en **Sí**, los usuarios del rol **Invitador de usuarios invitados** todavía podrán invitar a usuarios invitados.

6. En **Email one-time passcode for guests** (Código de acceso de un solo uso por correo electrónico para invitados), elija la configuración adecuada. Para más información, consulte [Autenticación con código de acceso de un solo uso por correo electrónico](one-time-passcode.md):

   - **Automatically enable email one-time passcode for guests in October 2021** (Habilitar automáticamente el código de acceso de un solo uso por correo electrónico a partir de octubre de 2021). Valor predeterminado. Si la característica de código de acceso de un solo uso por correo electrónico no está habilitada para el inquilino, se activará automáticamente en octubre de 2021. Si desea que la característica se habilite desde ese momento, no tiene que hacer nada más. Si ya ha habilitado o deshabilitado la característica, esta opción no estará disponible.

   - **Enable email one-time passcode for guests effective now** (Habilitar el código de acceso de un solo uso por correo electrónico para invitados desde este momento). Activa la característica de código de acceso de un solo uso por correo electrónico para el inquilino.

   - **Disable email one-time passcode for guests** (Deshabilitar el código de acceso de un solo uso por correo electrónico para invitados). Desactiva la característica de código de acceso de un solo uso por correo electrónico para el inquilino y evita que la característica se active en octubre de 2021.

   > [!NOTE]
   > En lugar de las opciones anteriores, verá el siguiente botón si ha habilitado o deshabilitado esta característica, o bien si ha participado previamente en la versión preliminar:
   >
   >![Habilitación de la participación en el código de acceso de un solo uso por correo electrónico](media/delegate-invitations/enable-email-otp-opted-in.png)

7. En **Enable guest self-service sign up via user flows** (Habilitación del registro de autoservicio de invitados mediante flujos de usuario), seleccione **Yes** (Sí) si desea crear flujos de usuario que permitan a los usuarios suscribirse a las aplicaciones. Para más información sobre este valor, consulte [Incorporación de un flujo de usuario de registro de autoservicio a una aplicación](self-service-sign-up-user-flow.md).

    ![Configuración de la suscripción de autoservicio a través de flujos de usuario](./media/delegate-invitations/self-service-sign-up-setting.png)

7. En **Restricciones de colaboración**, puede elegir si desea permitir o denegar las invitaciones a los dominios que especifique y especificar nombres de dominio concretos en los cuadros de texto. Si hay varios dominios, especifique cada dominio en una nueva línea. Para obtener más información, consulte [Allow or block invitations to B2B users from specific organizations](allow-deny-list.md) (Permitir o bloquear invitaciones a usuarios de B2B procedentes de determinadas organizaciones).

    ![Configuración de restricciones de colaboración](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>Asignación del rol de invitador de usuarios invitados a un usuario

Con el rol de invitador de usuarios invitados, puede conceder a usuarios individuales la capacidad de invitar a otros usuarios sin asignarles un rol de administrador global u otro rol de administrador. Asigne el rol de invitador de usuarios invitados a diferentes individuos. A continuación, asegúrese de establecer la opción **Los administradores y los usuarios del rol de invitador de usuarios invitados pueden invitar** en **Sí**.

Este es un ejemplo que muestra cómo usar PowerShell para agregar un usuario al rol Invitador de personas:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos sobre la colaboración de B2B de Azure AD:

- [¿Qué es la colaboración B2B de Azure AD?](what-is-b2b.md)
- [Incorporación de usuarios de colaboración B2B sin invitación](add-user-without-invite.md)
- [Incorporación de usuarios de colaboración B2B a un rol](./add-users-administrator.md)