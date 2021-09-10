---
title: Habilitación de las opciones de una aplicación móvil Android mediante Azure Active Directory B2C
description: Habilite el uso de las opciones de una aplicación móvil Android de varias maneras.
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
ms.openlocfilehash: fc9e7a8dde4396933c244ad1d4b4a01d5003057c
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297995"
---
# <a name="configure-authentication-options-in-an-android-application-using-azure-active-directory-b2c"></a>Configuración de las opciones de autenticación de una aplicación Android mediante Azure Active Directory B2C 

En este artículo se describen métodos para personalizar y mejorar la experiencia de autenticación de Azure Active Directory B2C (Azure AD B2C) para una aplicación Android. Antes de empezar, familiarícese con los siguientes artículos: [Configuración de la autenticación en una aplicación Android de ejemplo](configure-authentication-sample-android-app.md) o [Habilitación de la autenticación en su propia aplicación Android mediante Azure Active Directory B2C](enable-authentication-android-app.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Para usar un dominio personalizado y el identificador de inquilino en la dirección URL de autenticación, siga las instrucciones que se indican en [Habilitación de dominios personalizados](custom-domain.md). Busque su objeto de configuración MSAL y cambie las **autoridades** con su nombre de dominio personalizado y su ID de inquilino.


#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

El código de Kotlin siguiente muestra el objeto de configuración de MSAL antes del cambio:

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```

El código de Kotlin siguiente muestra el objeto de configuración de MSAL después del cambio:

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```


#### <a name="java"></a>[Java](#tab/java)

El código de Kotlin siguiente muestra el objeto de configuración de MSAL antes del cambio:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```
El código de Kotlin siguiente muestra el objeto de configuración de MSAL después del cambio:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si usa una directiva personalizada, agregue la notificación de entrada necesaria como se describe en [Configuración del inicio de sesión directo](direct-signin.md#prepopulate-the-sign-in-name). 
1. Busque el objeto de configuración de MSAL y agregue el método **withLoginHint()** con la sugerencia de inicio de sesión.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withLoginHint("bob@contoso.com") 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withLoginHint("bob@contoso.com")  
    // More settings here
    .build();

b2cApp.acquireToken(parameters);

```

--- 

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Compruebe el nombre de dominio del proveedor de identidades externo. Para más información, consulte [Redirección del inicio de sesión a un proveedor social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Cree o use un objeto de lista de salida para almacenar parámetros de consulta adicionales.
1. Agregue al diccionario el parámetro `domain_hint` con el nombre de dominio correspondiente a la lista. Por ejemplo, `facebook.com`.
1. Pase la lista de parámetros de consulta adicionales al método `withAuthorizationQueryStringParameters` del objeto de configuración de MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("domain_hint", "facebook.com"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("domain_hint", "facebook.com"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configure la personalización de idioma](language-customization.md).
1. Cree o use un objeto de lista de salida para almacenar parámetros de consulta adicionales.
1. Agregue a la lista el parámetro `ui_locales` con el código de idioma correspondiente. Por ejemplo, `en-us`.
1. Pase la lista de parámetros de consulta adicionales al método `withAuthorizationQueryStringParameters` del objeto de configuración de MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("ui_locales", "en-us"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("ui_locales", "en-us"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configure el elemento [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Cree o use un objeto de lista de salida para almacenar parámetros de consulta adicionales.
1. Agregue el parámetro de cadena de consulta personalizado, como `campaignId`. Establezca el valor del parámetro. Por ejemplo, `germany-promotion`.
1. Pase la lista de parámetros de consulta adicionales al método `withAuthorizationQueryStringParameters` del objeto de configuración de MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("campaignId", "germany-promotion"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("campaignId", "germany-promotion"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. En la directiva personalizada, defina un [perfil técnico de sugerencias de token de identificador](id-token-hint.md).
1. En el código, genere o adquiera un token de identificador y establezca el token en una variable. Por ejemplo, `idToken`. 
1. Cree o use un objeto de lista de salida para almacenar parámetros de consulta adicionales.
1. Agregue el parámetro `id_token_hint` con la variable correspondiente que almacena el token de identificador.
1. Pase la lista de parámetros de consulta adicionales al método `withAuthorizationQueryStringParameters` del objeto de configuración de MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("id_token_hint", idToken))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("id_token_hint", idToken));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 



## <a name="next-steps"></a>Pasos siguientes

- Más información: [Opciones de configuración de MSAL para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)
