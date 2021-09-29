---
title: 'Habilitación de la autenticación en una aplicación Android: Azure AD B2C'
description: Habilite la autenticación en una aplicación Android mediante los bloques de creación de Azure Active Directory B2C. Obtenga información sobre cómo usar Azure AD B2C para iniciar sesión y registrar usuarios en una aplicación Android.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/16/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support, has-adal-ref
ms.openlocfilehash: 4dca649048a393adaba58614ab8f63dec5db29e2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575178"
---
# <a name="enable-authentication-in-your-own-android-app-by-using-azure-ad-b2c"></a>Habilitación de la autenticación en una aplicación de Android propia mediante Azure AD B2C

En este artículo se muestra cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a su propia aplicación móvil Android. 

Use este artículo con [Configuración de la autenticación en una aplicación Android de ejemplo mediante Azure AD B2C](./configure-authentication-sample-android-app.md) y sustituya la aplicación Android de ejemplo por la suya propia. Después de completar las instrucciones de este artículo, la aplicación aceptará inicios de sesión mediante Azure AD B2C.

## <a name="prerequisites"></a>Requisitos previos

Revise los requisitos previos y las instrucciones de integración de [Configuración de la autenticación en una aplicación Android de ejemplo mediante Azure AD B2C](configure-authentication-sample-android-app.md).

## <a name="create-an-android-app-project"></a>Creación de un proyecto de aplicación Android

Si aún no tiene una aplicación Android, haga lo siguiente para configurar un nuevo proyecto:

1. En Android Studio, seleccione **Start a new Android Studio project** (Iniciar un proyecto de Android Studio).
1. Seleccione **Basic Activity** (Actividad básica) y, después, **Siguiente**.
1. Asigne un nombre a la aplicación.
1. Guarde el nombre del paquete. Deberá escribirlo más tarde en Azure Portal.
1. Cambie el lenguaje de **Kotlin** a **Java**.
1. Establezca el **nivel de API mínimo** en **API 19** o superior y seleccione **Finalizar**.
1. En la vista de proyecto, seleccione **Proyecto** en la lista desplegable para mostrar los archivos de proyecto de origen y que no sean de origen, abra **app/build.gradle** y, después, establezca **targetSdkVersion** en **28**.

## <a name="step-1-install-the-dependencies"></a>Paso 1: Instalación de las dependencias

En la ventana del proyecto de Android Studio, vaya a **app** > **build.gradle** y, después, agregue lo siguiente:

```gradle
apply plugin: 'com.android.application'

allprojects {
    repositories {
    mavenCentral()
    google()
    mavenLocal()
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    maven {
        name "vsts-maven-adal-android"
        url "https://identitydivision.pkgs.visualstudio.com/_packaging/AndroidADAL/maven/v1"
        credentials {
            username System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") : project.findProperty("vstsUsername")
            password System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") : project.findProperty("vstsMavenAccessToken")
        }
    }
    jcenter()
    }
}
dependencies{
    implementation 'com.microsoft.identity.client:msal:2.+'
    }
packagingOptions{
    exclude("META-INF/jersey-module-version")
}
```


## <a name="step-2-add-the-authentication-components"></a>Paso 2: Adición de componentes de autenticación

El [código de ejemplo](configure-authentication-sample-android-app.md#step-3-get-the-android-mobile-app-sample) consta de los siguientes componentes. Agregue estos componentes de la aplicación Android de ejemplo a su propia aplicación. 

|Componente  |Tipo  | Source |Descripción  |
|---------|---------|---------|---------|
| B2CUser| Clase| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CUser.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CUser.java)| Representa un usuario B2C. Esta clase permite a los usuarios iniciar sesión con varias directivas. | 
| B2CModeFragment | Clase de fragmento| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CModeFragment.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CModeFragment.java)| Un fragmento representa una parte modular del inicio de sesión con la interfaz de usuario de Azure AD B2C dentro de la actividad principal. Este fragmento contiene la mayor parte del código de autenticación. |
| fragment_b2c_mode.xml | Diseño de fragmentos| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) | Define la estructura de una interfaz de usuario para el componente de fragmento B2CModeFragment. |
| B2CConfiguration| Clase| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CConfiguration.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CConfiguration.java)| Un archivo de configuración contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación móvil usa esta información para establecer una relación de confianza con Azure AD B2C, iniciar y cerrar la sesión de los usuarios y obtener tokens y validarlos. Para más opciones de configuración, consulte el archivo auth_config_b2c.json.  | 
|auth_config_b2c.json | Archivo JSON| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/raw/auth_config_b2c.json) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/raw/auth_config_b2c.json)| Un archivo de configuración contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación móvil usa esta información para establecer una relación de confianza con Azure AD B2C, iniciar y cerrar la sesión de los usuarios y obtener tokens y validarlos. Para obtener más opciones de configuración, vea la clase B2CConfiguration. |
| | | 

## <a name="step-3-configure-your-android-app"></a>Paso 3: Configuración de su aplicación para Android

Después de [agregar los componentes de autenticación](#step-2-add-the-authentication-components), configure la aplicación Android con los ajustes de Azure AD B2C. Los ajustes del proveedor de identidad de Azure AD B2C se configuran en el archivo *auth_config_b2c.json* y en la clase B2CConfiguration. 

Para obtener más información, consulte [Configuración de la aplicación móvil de ejemplo](configure-authentication-sample-android-app.md#step-5-configure-the-sample-mobile-app).

## <a name="step-4-set-the-redirect-uri"></a>Paso 4: Establecimiento del URI de redireccionamiento

Configure dónde escucha su aplicación la respuesta del token de Azure AD B2C. 

1. Genere un nuevo hash de firma de desarrollo. Esto cambiará para cada entorno de desarrollo.
    
    Para Windows:
    
    ```
    keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    Para iOS:
    
    ```dotnetcli
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    Para un entorno de producción, use el siguiente comando:
    
    ```
    keytool -exportcert -alias SIGNATURE_ALIAS -keystore PATH_TO_KEYSTORE | openssl sha1 -binary | openssl base64
    ```

    Para obtener más ayuda con la firma de sus aplicaciones, consulte [Firma de su aplicación Android](https://developer.android.com/studio/publish/app-signing). 

1. Seleccione **app** > **src** > **main** > **AndroidManifest.xml** y, después, agregue la siguiente actividad `BrowserTabActivity`al cuerpo de la aplicación:
    
    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Package_Name"
                android:path="/Signature_Hash" />
        </intent-filter>
    </activity>
    ```
1. Sustituya `Signature_Hash` por el hash que ha generado.
1. Sustituya `Package_Name` por el nombre de su paquete Android.
 
Para actualizar el registro de la aplicación móvil con el URI de redireccionamiento de la aplicación, haga lo siguiente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y, después, seleccione la aplicación que registró en el [Paso 2.3: Registro de la aplicación móvil](configure-authentication-sample-android-app.md#step-23-register-the-mobile-app).
1. Seleccione **Autenticación**.
1. En **Android**, seleccione **Agregar URI**.
1. Escriba el **Nombre del paquete** y el **Hash de firma**.
1. Seleccione **Guardar**.

El URI de redirección y la actividad `BrowserTabActivity` deben ser similares al ejemplo siguiente: 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

La dirección URL de redireccionamiento del ejemplo de Android se ve así:

```
msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

El filtro de intención usa el mismo patrón, como se muestra en el siguiente fragmento XML:

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data
            android:host="com.azuresamples.msalandroidkotlinapp"
            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
            android:scheme="msauth" />
    </intent-filter>
</activity>
```



#### <a name="java"></a>[Java](#tab/java)

La dirección URL de redireccionamiento del ejemplo de Android se ve así:

```
msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

El filtro de intención usa el mismo patrón, como se muestra en el siguiente fragmento XML:

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
<intent-filter>
    <action android:name="android.intent.action.VIEW" />

    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />

    <data
        android:host="com.azuresamples.msalandroidapp"
        android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
        android:scheme="msauth" />
</intent-filter>
</activity>
```

--- 

## <a name="step-5-customize-your-code-building-blocks"></a>Paso 5: Personalización de los bloques de compilación de código

En esta sección se describen los bloques de compilación de código que habilitan la autenticación para la aplicación Android. En la tabla siguiente se enumeran los métodos de B2CModeFragment y se explica cómo personalizar el código. 

### <a name="step-51-instantiate-a-public-client-application"></a>Paso 5.1: Creación de instancias de una aplicación cliente pública

Las aplicaciones cliente públicas no son de confianza para mantener los secretos de aplicación de forma segura y no tienen secretos de cliente. En [onCreate](https://developer.android.com/reference/android/app/Fragment#onCreate(android.os.Bundle)) u [onCreateView](https://developer.android.com/reference/android/app/Fragment#onCreateView(android.view.LayoutInflater,%20android.view.ViewGroup,%20android.os.Bundle)), cree una instancia de MSAL mediante el objeto de aplicación de cliente pública de varias cuentas.

La clase `MultipleAccountPublicClientApplication` se usa para crear aplicaciones basadas en MSAL que permitan que varias cuentas inicien sesión al mismo tiempo. La clase permite el inicio de sesión con varios flujos de usuario de Azure AD B2C o directivas personalizadas. Por ejemplo, los usuarios inician sesión con un flujo de usuario de [registro o inicio de sesión](add-sign-up-and-sign-in-policy.md) y, posteriormente, ejecutan un flujo de usuario de [edición de perfil](add-profile-editing-policy.md). 

El siguiente fragmento de código muestra cómo inicializar la biblioteca MSAL con un archivo JSON de configuración `auth_config_b2c.json`.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
PublicClientApplication.createMultipleAccountPublicClientApplication(context!!,
    R.raw.auth_config_b2c,
    object : IMultipleAccountApplicationCreatedListener {
        override fun onCreated(application: IMultipleAccountPublicClientApplication) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application
            // Load the account (if there is any)
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            // Error handling
            displayError(exception)
        }
    })
```

#### <a name="java"></a>[Java](#tab/java)

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
    R.raw.auth_config_b2c,
    new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication application) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application;

            // Load the account (if there is any)
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            // Error handling
            displayError(exception);
        }
    });
```

--- 

### <a name="step-52-load-accounts"></a>Paso 5.2: Carga de cuentas

Cuando la aplicación llega al primer plano, carga la cuenta existente para determinar si los usuarios han iniciado sesión. Use este método para actualizar la interfaz de usuario con el estado de autenticación. Por ejemplo, puede habilitar o deshabilitar el botón de cierre de sesión.

El siguiente fragmento de código muestra cómo cargar las cuentas.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private fun loadAccounts() {
    if (b2cApp == null) {
        return
    }
    b2cApp!!.getAccounts(object : LoadAccountsCallback {
        override fun onTaskCompleted(result: List<IAccount>) {
            users = B2CUser.getB2CUsersFromAccountList(result)
            updateUI(users)
        }
    
        override fun onError(exception: MsalException) {
            displayError(exception)
        }
    })
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private void loadAccounts() {
    if (b2cApp == null) {
        return;
    }

    b2cApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            users = B2CUser.getB2CUsersFromAccountList(result);
            updateUI(users);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

--- 

### <a name="step-53-start-an-interactive-authorization-request"></a>Paso 5.3: Inicio de una solicitud de autorización interactiva

Una solicitud de autorización interactiva es un flujo en el que se solicita a los usuarios que se registren o inicien sesión. El método `initializeUI` configura el evento de clic `runUserFlowButton`. Cuando los usuarios seleccionan el botón **Ejecutar flujo de usuario**, la aplicación los lleva a Azure AD B2C para completar el flujo de inicio de sesión. 

El método `runUserFlowButton.setOnClickListener` prepara el objeto `AcquireTokenParameters` con datos relevantes sobre la solicitud de autorización. Después, el método `acquireToken` solicita a los usuarios que completen el flujo de registro o de inicio de sesión. 

El siguiente fragmento de código muestra cómo iniciar la solicitud de autorización interactiva: 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority(getAuthorityFromPolicyName(policy_list.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.scopes)
        .withPrompt(Prompt.LOGIN)
        .withCallback(authInteractiveCallback)
        .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(getActivity())
        .fromAuthority(B2CConfiguration.getAuthorityFromPolicyName(policyListSpinner.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.getScopes())
        .withPrompt(Prompt.LOGIN)
        .withCallback(getAuthInteractiveCallback())
        .build();

b2cApp.acquireToken(parameters);
```

--- 

 
### <a name="step-54-make-an-interactive-authorization-request-callback"></a>Paso 5.4: Llamada de solicitud de autorización interactiva

Una vez que los usuarios finalizan el flujo de autorización, ya se de forma correcta o incorrecta, el resultado se devuelve al método de devolución de llamada `getAuthInteractiveCallback()`. 

El método de devolución de llamada pasa el objeto `AuthenticationResult`, o un mensaje de error en el objeto `MsalException`. Use este método para:

- Actualizar la interfaz de usuario de la aplicación móvil con información una vez terminado el inicio de sesión.
- Recargar el objeto de cuentas.
- Llame a un servicio de API web con un token de acceso.
- Controlar errores de autenticación.

El siguiente fragmento de código muestra el uso de la devolución de llamada de autenticación interactiva.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private val authInteractiveCallback: AuthenticationCallback
    private get() = object : AuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            /* Successfully got a token, use it to call a protected resource; web API  */
            Log.d(TAG, "Successfully authenticated")

            /* display result info */
            displayResult(authenticationResult)

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            val B2C_PASSWORD_CHANGE = "AADB2C90118"
            if (exception.message!!.contains(B2C_PASSWORD_CHANGE)) {
                txt_log!!.text = """
                    Users click the 'Forgot Password' link in a sign-up or sign-in user flow.
                    Your application needs to handle this error code by running a specific user flow that resets the password.
                    """.trimIndent()
                return
            }

            /* Failed to acquireToken */Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        override fun onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.")
        }
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");

            /* display result info */
            displayResult(authenticationResult);

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            final String B2C_PASSWORD_CHANGE = "AADB2C90118";
            if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
                logTextView.setText("Users click the 'Forgot Password' link in a sign-up or sign-in user flow.\n" +
                        "Your application needs to handle this error code by running a specific user flow that resets the password.");
                return;
            }

            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

--- 

## <a name="step-6-call-a-web-api"></a>Paso 6: Llamada a una API web

Para llamar a una [API web de autorización basada en tokens](enable-authentication-web-api.md), la aplicación necesita un token de acceso válido. La aplicación hace lo siguiente:


1. Adquiere un token de acceso con los permisos necesarios (ámbitos) para el punto de conexión de API web.
1. Pasa el token de acceso como token de portador en el encabezado de autorización de la solicitud HTTP con este formato:

```http
Authorization: Bearer <access-token>
```

Cuando los usuarios [inician sesión interactivamente](#step-53-start-an-interactive-authorization-request), la aplicación obtiene un token de acceso en el método de devolución de llamada `getAuthInteractiveCallback`. Para las llamadas consecutivas a la API web, utilice el procedimiento silencioso de adquisición de tokens tal y como se describe en esta sección. 

Antes de llamar a una API web, llame al método `acquireTokenSilentAsync` con los ámbitos adecuados para el punto de conexión de la API web. La biblioteca MSAL hace lo siguiente:

1. Intenta capturar un token de acceso con los ámbitos solicitados de la caché de tokens. Si el token está presente, se devuelve. 
1. Si el token no está presente en la caché de tokens, MSAL intenta usar su token de actualización para adquirir un nuevo token. 
1. Si el token de actualización no existe o ha expirado, se devuelve una excepción. Recomendamos que pida al usuario que [inicie sesión de forma interactiva](#step-53-start-an-interactive-authorization-request).  

El fragmento de código siguiente muestra cómo adquirir un token de acceso:

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

El evento de clic del botón `acquireTokenSilentButton` adquiere un token de acceso con los ámbitos proporcionados. 

```kotlin
btn_acquireTokenSilently.setOnClickListener(View.OnClickListener {
    if (b2cApp == null) {
        return@OnClickListener
    }
    val selectedUser = users!![user_list.getSelectedItemPosition()]
    selectedUser.acquireTokenSilentAsync(b2cApp!!,
            policy_list.getSelectedItem().toString(),
            B2CConfiguration.scopes,
            authSilentCallback)
})
```

El método de devolución de llamada `authSilentCallback` devuelve un token de acceso y llama a una API web:

```kotlin
private val authSilentCallback: SilentAuthenticationCallback
    private get() = object : SilentAuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            Log.d(TAG, "Successfully authenticated")

            /* Call your web API here*/
            callWebAPI(authenticationResult)
        }

        override fun onError(exception: MsalException) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception is MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    }
```

El ejemplo siguiente se demuestra cómo llamar a una API web protegida con un token de portador:

```kotlin
@Throws(java.lang.Exception::class)
private fun callWebAPI(authenticationResult: IAuthenticationResult) {
    val accessToken = authenticationResult.accessToken
    val thread = Thread {
        try {
            val url = URL("https://your-app-service.azurewebsites.net/helo")
            val conn = url.openConnection() as HttpsURLConnection
            conn.setRequestProperty("Accept", "application/json")
            
            // Set the bearer token
            conn.setRequestProperty("Authorization", "Bearer $accessToken")
            if (conn.responseCode == HttpURLConnection.HTTP_OK) {
                val br = BufferedReader(InputStreamReader(conn.inputStream))
                var strCurrentLine: String?
                while (br.readLine().also { strCurrentLine = it } != null) {
                    Log.d(TAG, strCurrentLine)
                }
            }
            conn.disconnect()
        } catch (e: IOException) {
            e.printStackTrace()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
    thread.start()
}
```

#### <a name="java"></a>[Java](#tab/java)

El evento de clic del botón `acquireTokenSilentButton` adquiere un token de acceso con los ámbitos proporcionados. 

```java
acquireTokenSilentButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        if (b2cApp == null) {
            return;
        }

        final B2CUser selectedUser = users.get(b2cUserList.getSelectedItemPosition());
        selectedUser.acquireTokenSilentAsync(b2cApp,
                policyListSpinner.getSelectedItem().toString(),
                B2CConfiguration.getScopes(),
                getAuthSilentCallback());
    }
});
```

El método de devolución de llamada `authSilentCallback` devuelve un token de acceso y llama a una API web:

```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Call your web API here*/
            callWebAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

El ejemplo siguiente se demuestra cómo llamar a una API web protegida con un token de portador:

```java
private void callWebAPI(IAuthenticationResult authenticationResult) throws Exception {
    final String accessToken = authenticationResult.getAccessToken();
    
    
    Thread thread = new Thread(new Runnable() {
    
        @Override
        public void run() {
            try {
                URL url = new URL("https://your-app-service.azurewebsites.net/helo");
                HttpsURLConnection conn = (HttpsURLConnection)url.openConnection();
                conn.setRequestProperty("Accept", "application/json");
                
                // Set the bearer token
                conn.setRequestProperty("Authorization", "Bearer " +  accessToken);
    
                if (conn.getResponseCode() == HttpURLConnection.HTTP_OK) {
                    BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
                    String strCurrentLine;
                    while ((strCurrentLine = br.readLine()) != null) {
                        Log.d(TAG, strCurrentLine);
                    }
                }
                conn.disconnect();
            } catch (IOException e) {
                e.printStackTrace();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    });
    
    thread.start();
    
    }
```

--- 

### <a name="add-permission-to-perform-network-operations"></a>Adición de permiso para realizar operaciones de red

Para realizar operaciones de red en la aplicación, agregue el siguiente permiso para el manifiesto. Para obtener más información, consulte [Cómo conectarse a la red](https://developer.android.com/training/basics/network-ops/connecting).

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo:
* [Configuración de las opciones de autenticación en una aplicación de Android mediante Azure AD B2C](enable-authentication-android-app-options.md)
* [Habilitación de la autenticación en su propia API web mediante Azure Active Directory B2C](enable-authentication-web-api.md)
