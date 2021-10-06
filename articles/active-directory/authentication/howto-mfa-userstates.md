---
title: 'Habilitación de Multi-Factor Authentication por usuario: Azure Active Directory'
description: Aprenda a habilitar Azure AD Multi-Factor Authentication por usuario mediante el cambio del estado del usuario.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 066e0a48442825785bc2c413c0e7d40ca470f45d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353233"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Habilitación de Azure AD Multi-Factor Authentication por usuario para proteger los eventos de inicio de sesión

Para proteger los eventos de inicio de sesión de usuario en Azure AD, puede ser necesaria la autenticación multifactor (MFA). La habilitación de Azure AD Multi-Factor Authentication mediante directivas de acceso condicional es el enfoque recomendado. El acceso condicional es una característica de Azure AD Premium P1 o P2 que permite aplicar reglas para exigir MFA según sea necesario en determinados escenarios. Para empezar a usar el acceso condicional, consulte [Tutorial: Protección de los eventos de inicio de sesión de usuario mediante Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

En el caso de inquilinos gratuitos de Azure AD sin acceso condicional, puede [usar valores predeterminados de seguridad para proteger a los usuarios](../fundamentals/concept-fundamentals-security-defaults.md). A los usuarios se les solicita MFA según sea necesario, pero no puede definir sus propias reglas para controlar el comportamiento.

En cambio, si es necesario, puede habilitar cada cuenta para Azure AD Multi-Factor Authentication por usuario. Cuando los usuarios están habilitados de forma individual, realizan la autenticación multifactor cada vez que inician sesión (con algunas excepciones, como cuando inician sesión desde direcciones IP de confianza o si la característica para _recordar MFA en dispositivos de confianza_) está activada.

No se recomienda cambiar los [estados del usuario](#azure-ad-multi-factor-authentication-user-states) a menos que las licencias de Azure AD no incluyan acceso condicional y no quiera usar los valores predeterminados de seguridad. Para más información sobre las distintas formas de habilitar MFA, consulte [Características y licencias de Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> En este artículo se detalla cómo ver y cambiar el estado de Azure AD Multi-Factor Authentication por usuario. Si usa el acceso condicional o los valores predeterminados de seguridad, no revise ni habilite las cuentas de usuario mediante estos pasos.
>
> Al habilitar Azure AD Multi-Factor Authentication mediante una directiva de acceso condicional, no se cambia el estado del usuario. No se alarme si los usuarios aparecen deshabilitados. El acceso condicional no cambia el estado.
>
> **No habilite ni aplique Azure AD Multi-Factor Authentication por usuario si usa directivas de acceso condicional.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Estados de usuario de Azure AD Multi-Factor Authentication

El estado de un usuario refleja si un administrador lo ha inscrito en Azure AD Multi-Factor Authentication por usuario. Las cuentas de usuario de Azure AD Multi-Factor Authentication tienen los siguientes tres estados:

| State | Descripción | Autenticación heredada afectada | Aplicaciones que son de explorador afectadas | Autenticación moderna afectada |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Estado predeterminado de un usuario no inscrito en Azure AD Multi-Factor Authentication por usuario. | No | No | No |
| habilitado | El usuario está inscrito en Azure AD Multi-Factor Authentication por usuario, pero puede seguir usando su contraseña en la autenticación heredada. Si el usuario todavía no ha registrado métodos de autenticación MFA, recibirá un aviso para que lo haga la próxima vez que inicie sesión con la autenticación moderna (por ejemplo, a través de un explorador web). | No. La autenticación heredada sigue funcionando hasta que se completa el proceso de registro. | Sí. Una vez que expire la sesión, se requerirá el registro en Azure AD Multi-Factor Authentication.| Sí. Una vez que expire el token de acceso, se requerirá el registro en Azure AD Multi-Factor Authentication. |
| Aplicado | El usuario está inscrito en Azure AD Multi-Factor Authentication por usuario. Si el usuario aún no ha registrado métodos de autenticación, recibirá un aviso para que lo haga la próxima vez que inicie sesión con la autenticación moderna (por ejemplo, a través de un explorador web). Los usuarios que completen el registro mientras están en el estado *Habilitado* pasan automáticamente al estado *Enforced* (Aplicado). | Sí. Las aplicaciones requieren contraseñas de aplicación. | Sí. Se requiere Azure AD Multi-Factor Authentication en el inicio de sesión. | Sí. Se requiere Azure AD Multi-Factor Authentication en el inicio de sesión. |

Todos los usuarios comienzan con el estado *Deshabilitado*. Cuando se inscribe a los usuarios en Azure AD Multi-Factor Authentication por usuario, su estado cambia a *Habilitado*. Cuando los usuarios habilitados inician sesión y completan el proceso de registro, el estado cambia a *Aplicado*. Los administradores pueden mover a los usuarios entre estados, por ejemplo, de *Enforced* (Aplicado) a *Habilitado* o a *Deshabilitado*.

> [!NOTE]
> Si MFP por usuario se vuelve a habilitar en un usuario y este no se registra de nuevo, su estado de MFA no pasa de *Habilitado* a *Enforced* (Aplicado) en la interfaz de usuario de administración de MFA. El administrador debe mover al usuario directamente a *Enfoced* (Aplicado).

## <a name="view-the-status-for-a-user"></a>Ver el estado de un usuario

Para ver y administrar estados de usuario, haga lo siguiente para acceder a la página de Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
1. Busque y seleccione *Azure Active Directory* y, a continuación, seleccione **Usuarios** > **Todos los usuarios**.
1. Seleccione **Multi-Factor Authentication**. Es posible que tenga que desplazarse hacia la derecha para ver esta opción de menú. Seleccione la siguiente captura de pantalla de ejemplo para ver la ventana de Azure Portal completa y la ubicación del menú: [![Selección de Multi-Factor Authentication desde la ventana Usuarios en Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Se abre una nueva página que muestra el estado del usuario, tal y como se muestra en el ejemplo siguiente.
   ![Captura de pantalla que muestra información de ejemplo del estado del usuario de Azure AD Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Cambiar el estado de un usuario

Para cambiar el estado de Azure AD Multi-Factor Authentication por usuario de un usuario, realice los pasos siguientes:

1. Siga los pasos anteriores para acceder a la página **Usuarios** de Azure AD Multi-Factor Authentication y [ver el estado de un usuario](#view-the-status-for-a-user).
1. Busque el usuario para el que quiere habilitar Azure AD Multi-Factor Authentication. Puede que necesite cambiar la vista en la parte superior a **usuarios**.
   ![Seleccione el usuario para cambiar su estado desde la pestaña de usuarios](./media/howto-mfa-userstates/enable1.png)
1. Active la casilla situada junto a los nombres de los usuarios a los que desea cambiar el estado.
1. En el lado derecho, en **Pasos rápidos**, elija **Habilitar** o **Deshabilitar**. En el ejemplo siguiente, el usuario *John Smith* tiene una casilla junto a su nombre y se va a habilitar para su uso: ![Habilite el usuario seleccionado haciendo clic en Habilitar en el menú de pasos rápidos](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Los usuarios con estado *Habilitado* cambian automáticamente a *Aplicado* cuando se registran en Azure AD Multi-Factor Authentication. No cambie manualmente el estado del usuario a *Enforced* (Aplicado) a menos que el usuario ya esté registrado o si es admisible que experimente interrupciones en las conexiones a protocolos de autenticación heredados.

1. Confirme la selección en la ventana emergente que se abre.

Después de habilitar los usuarios, notifíquelos por correo electrónico. Indique a los usuarios que aparecerá un mensaje para solicitarles que se registren la próxima vez que inicien sesión. Además, si su organización utiliza aplicaciones sin explorador que no son compatibles con la autenticación moderna, deben crear contraseñas de aplicación. Para más información, consulte la [guía del usuario final de Azure AD Multi-Factor Authentication](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc) para ayudarle a empezar.

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertir a los usuarios de MFA por usuario a MFA basado en el acceso condicional

Si los usuarios se han habilitado con Azure AD Multi-Factor Authentication por usuario habilitado y aplicado, el siguiente comando de PowerShell puede ayudarle a realizar la conversión a Azure AD Multi-Factor Authentication basado en acceso condicional.

Ejecútelo en una ventana de ISE o guárdelo como un archivo `.PS1` para ejecutarlo localmente. La operación solo se puede realizar mediante el [módulo MSOnline](/powershell/module/msonline#msonline). 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="next-steps"></a>Pasos siguientes

Para configurar las opciones de Azure AD Multi-Factor Authentication, consulte [Configuración de Azure AD Multi-Factor Authentication](howto-mfa-mfasettings.md).

Para administrar la configuración del usuario de Azure AD Multi-factor Authentication, consulte [Administración de la configuración del usuario con Azure AD Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Para saber por qué se ha solicitado o no a un usuario que realice MFA, consulte [Informes de Azure AD Multi-Factor Authentication](howto-mfa-reporting.md).