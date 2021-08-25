---
title: Guía de migración de ADAL a MSAL para Android | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo migrar la aplicación de Android de la biblioteca de autenticación de Azure Active Directory (ADAL) a la biblioteca de autenticación de Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 5a85afc8a7566c36068eb07da9bb8de23462aa28
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418800"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Guía de migración de ADAL a MSAL para Android

En este artículo se resaltan los cambios que debe realizar para migrar una aplicación que usa la biblioteca de autenticación de Azure Active Directory (ADAL) para que use la biblioteca de autenticación de Microsoft (MSAL).

## <a name="difference-highlights"></a>Diferencias destacadas

ADAL funciona con el punto de conexión de Azure Active Directory v1.0. La biblioteca de autenticación de Microsoft (MSAL) funciona con la Plataforma de identidad de Microsoft, que anteriormente se conocía como punto de conexión de Azure Active Directory v2.0. La Plataforma de identidad de Microsoft se diferencia de Azure Active Directory v1.0 en lo siguiente:

Es compatible con:
  - Identidad organizativa (Azure Active Directory)
  - Identidades no organizativas como Outlook.com, Xbox Live, etc.
  - (Solo Azure AD B2C) Inicio de sesión federado con Google, Facebook, Twitter y Amazon

- Es compatibles con las normas:
  - OAuth v2.0
  - OpenID Connect (OIDC)

La API pública de MSAL presenta cambios importantes, entre los que se incluyen:

- Un nuevo modelo de acceso a tokens:
  - ADAL proporciona acceso a los tokens a través de `AuthenticationContext`, que representa el servidor. MSAL proporciona acceso a los tokens a través de `PublicClientApplication`, que representa el cliente. Los desarrolladores de cliente no necesitan crear una nueva instancia de `PublicClientApplication` para cada autoridad con la que tengan que interactuar. Solo se requiere una configuración `PublicClientApplication`.
  - Compatibilidad para solicitar tokens de acceso mediante el uso de ámbitos además de identificadores de recursos.
  - Compatibilidad con el consentimiento incremental. Los desarrolladores pueden solicitar ámbitos a medida que el usuario tiene acceso a más funcionalidades en la aplicación, incluidas las que no se incluyen durante el registro de la aplicación.
  - Las autoridades ya no se validan en tiempo de ejecución. En su lugar, el desarrollador declara una lista de "autoridades conocidas" durante el desarrollo.
- Cambios en la API de token:
  - En ADAL, `AcquireToken()` realiza primero una solicitud silenciosa. Si no es así, realiza una solicitud interactiva. Este comportamiento dio lugar a que algunos desarrolladores confiaran solo en `AcquireToken`, lo que originó que se le solicitaran las credenciales de forma inesperada. MSAL requiere que los desarrolladores establezcan intencionadamente el momento en que el usuario recibe un mensaje de la interfaz de usuario.
    - `AcquireTokenSilent` siempre produce una solicitud silenciosa que se realiza correctamente o produce un error.
    - `AcquireToken` siempre produce una solicitud que solicita al usuario a través de la interfaz de usuario.
- MSAL admite la interacción el inicio de sesión desde un explorador predeterminado o una vista web insertada:
  - De forma predeterminada, se usa el explorador predeterminado en el dispositivo. Esto permite a MSAL usar el estado de autenticación (cookies) que ya está presente para una o varias cuentas con sesión iniciada. Si no hay ningún estado de autenticación, la autenticación durante la autorización con MSAL produce el estado de autenticación (cookies) que otras aplicaciones web pueden usar en el mismo explorador.
- Nuevo modelo de excepción:
  - Las excepciones definen más claramente el tipo de error que se ha producido y lo que el desarrollador debe hacer para resolverlo.
- MSAL admite objetos de parámetro para llamadas `AcquireToken` y `AcquireTokenSilent`.
- MSAL admite la configuración declarativa para:
  - Identificador de cliente, URI de redirección.
  - Explorador predeterminado e insertado
  - Autoridades
  - Configuración HTTP como lectura y tiempo de espera de la conexión

## <a name="your-app-registration-and-migration-to-msal"></a>Registro de la aplicación y migración a MSAL

No tiene que cambiar el registro de la aplicación existente para usar MSAL. Si desea aprovechar el consentimiento incremental/progresivo, puede que tenga que revisar el registro para identificar los ámbitos específicos que desea solicitar de manera incremental. A continuación se ofrece más información sobre los ámbitos y el consentimiento incremental.

En el registro de la aplicación en el portal, verá una pestaña **Permisos de API**. En ella se proporciona una lista de las API y los permisos (ámbitos) en los que la aplicación está configurada actualmente para solicitar acceso. También se muestra una lista de los nombres de ámbito asociados a cada permiso de la API.

### <a name="user-consent"></a>Consentimiento del usuario

Con ADAL y el punto de conexión de Azure AD v1, el consentimiento del usuario a los recursos que posee se concedió al usarlos por primera vez. Con MSAL y la Plataforma de identidad de Microsoft, se puede solicitar el consentimiento de forma incremental. El consentimiento incremental es útil para los permisos que un usuario puede considerar privilegios elevados o, por el contrario, de los que dude porque no se proporciona una explicación clara de por qué se requiere el permiso. En ADAL, esos permisos pueden provocar que el usuario abandone el registro en la aplicación.

> [!TIP]
> Use el consentimiento incremental para proporcionar contexto adicional a los usuarios sobre por qué la aplicación necesita un permiso.

### <a name="admin-consent"></a>Consentimiento de administrador

Los administradores de la organización pueden dar su consentimiento a los permisos que la aplicación necesita en nombre de todos los miembros de su organización. Algunas organizaciones solo permiten a los administradores dar su consentimiento a las aplicaciones. El consentimiento del administrador requiere que incluya en el registro de la aplicación todos los permisos de API y los ámbitos usados por la aplicación.

> [!TIP]
> Aunque puede solicitar un ámbito mediante MSAL para algo que no esté incluido en el registro de la aplicación, se recomienda actualizar el registro de la aplicación para que incluya todos los recursos y ámbitos para los que un usuario puede conceder permiso.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migración de identificadores de recursos a ámbitos

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autenticación y solicitud de autorización para todos los permisos en el primer uso

Si actualmente usa ADAL y no necesita usar el consentimiento incremental, la manera más sencilla de empezar a usar MSAL es crear una solicitud `acquireToken` con el nuevo objeto `AcquireTokenParameter` y establecer el valor del identificador de recurso.

> [!CAUTION]
> No es posible establecer ambos ámbitos y un identificador de recurso. Al intentar establecer ambos, se producirá la excepción `IllegalArgumentException`.

El resultado será el mismo comportamiento de la versión v1 que está usando. Durante la primera interacción del usuario, se le piden todos los permisos solicitados en el registro de la aplicación.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Autentique y solicite permisos solo cuando sea necesario.

Para aprovechar el consentimiento incremental, cree una lista de los permisos (ámbitos) que la aplicación usa desde el registro de la aplicación y, después, organícelos en dos listas según lo siguiente:

- Qué ámbitos desea solicitar durante la primera interacción del usuario con la aplicación, en el inicio de sesión.
- Los permisos que están asociados a una característica importante de la aplicación y que también necesitará explicar al usuario.

Una vez organizados los ámbitos, organice cada una de las listas según el recurso (API) para el que desea solicitar un token. También cualquier otro ámbito que desee que el usuario autorice al mismo tiempo.

El objeto Parameters que se usa para hacer que la solicitud a MSAL admita:

- `Scope`: La lista de ámbitos para los que desea solicitar autorización y recibir un token de acceso.
- `ExtraScopesToConsent`: Una lista adicional de ámbitos para los que desea solicitar autorización mientras está solicitando un token de acceso para otro recurso. Esta lista de ámbitos permite minimizar el número de veces que es necesario solicitar la autorización del usuario. Eso significa menos solicitudes de autorización o consentimiento del usuario.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migración de AuthenticationContext a PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Construcción de PublicClientApplication

Cuando se usa MSAL, se crean instancias de `PublicClientApplication`. Este objeto modela la identidad de la aplicación y se usa para hacer solicitudes a una o varias autoridades. Con este objeto, configurará la identidad del cliente, el URI de redirección, la autoridad predeterminada, si se va a usar el explorador de dispositivos y la vista web insertada, el nivel de registro, etc.

Puede configurar este objeto mediante una declaración con JSON, que puede proporcionar como un archivo o un almacén como un recurso dentro de su APK.

Aunque este objeto no es un singleton, internamente usa `Executors` compartidos para las solicitudes interactivas y silenciosas.

### <a name="business-to-business"></a>Colaboración entre negocios

En ADAL, cada organización a la que se solicitan tokens de acceso requiere una instancia independiente de `AuthenticationContext`. En MSAL, esto ya no es un requisito. Puede especificar la autoridad desde la que desea solicitar un token como parte de la solicitud silenciosa o interactiva.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migración de la validación de la autoridad con autoridades conocidas

MSAL no tiene una marca para habilitar o deshabilitar la validación de la autoridad. La validación de la autoridad es una característica de ADAL y de las primeras versiones de MSAL que impide que el código solicite tokens de una autoridad potencialmente malintencionada. MSAL ahora recupera una lista de autoridades conocidas para Microsoft y combina esa lista con las autoridades especificadas en la configuración.

> [!TIP]
> Si es un usuario de negocios a consumidor (B2C) de Azure, significa que ya no tiene que deshabilitar la validación de la autoridad. En su lugar, incluya cada una de las directivas de Azure AD B2C admitidas como autoridades en la configuración de MSAL.

Si intenta usar una autoridad que no es conocida por Microsoft y no se incluye en la configuración, obtendrá la excepción `UnknownAuthorityException`.

### <a name="logging"></a>Registro
Ahora puede configurar de forma declarativa el registro como parte de la configuración, como se indica a continuación:

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>Migración de UserInfo a Account

En ADAL, `AuthenticationResult` proporciona un objeto `UserInfo` que se usa para recuperar información acerca de la cuenta autenticada. El término "usuario", que significaba un humano o un agente software, se aplicaba de forma que resultaba difícil comunicar que algunas aplicaciones admiten un solo usuario (ya sea un humano o un agente software) con varias cuentas.

Piense en una cuenta bancaria. Puede tener más de una cuenta en más de una entidad financiera. Al abrir una cuenta, usted (el usuario) recibe credenciales, como una tarjeta de débito y un PIN, que se usan para tener acceso a su saldo, pagos de facturas, etc., para cada cuenta. Estas credenciales solo se pueden usar en la entidad financiera que las emitió.

De forma análoga a las cuentas de una entidad financiera, el acceso a las cuentas de la Plataforma de identidad de Microsoft se realiza con las credenciales. Estas credenciales las emite Microsoft o se registran en Microsoft. O Microsoft en nombre de una organización.

En lo que la Plataforma de identidad de Microsoft difiere de una entidad financiera del ejemplo, es que la Plataforma de identidad de Microsoft permite a los usuarios usar una cuenta y sus credenciales asociadas para acceder a los recursos que pertenecen a varias personas y organizaciones. Esto es como poder usar una tarjeta emitida por un banco en otra entidad financiera. Esto funciona porque todas las organizaciones en cuestión usan la Plataforma de identidad de Microsoft, que permite usar una cuenta en varias organizaciones. Este es un ejemplo:

Sam trabaja para Contoso.com pero administra máquinas virtuales de Azure que pertenecen a Fabrikam.com. Para que Sam administre las máquinas virtuales de Fabrikam, debe tener autorización para acceder a ellas. Se puede conceder este acceso agregando la cuenta de Sam a Fabrikam.com y concediendo a su cuenta un rol que le permita trabajar con las máquinas virtuales. Esto se haría con Azure Portal.

Agregar la cuenta de Sam de Contoso.com como miembro de Fabrikam.com daría lugar a la creación de un nuevo registro para Sam en el directorio de Azure Active Directory de Fabrikam.com. El registro de Sam en Azure Active Directory se conoce como objeto de usuario. En este caso, ese objeto de usuario apuntaría de nuevo al objeto de usuario de Sam en Contoso.com. El objeto de usuario de Sam en Fabrikam es la representación local de Sam y se utilizaría para almacenar información sobre la cuenta asociada a Sam en el contexto de Fabrikam.com. En Contoso.com, el cargo de Sam es consultor sénior de DevOps. En Fabrikam, el cargo de Sam es contratista-máquinas virtuales. En Contoso.com, Sam no es una persona responsable ni autorizada para administrar máquinas virtuales. En Fabrikam.com, es su única función de trabajo. Además, Sam todavía tiene un conjunto de credenciales de las que hay que realizar un seguimiento, que son las credenciales emitidas por Contoso.com.

Una vez realizada la llamada a `acquireToken` correcta, verá una referencia a un objeto `IAccount` que se puede usar en las solicitudes posteriores a `acquireTokenSilent`.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Si tiene una aplicación que tiene acceso a las notificaciones de una cuenta de cada uno de los inquilinos en los que la cuenta está representada, puede convertir los objetos `IAccount` en `IMultiTenantAccount`. Esta interfaz proporciona un mapa de `ITenantProfiles`, con una clave por identificador de inquilino, que permite acceder a las notificaciones que pertenecen a la cuenta en cada uno de los inquilinos a los que ha solicitado un token, en relación con la cuenta actual.

Las notificaciones en la raíz de `IAccount` y `IMultiTenantAccount` siempre contienen las notificaciones del inquilino principal. Si aún no ha realizado una solicitud para un token dentro del inquilino principal, esta colección estará vacía.

## <a name="other-changes"></a>Otros cambios

### <a name="use-the-new-authenticationcallback"></a>Uso del nuevo AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>Migración a las nuevas excepciones

En ADAL, hay un tipo de excepción, `AuthenticationException`, que incluye un método para recuperar el valor de enumeración `ADALError`.
En MSAL, hay una jerarquía de excepciones y cada una tiene su propio conjunto de códigos de error asociados.

| Excepción                                        | Descripción                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | Se produce si uno o más argumentos de entrada no son válidos.                 |
| `MsalClientException`                            | Se produce si el error está en el lado del cliente.                                 |
| `MsalDeclinedScopeException`                     | Se produce si el servidor rechazó uno o varios ámbitos solicitados. |
| `MsalException`                                  | Excepción comprobada predeterminada producida por MSAL.                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | Se produce si el recurso tiene habilitada la directiva de protección de MAMCA.         |
| `MsalServiceException`                           | Se produce si el error está en el lado del servidor.                                 |
| `MsalUiRequiredException`                        | Se produce si el token no se puede actualizar de forma silenciosa.                    |
| `MsalUserCancelException`                        | Se produce si el usuario canceló el flujo de autenticación.                |

### <a name="adalerror-to-msalexception-translation"></a>Traducción de ADALError a MsalException

| Si está detectando estos errores en ADAL…  | … detecte estas excepciones de MSAL:                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| *Ningún ADALError equivalente* | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.EVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| *Ningún ADALError equivalente* | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| *Ningún ADALError equivalente* | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| *Ningún ADALError equivalente* | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>Registro de ADAL a registro de MSAL

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
      @Override
      public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
          logs.append(message).append('\n');
      }
  });

// New Log Levels:
public enum LogLevel
{
    /**
     * Error level logging.
     */
    ERROR,
    /**
     * Warning level logging.
     */
    WARNING,
    /**
     * Info level logging.
     */
    INFO,
    /**
     * Verbose level logging.
     */
    VERBOSE
}
```
