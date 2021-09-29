---
title: Configuración de Iniciar sesión con Apple (versión preliminar)
description: Aprenda a configurar Iniciar sesión con Apple como un proveedor de identidades para una aplicación App Service o Azure Functions.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: c0da83a20440fabcf5f149c8acbf49077eb91a07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657165"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Configuración de la aplicación de App Service o Azure Functions para iniciar sesión mediante un proveedor de Iniciar sesión con Apple (versión preliminar)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este artículo se muestra cómo configurar Azure App Service o Azure Functions para usar Iniciar sesión con Apple como un proveedor de autenticación. 

Para completar el procedimiento de este artículo, debe haberse inscrito en el programa Apple Developer. Para inscribirse en el programa Apple Developer, vaya a [developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> Al habilitar Iniciar sesión con Apple, se deshabilitará la característica de autenticación o autorización de App Service de la aplicación a través de algunos clientes, como Azure Portal, la CLI de Azure y Azure PowerShell. La característica se basa en una nueva superficie de API que, durante la versión preliminar, aún no se ha tomado en cuenta para todas las experiencias de administración.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Creación de una aplicación en el portal de Apple Developer
Deberá crear un identificador de aplicación y un identificador de servicio en el portal de Apple Developer.

1. En el portal de Apple Developer, vaya a **Certificates, Identifiers, & Profiles** (Certificados, identificadores y perfiles).
2. Seleccione la pestaña **Identifiers** (Identificadores) y haga clic en el botón **(+)** .
3. En la página **Register a New Identifier** (Registrar un nuevo identificador), elija **App IDs** (Identificadores de aplicación) y seleccione **Continue** (Continuar). (Los identificadores de aplicación incluyen uno o varios identificadores de servicio). ![Registro de un nuevo identificador de aplicación en el portal de Apple Developer](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. En la página **Register an App ID** (Registrar un identificador de aplicación), proporcione una descripción y un identificador de paquete y seleccione **Iniciar sesión con Apple** en la lista de funcionalidades. Después, seleccione **Continuar**. Tome nota de su **App ID Prefix (Team ID)** [Prefijo de identificador de aplicación (id. de equipo)] en este paso, ya que lo necesitará más adelante.
![Configuración de un nuevo identificador de aplicación en el portal de Apple Developer](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Revise la información de registro de la aplicación y seleccione **Register** (Registrar).
6. De nuevo, en la pestaña **Identifiers** (Identificadores), seleccione el botón **(+)** .
![Creación de un nuevo identificador de servicio en el portal de Apple Developer](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. En la página **Register a New Identifier** (Registrar un nuevo identificador), elija **Service IDs** (Identificadores de servicio) y seleccione **Continue** (Continuar).
![Registro de un nuevo identificador de servicio en el portal de Apple Developer](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. En la página **Register a Services ID** (Registrar un identificador de servicios), proporcione una descripción y un identificador. La descripción es lo que se mostrará al usuario en la pantalla de consentimiento. El identificador será el identificador de cliente que se usará para configurar el proveedor de Apple con el servicio de aplicación. A continuación, seleccione **Configurar**.
![Proporcionar una descripción y un identificador](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. En la ventana emergente, establezca el identificador de aplicación principal en el identificador de aplicación que ha creado antes. Especifique el dominio de la aplicación en la sección de dominio. Para la dirección URL de retorno, utilice la dirección URL `<app-url>/.auth/login/apple/callback`. Por ejemplo, `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Seleccione **Agregar** y **Guardar**.
![Especificación del dominio y la URL de retorno para el registro](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Revise la información de registro del servicio y seleccione **Guardar**.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Generación del secreto de cliente
Apple requiere que los desarrolladores de aplicaciones creen y firmen un token JWT como el valor de secreto de cliente. Para generar este secreto, primero genere y descargue una clave privada de curva elíptica desde el portal de Apple Developer. A continuación, use esa clave para [firmar un JWT](#sign-the-client-secret-jwt) con una [carga útil específica](#structure-the-client-secret-jwt).

### <a name="create-and-download-the-private-key"></a>Creación y descarga de la clave privada
1. En la pestaña **Keys** (Claves) del portal de Apple Developer, elija **Create a key** (Crear una clave) o seleccione el botón **(+)** .
2. En la página **Register a New Key** (Registrar una nueva clave), asigne un nombre a la clave, active la casilla situada junto a **Iniciar sesión con Apple** y seleccione **Configurar**.
3. En la página **Configurar clave**, vincule la clave al identificador de aplicación principal que creó previamente y selecciona **Guardar**.
4. Para terminar de crear la clave, confirme la información y seleccione **Continuar**. A continuación, revise la información y seleccione **Registrar**.
5. En la página **Download Your Key** (Descarga de la clave), descargue la clave. Se descargará como un archivo `.p8` (PKCS#8): usará el contenido del archivo para firmar el JWT del secreto de cliente.

### <a name="structure-the-client-secret-jwt"></a>Estructuración del JWT del secreto de cliente
Apple requiere que el secreto de cliente tenga la codificación de Base64 de un token JWT. El token JWT descodificado debe tener una carga útil con una estructura similar a la de este ejemplo:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub**: identificador de cliente de Apple (también el identificador de servicio).
- **iss**: identificador del equipo de Apple Developer.
- **aud**: Apple recibe el token, por lo que es la audiencia.
- **exp**: no más de seis meses después de **nbf**.

La versión codificada en Base64 de la carga útil anterior tiene el siguiente aspecto: `eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG`

_Nota: Apple no acepta tokens JWT de secreto de cliente con una fecha de expiración superior a seis meses después de la fecha de creación (o nfb). Esto significa que tendrá que girar el secreto de cliente, como mínimo, cada seis meses._

Puede encontrar más información sobre la generación y validación de tokens en la [documentación para desarrolladores de Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>Firma del JWT del secreto de cliente
Usará el archivo `.p8` que descargó anteriormente para firmar el JWT del secreto de cliente. Este es un [archivo PCKS#8](https://en.wikipedia.org/wiki/PKCS_8) que contiene la clave de firma privada en formato PEM. Hay muchas bibliotecas que pueden crear y firmar el JWT por usted. 

Hay diferentes tipos de bibliotecas de código abierto disponibles en línea para crear y firmar tokens JWT. Para obtener más información sobre cómo generar tokens JWT, consulte [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Por ejemplo, una forma de generar el secreto de cliente es importar el [paquete NuGet Microsoft.IdentityModel.Tokens](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) y ejecutar una pequeña cantidad del código C# que se muestra a continuación.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: identificador del equipo de Apple Developer.
- **clientId**: identificador de cliente de Apple (también el identificador de servicio).
- **p8key**: clave de formato PEM. Para obtenerla, puede abrir el archivo `.p8` en un editor de texto y copiar todo lo que haya entre `-----BEGIN PRIVATE KEY-----` y `-----END PRIVATE KEY-----` sin saltos de línea.
- **keyId**: identificador de la clave descargada.

Este token devuelto es el valor del secreto de cliente que se usará para configurar el proveedor de Apple.

> [!IMPORTANT]
> El secreto de cliente es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.
>

Agregue el secreto de cliente como una [configuración de aplicación](./configure-common.md#configure-app-settings), con un nombre de configuración de su elección. Anote este nombre para usarlo más adelante.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Adición de información de un proveedor a su aplicación

> [!NOTE]
> La configuración necesaria está en un nuevo formato de API, que actualmente solo es compatible con la [configuración basada en archivos (versión preliminar)](configure-authentication-file-based.md). Debe seguir los pasos que se indican a continuación para usar este tipo de archivo.

Esta sección le guiará a través de la actualización de la configuración para incluir su nuevo IDP. La siguiente es una configuración de ejemplo.

1. En el objeto `identityProviders`, agregue un objeto `apple` si todavía no existe ninguno.
2. Asigne un objeto a esa clave con un objeto `registration` dentro y, opcionalmente, un objeto `login`:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client ID>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. En el objeto `registration`, establezca `clientId` en el identificador de cliente que ha recopilado.
    
    b. En el objeto `registration`, establezca `clientSecretSettingName` en el nombre de la opción de configuración de la aplicación donde almacenó el secreto de cliente.
    
    c. En el objeto `login`, puede optar por establecer la matriz `scopes` para que incluya una lista de los ámbitos utilizados al autenticarse con Apple, como "nombre" y "correo electrónico". Si los ámbitos están configurados, se solicitarán explícitamente en la pantalla de consentimiento cuando los usuarios inicien sesión por primera vez.

Una vez establecida esta configuración, está listo para usar el proveedor de Apple para autenticarse en la aplicación.

Una configuración completa podría ser similar a la siguiente (en el que el valor de APPLE_GENERATED_CLIENT_SECRET apunta a una opción de configuración que contiene un token JWT generado):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Pasos siguientes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
