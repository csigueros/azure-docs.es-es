---
title: Habilitación de las opciones de aplicación de Angular mediante Azure Active Directory B2C
description: Puede habilitar el uso de las opciones de aplicación de Angular de varias maneras.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: c2b94a1a2659825314d45d9baeecaa11904dc527
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220719"
---
# <a name="configure-authentication-options-in-an-angular-application-by-using-azure-active-directory-b2c"></a>Configuración de las opciones de autenticación de una aplicación de Angular mediante Azure Active Directory B2C

En este artículo se describen métodos para personalizar y mejorar la experiencia de autenticación de Azure Active Directory B2C (Azure AD B2C) para una aplicación de página única (SPA) de Angular. Antes de empezar, familiarícese con el artículo [Configuración de la autenticación en una aplicación de página única de Angular](configure-authentication-sample-angular-spa-app.md) o [Habilitación de la autenticación en su propia aplicación de página única de Angular](enable-authentication-angular-spa-app.md).


## <a name="sign-in-and-sign-out-behavior"></a>Comportamiento de inicio de sesión y registro


Son dos las maneras en que puede configurar la aplicación de página única para que inicie la sesión de los usuarios con MSAL.js:

- **Ventana emergente**: la autenticación tiene lugar en una ventana emergente y se conserva el estado de la aplicación. Use este enfoque si no desea que los usuarios se muevan de la página de la aplicación durante la autenticación.  Tenga en cuenta que hay [problemas conocidos con las ventanas emergentes en Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups).
  - Para iniciar sesión con ventanas emergentes, en la clase `src/app/app.component.ts`, use el método `loginPopup`.  
  - En la clase `src/app/app.module.ts`, establezca el atributo `interactionType` en `InteractionType.Popup`.
  - Para cerrar la sesión con ventanas emergentes, en la clase `src/app/app.component.ts`, use el método `logoutPopup`. También puede configurar `logoutPopup` para redirigir la ventana principal a otra página, como la página principal o la página de inicio de sesión, una vez completado el cierre de sesión al pasar `mainWindowRedirectUri` como parte de la solicitud.
- **Redirección**: se redirige al usuario a Azure AD B2C para completar el flujo de autenticación. Use este enfoque si los usuarios tienen restricciones de explorador o directivas en las que las ventanas emergentes están deshabilitadas. 
  - Para iniciar sesión con la redirección, en la clase `src/app/app.component.ts`, use el método `loginRedirect`.  
  - En la clase `src/app/app.module.ts`, establezca el atributo `interactionType` en `InteractionType.Redirect`.
  - Para iniciar sesión con la redirección, en la clase `src/app/app.component.ts`, use el método `logoutRedirect`. Configure el URI de redirección tras el cierre de sesión mediante el establecimiento de `postLogoutRedirectUri`. Este URI se debe registrar como un URI de redirección en el registro de la aplicación.
  
En el ejemplo siguiente se muestra cómo iniciar y cerrar sesión:

#### <a name="pop-up"></a>[Elemento emergente](#tab/popup)


```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest);
  } else {
    this.authService.loginPopup();
  }
}

logout() { 
  this.authService.logoutPopup({
    mainWindowRedirectUri: '/',
  });
}
```

#### <a name="redirect"></a>[Redirect](#tab/redirect)

```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
  } else {
    this.authService.loginRedirect();
  }
}

logout() { 
  this.authService.logoutRedirect({
    postLogoutRedirectUri: 'http://localhost:4200'
  });
}
```

---

La biblioteca Angular de MSAL tiene tres flujos de inicio de sesión: inicio de sesión interactivo (donde un usuario selecciona el botón de inicio de sesión), MSAL Guard y MSAL Interceptor. Las configuraciones MSAL Guard y MSAL Interceptor tienen efecto cuando un usuario intenta acceder a un recurso protegido sin un token de acceso válido. En tales casos, la biblioteca MSAL obliga al usuario a iniciar sesión. 

En los ejemplos siguientes se muestra cómo configurar MSAL Guard y MSAL Interceptor para el inicio de sesión con una ventana emergente o una redirección: 

#### <a name="pop-up"></a>[Elemento emergente](#tab/popup)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Popup,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

#### <a name="redirect"></a>[Redirect](#tab/redirect)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Redirect,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

---  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si usa una directiva personalizada, agregue la notificación de entrada necesaria, como se describe en [Configuración del inicio de sesión directo](direct-signin.md#prepopulate-the-sign-in-name). 
1. Cree un objeto de configuración de MSAL `PopupRequest` o `RedirectRequest`, o utilice uno ya existente.
1. Establezca el atributo `loginHint` con la sugerencia de inicio de sesión correspondiente. 

Los fragmentos de código siguientes muestran cómo pasar el parámetro de sugerencia de inicio de sesión. Usan `bob@contoso.com` como valor de atributo.

#### <a name="pop-up"></a>[Elemento emergente](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

#### <a name="redirect"></a>[Redirect](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

---  


[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Compruebe el nombre de dominio del proveedor de identidades externo. Para más información, consulte [Redirección del inicio de sesión a un proveedor social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Cree un objeto de configuración de MSAL `PopupRequest` o `RedirectRequest`, o utilice uno ya existente.
1. Establezca el atributo `domainHint` con la sugerencia de dominio correspondiente.

Los fragmentos de código siguientes muestran cómo pasar el parámetro de sugerencia de dominio. Usan `facebook.com` como valor de atributo.

#### <a name="pop-up"></a>[Elemento emergente](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

#### <a name="redirect"></a>[Redirect](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

---  

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configure la personalización de idioma](language-customization.md). 
1. Cree un objeto de configuración de MSAL `PopupRequest` o `RedirectRequest`, o use uno ya existente, con los atributos `extraQueryParameters`.
1. Agregue el parámetro `ui_locales` con el código de idioma correspondiente a los atributos `extraQueryParameters`.

Los fragmentos de código siguientes muestran cómo pasar el parámetro de sugerencia de dominio. Usan `es-es` como valor de atributo.

#### <a name="pop-up"></a>[Elemento emergente](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[Redirect](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

---   
 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configure el elemento [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Cree un objeto de configuración de MSAL `PopupRequest` o `RedirectRequest`, o use uno ya existente, con los atributos `extraQueryParameters`.
1. Agregue el parámetro de cadena de consulta personalizado, por ejemplo, `campaignId`. Establezca el valor del parámetro. 

Los fragmentos de código siguientes muestran cómo pasar un parámetro de cadena de consulta personalizado. Usan `germany-promotion` como valor de atributo.

#### <a name="pop-up"></a>[Elemento emergente](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[Redirect](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"campaignId" : "germany-promotion"}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. En la directiva personalizada, defina el [perfil técnico de sugerencias de token de identificador](id-token-hint.md).
1. Cree un objeto de configuración de MSAL `PopupRequest` o `RedirectRequest`, o use uno ya existente, con los atributos `extraQueryParameters`.
1. Agregue el parámetro `id_token_hint` con la variable correspondiente que almacena el token de identificador.

Los fragmentos de código siguientes muestran cómo definir una sugerencia de token de identificador:

#### <a name="pop-up"></a>[Elemento emergente](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

#### <a name="redirect"></a>[Redirect](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};;

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Para usar el dominio personalizado con el identificador de inquilino en la dirección URL de autenticación, siga las instrucciones que se indican en [Habilitación de dominios personalizados](custom-domain.md). Abra el objeto de configuración de MSAL `src/app/auth-config.ts` y cambie `authorities` y `knownAuthorities` para usar su nombre de dominio personalizado y e identificador de inquilino.  

El código JavaScript siguiente muestra el objeto de configuración de MSAL antes del cambio: 

```typescript
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

El código JavaScript siguiente muestra el objeto de configuración de MSAL después del cambio: 

```typescript
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


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]

Para configurar el [registro](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/logging.md) de Angular, en *src/app/auth-config.ts*, configure las claves siguientes:

- `loggerCallback` es la función de devolución de llamada del registrador. 
- `logLevel` permite especificar el nivel de registro. Valores posibles: `Error`, `Warning`, `Info` y `Verbose`.
- `piiLoggingEnabled` habilita la entrada de datos personales. Valores posibles: `true` o `false`.
 
En el siguiente fragmento de código se muestra cómo configurar el registro de MSAL:

```typescript
export const msalConfig: Configuration = {
  ...
  system: {
    loggerOptions: {
        loggerCallback: (logLevel, message, containsPii) => {  
            console.log(message);
          },
          logLevel: LogLevel.Verbose,
          piiLoggingEnabled: false
      }
  }
  ...
}
```

## <a name="next-steps"></a>Pasos siguientes

- Aprenda más sobre las [opciones de configuración de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md).
