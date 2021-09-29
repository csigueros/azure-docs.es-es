---
title: Restablecimiento de la contraseña de un usuario en Azure Active Directory | Microsoft Docs
description: Instrucciones acerca de cómo restablecer una contraseña de usuario con Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7efa458ebcae7e837be4bb574f4a2707e9cb9e30
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732603"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Restablecimiento de la contraseña de un usuario con Azure Active Directory

Como administrador, puede restablecer la contraseña de un usuario si se olvida la contraseña, si el usuario bloquea su dispositivo o si nunca ha recibido una contraseña.

>[!Note]
>Si el inquilino de Azure AD no es el directorio principal de un usuario, no podrá restablecer su contraseña. Esto significa que si el usuario inicia sesión en su organización mediante una cuenta de otra organización, una cuenta de Microsoft o una cuenta de Google, no podrá restablecer su contraseña.<br><br>Si el usuario tiene una fuente de autoridad como Windows Server Active Directory, solo podrá restablecer la contraseña si ha activado la escritura diferida de contraseñas.<br><br>Si el usuario tiene una fuente de autoridad como Azure AD externo, no podrá restablecer la contraseña. Solo el usuario, o un administrador de Azure AD externo, puede restablecer la contraseña.

>[!Note]
>Si no es administrador y busca instrucciones acerca de cómo restablecer la contraseña profesional o educativa, consulte [Restablecimiento de la contraseña profesional o educativa](https://support.microsoft.com/account-billing/reset-your-work-or-school-password-using-security-info-23dde81f-08bb-4776-ba72-e6b72b9dda9e).

## <a name="to-reset-a-password"></a>Para restablecer una contraseña

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador de usuarios o administrador de contraseñas. Para obtener más información sobre los roles disponibles, consulte [Roles integrados de Azure AD](../roles/permissions-reference.md).

2. Seleccione **Azure Active Directory**, seleccione **Usuarios** y, a continuación, busque y seleccione los usuarios que necesitan del restablecimiento y haga clic en **Restablecer contraseña**.

    Se muestra la página **Alain Charon - Perfil** con la opción **Restablecer contraseña**.

    ![Página de perfil del usuario, con la opción Restablecer contraseña resaltada](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. En la página **Restablecer contraseña**, seleccione **Restablecer contraseña**.

    > [!Note]
    > Al usar Azure Active Directory, una contraseña temporal se genera automáticamente para el usuario. En cambio cuando se usa Active Directory local, se crea la contraseña del usuario.

4. Copie la contraseña y proporciónela al usuario. El usuario deberá cambiar la contraseña durante el siguiente proceso de inicio de sesión.

    >[!Note]
    >La contraseña temporal nunca expira. La próxima vez que el usuario inicie sesión, la contraseña seguirá funcionando, sin importar cuánto tiempo haya transcurrido desde que se generó la contraseña temporal.

> [!IMPORTANT]
> Si un administrador no puede restablecer la contraseña del usuario y en los registros de eventos de la aplicación en el servidor de Azure AD Connect aparece el código de error hr=80231367, revise los atributos del usuario en Active Directory.  Si el atributo **AdminCount** está establecido en 1, impedirá que un administrador restablezca la contraseña del usuario.  El atributo **AdminCount** debe establecerse en 0 para que los administradores puedan restablecer la contraseña del usuario.


## <a name="next-steps"></a>Pasos siguientes

Después de restablecer la contraseña del usuario, puede realizar los siguientes procesos básicos:

- [Adición o eliminación de usuarios](add-users-azure-active-directory.md)

- [Asignación de roles a usuarios](active-directory-users-assign-role-azure-portal.md)

- [Add or change profile information](active-directory-users-profile-azure-portal.md) (Incorporación o modificación de la información del perfil)

- [Creación de un grupo básico e incorporación de miembros](active-directory-groups-create-azure-portal.md)

O bien, puede realizar otros escenarios de usuario complejos, como asignar delegados, usar directivas y compartir cuentas de usuario. Para obtener más información acerca de otras acciones disponibles, consulte la [documentación de administración de usuarios en Azure Active Directory](../enterprise-users/index.yml).