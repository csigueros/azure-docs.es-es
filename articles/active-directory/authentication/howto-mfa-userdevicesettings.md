---
title: 'Administración de los métodos de autenticación para Azure AD Multi-Factor Authentication: Azure Active Directory'
description: Aprenda a definir la configuración de usuarios de Azure Active Directory para Azure AD Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd69e65f5c280eaac8acb41138cf265be0af113
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763032"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Administración de los métodos de autenticación de usuarios para Azure AD Multi-Factor Authentication

Los usuarios de Azure AD tienen dos conjuntos distintos de información de contacto:  

- Información de contacto de perfil público, que se administra en el perfil del usuario y es visible para los miembros de la organización. En el caso de los usuarios sincronizados desde Windows Server AD, esta información se administra en la instancia local de Windows Server Active Directory Domain Services.
- Los métodos de autenticación, que siempre se mantienen privados y solo se usan para la autenticación, incluida la opción de autenticación multifactor (MFA). Los administradores pueden administrar estos métodos en la hoja del método de autenticación de un usuario, y los usuarios pueden administrar sus métodos en la página Información de seguridad de sus cuentas.

Al administrar los métodos de Azure AD Multi-Factor Authentication para los usuarios, los administradores de autenticación pueden: 

1. Agregar métodos de autenticación para un usuario específico, incluidos los números de teléfono usados para MFA.
1. Restablezca la contraseña de un usuario. 
1. Requerir que un usuario vuelva a registrarse para MFA.
1. Revocar las sesiones de MFA existentes.
1. Eliminar todas las contraseñas de aplicación existentes del usuario.  

## <a name="add-authentication-methods-for-a-user"></a>Incorporación de métodos de autenticación para un usuario 

Puede agregar métodos de autenticación para un usuario a través de Azure Portal o Microsoft Graph.  

> [!NOTE]
> Por motivos de seguridad, los campos de información pública de contacto del usuario no deben usarse para realizar MFA. En su lugar, los usuarios deben rellenar los números del método de autenticación que se usarán para MFA.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Incorporación de los métodos de autenticación desde Azure Portal":::

Para agregar métodos de autenticación para un usuario mediante Azure Portal:  

1. Inicie sesión en **Azure Portal**. 
1. Diríjase a **Azure Active Directory** > **Usuarios** > **Todos los usuarios**. 
1. Elija el usuario para el que quiere agregar un método de autenticación y seleccione **Métodos de autenticación**.  
1. En la parte superior de la ventana, seleccione **+ Agregar método de autenticación**.
   1. Seleccione un método (número de teléfono o correo electrónico). El correo electrónico se puede usar para restablecer la contraseña automáticamente, pero no para la autenticación. Al agregar un número de teléfono, seleccione un tipo de teléfono y escriba un número de teléfono con un formato válido (por ejemplo, +1 4255551234).
   1. Seleccione **Agregar**.

> [!NOTE]
> La experiencia de versión preliminar permite a los administradores agregar cualquiera de los métodos de autenticación disponibles para los usuarios, mientras que la experiencia original solo permite actualizar los métodos de teléfono y teléfono alternativo.

### <a name="manage-methods-using-powershell"></a>Administración de métodos mediante PowerShell:  

Instale el módulo de PowerShell Microsoft.Graph.Identity.Signins con los siguientes comandos. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Enumere los métodos de autenticación basada en teléfono para un usuario específico.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Cree un método de autenticación mediante teléfono móvil para un usuario específico.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

Quite un método telefónico específico para un usuario.

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Los métodos de autenticación también se pueden administrar mediante las Microsoft Graph API. Puede encontrar más información en el documento [Introducción a la API de los métodos de autenticación de Azure AD.](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

## <a name="manage-user-authentication-options"></a>Administración de opciones de autenticación de usuarios

Si tiene asignado el rol de *administrador de autenticación*, puede requerir a los usuarios que restablezcan su contraseña, que vuelvan a registrarse para MFA o que revoquen las sesiones de MFA desde su objeto de usuario. Para administrar la configuración de usuario, complete los siguientes pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la parte izquierda, seleccione **Azure Active Directory** > **Usuarios** > **Todos los usuarios**.
1. Elija el usuario en el que quiere realizar una acción y seleccione **Métodos de autenticación**. En la parte superior de la ventana, elija una de las siguientes opciones para el usuario:
   - **Restablecer contraseña** restablece la contraseña del usuario y asigna una contraseña temporal que se debe cambiar en el siguiente inicio de sesión.
   - **Requerir volver a registrar MFA** hace que cuando el usuario inicie sesión la próxima vez, se le solicite que configure un nuevo método de autenticación MFA.
   
      > [!NOTE]
      > Los métodos de autenticación del usuario registrados actualmente no se eliminan cuando un administrador requiere volver a registrarse para MFA. Después de que un usuario se registre de nuevo en MFA, se recomienda revisar la información de seguridad y eliminar los métodos de autenticación previamente registrados que ya no se puedan usar.
   
   - **Revocar sesiones de MFA** borra las sesiones de MFA recordadas del usuario y le pide que realice MFA la próxima vez que la directiva lo exija en el dispositivo.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Administración de los métodos de autenticación desde Azure Portal":::

## <a name="delete-users-existing-app-passwords"></a>Eliminación de las contraseñas de aplicación existentes de los usuarios

En el caso de los usuarios que hayan definido contraseñas de aplicación, los administradores también pueden eliminar esas contraseñas, lo que provocará un error en la autenticación heredada en esas aplicaciones. Estas acciones pueden ser necesarias si necesita proporcionar asistencia a un usuario o necesita restablecer sus métodos de autenticación. Las aplicaciones sin explorador asociadas con estas contraseñas de aplicación dejarán de funcionar hasta que se cree una nueva contraseña de aplicación. 

Para eliminar las contraseñas de aplicación de un usuario, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la parte izquierda, seleccione **Azure Active Directory** > **Usuarios** > **Todos los usuarios**.
1. Seleccione **Multi-Factor Authentication**. Es posible que tenga que desplazarse hacia la derecha para ver esta opción de menú. Seleccione la siguiente captura de pantalla de ejemplo para ver la ventana de Azure Portal completa y la ubicación del menú: [![Selección de Multi-Factor Authentication desde la ventana Usuarios en Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Active la casilla junto al usuario o usuarios que desea administrar. Aparecerá una lista de opciones de paso rápido a la derecha.
1. Seleccione **Administrar configuración de usuario** y active la casilla **Eliminar todas las contraseñas de aplicación existentes generadas por los usuarios seleccionados**, tal como se muestra en el ejemplo siguiente: ![Eliminar todas contraseñas de aplicación existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Seleccione **Guardar** y luego **Cerrar**.

## <a name="next-steps"></a>Pasos siguientes

Este artículo le ha mostrado cómo configurar opciones de usuario individuales. Para configurar todas las opciones del servicio Azure AD Multi-Factor Authentication, consulte [Configuración de Azure AD Multi-Factor Authentication](howto-mfa-mfasettings.md).

Si los usuarios necesitan ayuda, consulte la [guía del usuario de Azure AD Multi-Factor Authentication](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc).