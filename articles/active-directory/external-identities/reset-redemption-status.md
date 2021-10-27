---
title: 'Restablecimiento del estado de canje de un usuario invitado: Azure AD'
description: Obtenga información sobre cómo restablecer el estado de canje de invitación para un usuario invitado de Azure Active Directory B2B en Azure AD External Identities.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e19c4e6e13f4abcb8143f789dc1a7aa8de47e82
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129998967"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Restablecimiento del estado de canje para un usuario invitado (versión preliminar)

Una vez que un usuario invitado ha canjeado su invitación para la colaboración B2B, puede haber ocasiones en las que tenga que actualizar la información de inicio de sesión, por ejemplo, cuando:

- El usuario quiera iniciar sesión con otro correo electrónico y proveedor de identidades.
- Se haya eliminado y vuelto a crear la cuenta del usuario en su inquilino principal.
- El usuario esté trabajando en otra empresa, pero todavía necesite el mismo acceso a los recursos.
- Las responsabilidades del usuario se hayan pasado a otro usuario.

Para administrar estos escenarios, anteriormente tenía que eliminar manualmente la cuenta del usuario invitado de su directorio y volver a invitar al usuario. Ahora puede usar PowerShell o la API de invitación de Microsoft Graph para restablecer el estado de canje del usuario y volver a invitarlo, a la vez que conserva el identificador de objeto del usuario, las pertenencias a grupos y las asignaciones de aplicaciones. Cuando el usuario canjea la invitación nueva, el UPN del usuario no cambia, aunque su nombre de inicio de sesión se cambia al nuevo correo electrónico. A continuación, el usuario puede iniciar sesión con el nuevo correo electrónico o con un correo electrónico que se haya agregado a la propiedad `otherMails` del objeto de usuario.

## <a name="use-the-azure-portal-to-reset-redemption-status"></a>Use Azure Portal para restablecer el estado de canje.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con una cuenta de Administrador global o de administrador de usuarios para el directorio.
1. Busque y seleccione **Azure Active Directory**.
1. Seleccione **Usuarios**.
1. En la lista, seleccione el nombre del usuario para abrir el perfil del usuario.
1. Si el usuario quiere iniciar sesión con otro correo electrónico:
   - Seleccione el icono **Editar** en la parte superior de la página.
   - En la sección **Información de contacto**, en **Correo electrónico**, escriba el nuevo correo electrónico.
   - Junto a **Correo electrónico alternativo**, seleccione **Editar**. Actualice el correo electrónico alternativo en la lista con el nuevo correo electrónico y, a continuación, seleccione **Actualizar**.
   - Seleccione el icono **Guardar** en la parte superior de la página.
1. En la sección **Identidad**, en **Invitación aceptada**, seleccione **(administrar)** .
1. En **Estado de canje**, junto a **¿Restablecer estado de invitación? (versión preliminar)** , seleccione **Sí**.
1. Seleccione **Sí** para confirmar la acción.


## <a name="use-powershell-or-microsoft-graph-api-to-reset-redemption-status"></a>Uso de PowerShell o Microsoft Graph API para restablecer el estado de canje

### <a name="reset-the-email-address-used-for-sign-in"></a>Restablecer la dirección de correo electrónico utilizada para el inicio de sesión

Si un usuario quiere iniciar sesión con otro correo electrónico:

1. Asegúrese de que la nueva dirección de correo electrónico se agrega a la propiedad del objeto de usuario`mail` o `otherMails`. 
1.  Reemplace la dirección de correo electrónico en la propiedad `InvitedUserEmailAddress` por la nueva dirección de correo electrónico.
1. Use uno de los métodos siguientes para restablecer el estado de canje del usuario.

> [!NOTE]
>Durante la versión preliminar pública, tenemos dos recomendaciones:
>- Cuando vuelva a establecer la dirección de correo electrónico del usuario en una nueva, se recomienda establecer la propiedad `mail`. De este modo, el usuario puede canjear la invitación iniciando sesión en el directorio, además de usar el vínculo de canje en la invitación.
>- Cuando vuelva a establecer el estado de un usuario invitado B2B, asegúrese de hacerlo en el contexto del usuario. Actualmente no se admiten las llamadas solo de la aplicación.
>
### <a name="use-powershell-to-reset-redemption-status"></a>Uso de PowerShell para restablecer el estado de canje

Instale el módulo AzureADPreview de PowerShell más reciente y cree una nueva invitación con `InvitedUserEmailAddress` establecido en la nueva dirección de correo electrónico, y `ResetRedemption` establecido en `true`.

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

### <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Uso de Microsoft Graph API para restablecer el estado de canje

Con la [API de invitación de Microsoft Graph](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true), establezca la propiedad `resetRedemption` en `true` y especifique la nueva dirección de correo electrónico en la propiedad `invitedUserEmailAddress`.

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de usuarios de colaboración B2B de Azure Active Directory con PowerShell](customize-invitation-api.md#powershell)
- [Propiedades de un usuario invitado de Azure AD B2B](user-properties.md)
