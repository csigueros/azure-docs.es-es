---
title: Habilitación de las opciones de aplicaciones SPA mediante Azure Active Directory B2C
description: En este artículo se abordan varias maneras de habilitar el uso de aplicaciones SPA.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 675db9c9d0a21b805ce8b4edeaff1a3b29ae7bfd
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122068252"
---
# <a name="configure-authentication-options-in-a-single-page-application-by-using-azure-ad-b2c"></a>Configuración de las opciones de autenticación en una aplicación de página única mediante Azure AD B2C

En este artículo se describe cómo personalizar y mejorar la experiencia de autenticación de Azure Active Directory B2C (Azure AD B2C) para una aplicación de página única (SPA). 

Antes de empezar, familiarícese con el artículo [Configuración de la autenticación en una aplicación web de ejemplo](configure-authentication-sample-spa-app.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Para usar un dominio personalizado y el identificador de inquilino en la dirección URL de autenticación, siga las instrucciones que se indican en [Habilitación de dominios personalizados](custom-domain.md). Busque el objeto de configuración Biblioteca de autenticación de Microsoft (MSAL) y cambie *authorities* y *knownAuthorities* para usar el nombre de dominio y el identificador de inquilino personalizados.

El código JavaScript siguiente muestra el objeto de configuración MSAL *antes* del cambio: 

```Javascript
const msalConfig = {
    auth: {
      ...
      authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_susi",
      knownAuthorities: ["fabrikamb2c.b2clogin.com"],
      ...
    },
  ...
}
```  

El código JavaScript siguiente muestra el objeto de configuración MSAL *después* del cambio: 

```Javascript
const msalConfig = {
    auth: {
      ...
      authority: "https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi",
      knownAuthorities: ["custom.domain.com"],
      ...
    },
  ...
}
```  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si usa una directiva personalizada, agregue la notificación de entrada necesaria, como se describe en [Configuración del inicio de sesión directo](direct-signin.md#prepopulate-the-sign-in-name). 
1. Cree un objeto para almacenar el elemento **login_hint** y pase este objeto al método **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
        loginHint: "bob@contoso.com"
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Compruebe el nombre de dominio del proveedor de identidades externo. Para más información, consulte [Redirección del inicio de sesión a un proveedor social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Cree un objeto para almacenar el elemento **extraQueryParameters** y pase este objeto al método **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
         extraQueryParameters: {domain_hint: 'facebook.com'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configure la personalización de idioma](language-customization.md).
1. Cree un objeto para almacenar el elemento **extraQueryParameters** y pase este objeto al método **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
         extraQueryParameters: {ui_locales: 'en-us'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configure el elemento [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Cree un objeto para almacenar el elemento **extraQueryParameters** y pase este objeto al método **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
         extraQueryParameters: {campaignId: 'germany-promotion'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. En la directiva personalizada, defina un [perfil técnico de sugerencias de token de identificador](id-token-hint.md).
1. Cree un objeto para almacenar el elemento **extraQueryParameters** y pase este objeto al método **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
         extraQueryParameters: {id_token_hint: 'id-token-hint-value'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

## <a name="secure-your-logout-redirect"></a>Protección de la redirección de cierre de sesión

Después del cierre de sesión, se redirige al usuario al URI especificado en el parámetro `post_logout_redirect_uri`, independientemente de las direcciones URL de respuesta que se hayan especificado para la aplicación. Sin embargo, si se pasa un valor de `id_token_hint` válido y la opción [Requerir token de identificador en solicitudes de cierre de sesión](session-behavior.md#secure-your-logout-redirect) está activada, Azure AD B2C comprueba que el valor de `post_logout_redirect_uri` coincida con uno de los URI de redirección configurados de la aplicación antes de realizar la redirección. Si no se configuró ninguna dirección URL de respuesta coincidente para la aplicación, se muestra un mensaje de error y no se redirige al usuario.

Para admitir un URI de redireccionamiento de cierre de sesión protegido, siga estos pasos:

1. Cree una variable accesible globalmente para almacenar el valor de `id_token`.
    ```javascript
    let id_token = "";
    ```
    
1. En la función `handleResponse` de MSAL, analice el valor `id_token` de objeto `authenticationResult` en la variable `id_token`.
    ```javascript
    function handleResponse(response) {
        if (response !== null) {
            setAccount(response.account);
            id_token = response.idToken;
        } else {
            selectAccount();
        }
    }
    ```
    
1. En la función `signOut`, agregue el parámetro `id_token_hint` al objeto **logoutRequest**.
    ```javascript
    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
            //set id_token_hint to the id_token value
            idTokenHint : id_token,
            mainWindowRedirectUri: msalConfig.auth.redirectUri
        };
        myMSALObj.logoutPopup(logoutRequest);
    }
    ```

En el ejemplo anterior, el elemento **post_logout_redirect_uri** pasado a la solicitud de cierre de sesión tendrá el formato `https://your-app.com/`. Esta dirección URL debe agregarse a la dirección URL de respuesta del registro de aplicación.

## <a name="enable-single-logout"></a>Deshabilitación del cierre de sesión único

El cierre de sesión único de Azure AD B2C usa el cierre de sesión del canal frontal de OpenId Connect para realizar solicitudes de cierre de sesión a todas las aplicaciones en las que el usuario ha iniciado sesión mediante Azure AD B2C.

Estas solicitudes de cierre de sesión se realizan desde la página de cierre de sesión de Azure AD B2C, en un Iframe oculto. Los Iframes hacen solicitudes HTTP a todos los puntos de conexión de cierre de sesión del canal frontal registrados para las aplicaciones que Azure AD B2C ha registrado como iniciadas. 

El punto de conexión de cierre de sesión de cada aplicación debe llamar al método **MSAL logout()** . También debe configurar explícitamente MSAL para que se ejecute dentro de un Iframe en este escenario estableciendo `allowRedirectInIframe` en `true`.

El ejemplo de código siguiente establece `allowRedirectInIframe` en `true`:

```javascript
const msalConfig = {
    auth: {
        clientId: "enter_client_id_here",
        .....
    },
    cache: {
        cacheLocation: "..",
        ....
    },
    system: {
        allowRedirectInIframe: true
    };
}

async function logoutSilent(MSAL) {
   return MSAL.logout({
      onRedirectNavigate: (url) => {
         return false;
       }
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda más sobre las [opciones de configuración de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md).
