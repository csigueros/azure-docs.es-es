---
title: API seguras que se usan como conectores de API en Azure AD B2C
titleSuffix: Azure AD B2C
description: Proteja las API de RESTful personalizadas que se usan como conectores de API en Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ef1c747666b2c75567d88f440cef37a631f64064
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730857"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-b2c"></a>Proteja la API que se usa como un conector de API en Azure AD B2C 

Al integrar una API de REST en un flujo de usuario de Azure Active Directory B2C, debe proteger el punto de conexión de la API mediante autenticación. La autenticación de la API de REST garantiza que solo los servicios que tengan credenciales adecuadas, como Azure Active Directory B2C, puedan realizar llamadas al punto de conexión. En este artículo se examina cómo proteger la API de REST. 


## <a name="prerequisites"></a>Requisitos previos

Complete los pasos que se incluyen en la guía [Tutorial: Incorporación de un conector de API a un flujo de usuario de registro](add-api-connector.md).

::: zone pivot="b2c-user-flow"

Puede proteger el punto de conexión de la API mediante la autenticación HTTP básica o la autenticación de certificados de cliente HTTPS. En cualquier caso, debe proporcionar las credenciales que Azure AD B2C usará al llamar a su punto de conexión de la API. A continuación, el punto de conexión de la API comprueba las credenciales y realiza decisiones de autorización.

::: zone-end

## <a name="http-basic-authentication"></a>Autenticación HTTP básica

La autenticación HTTP básica se define en [RFC 2617](https://tools.ietf.org/html/rfc2617). La autenticación básica funciona de la manera siguiente: Azure AD B2C envía una solicitud HTTP con las credenciales del cliente (`username` y `password`) en el encabezado `Authorization`. Las credenciales tienen el formato de cadena codificada en Base 64 `username:password`. A continuación, la API es responsable de comprobar estos valores para tomar otras decisiones de autorización.

::: zone pivot="b2c-user-flow"

Para configurar un conector de API con autenticación básica HTTP, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En **Servicios de Azure**, seleccione **Azure AD B2C**.
3. Seleccione **Conectores de API** y, después, el **conector de API** que quiera configurar.
4. En **Tipo de autenticación**, seleccione **Básica**.
5. Rellene los campos **Nombre de usuario** y **Contraseña** con relación al punto de conexión de la API de REST.
    :::image type="content" source="media/add-api-connector/api-connector-config.png" alt-text="Suministro de la configuración de autenticación básica de un conector de API.":::
6. Seleccione **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-rest-api-username-and-password-policy-keys"></a>Agregar claves de directiva de nombre de usuario y contraseña de la API REST

Para configurar un perfil técnico de la API REST con autenticación HTTP básica, cree las siguientes claves criptográficas para almacenar el nombre de usuario y la contraseña:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el filtro **Directorio + suscripción** en el menú superior y elija el directorio de Azure AD B2C.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. En la página de introducción, seleccione **Identity Experience Framework**.
1. Seleccione **Claves de directiva** y, luego, **Agregar**.
1. En **Opciones**, seleccione **Manual**.
1. En **Nombre**, escriba **RestApiUsername**.
    Es posible que se agregue automáticamente el prefijo *B2C_1A_* .
1. En el cuadro **Secreto**, escriba el nombre de usuario de la API REST.
1. En **Uso de la clave**, seleccione **Cifrado**.
1. Seleccione **Crear**.
1. Vuelva a seleccionar **Claves de directiva**.
1. Seleccione **Agregar**.
1. En **Opciones**, seleccione **Manual**.
1. En **Nombre**, escriba **RestApiPassword**.
    Es posible que se agregue automáticamente el prefijo *B2C_1A_* .
1. En el cuadro **Secreto**, escriba la contraseña de la API REST.
1. En **Uso de la clave**, seleccione **Cifrado**.
1. Seleccione **Crear**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Configuración del perfil técnico de la API REST para usar la autenticación HTTP básica

Después de crear las claves necesarias, configure los metadatos de perfil técnico de la API REST para que hagan referencia a las credenciales.

1. Abra el archivo de la directiva de extensión (TrustFrameworkExtensions.xml) en el directorio de trabajo.
1. Busque el perfil técnico de la API REST. Por ejemplo, `REST-ValidateProfile` o `REST-GetProfile`.
1. Busque el elemento `<Metadata>`.
1. Cambie el valor de *AuthenticationType* a `Basic`.
1. Cambie el valor de *AllowInsecureAuthInProduction* a `false`.
1. Agregue el siguiente fragmento de código XML inmediatamente después del elemento `</Metadata>` de cierre:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

El siguiente fragmento XML es un ejemplo de perfil técnico de RESTful configurado con autenticación básica HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="https-client-certificate-authentication"></a>Autenticación con certificados de cliente HTTPS

La autenticación con certificados de cliente es una autenticación mutua basada en certificados donde el cliente, Azure AD B2C, proporciona su certificado de cliente al servidor para demostrar su identidad. Esto sucede como parte del protocolo de enlace SSL. La API es responsable de validar los certificados que pertenecen a un cliente válido, como Azure AD B2C, y de tomar decisiones de autorización. El certificado de cliente es un certificado digital X.509. 

> [!IMPORTANT]
> En entornos de producción, debe estar firmado por una entidad de certificación.

### <a name="create-a-certificate"></a>Crear un certificado

#### <a name="option-1-use-azure-key-vault-recommended"></a>Opción 1: Usar Azure Key Vault (recomendado)

Para crear un certificado, puede usar [Azure Key Vault](../key-vault/certificates/create-certificate.md), que tiene opciones para certificados autofirmados e integraciones con proveedores de emisores de certificados para certificados firmados. La configuración recomendada incluye:
- **Asunto**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **Tipo de contenido**: `PKCS #12`
- **Tipo de Acton de duración**: `Email all contacts at a given percentage lifetime` o `Email all contacts a given number of days before expiry`
- **Tipo de clave**: `RSA`
- **Tamaño de clave**: `2048`
- **Clave privada exportable**: `Yes` (para poder exportar el archivo `.pfx`)

Después, puede [exportar el certificado](../key-vault/certificates/how-to-export-certificate.md).

#### <a name="option-2-prepare-a-self-signed-certificate-using-powershell-module"></a>Opción 2: Preparar un certificado autofirmado mediante el módulo de PowerShell

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

::: zone pivot="b2c-user-flow"

### <a name="configure-your-api-connector"></a>Configuración de un conector de API

Para configurar un conector de API con autenticación de certificado de cliente, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En **Servicios de Azure**, seleccione **Azure AD B2C**.
3. Seleccione **Conectores de API** y, después, el **conector de API** que quiera configurar.
4. En **Tipo de autenticación**, seleccione **Certificado**.
5. En el cuadro **Cargar certificado**, seleccione el archivo .pfx del certificado con una clave privada.
6. En el cuadro **Escribir contraseña**, escriba la contraseña del certificado.
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="Proporcionar la configuración de autenticación de certificados para un conector de API.":::
7. Seleccione **Guardar**.

### <a name="perform-authorization-decisions"></a>Toma de decisiones de autorización 
La API debe implementar la autorización basada en certificados de cliente enviados con el fin de proteger los puntos de conexión de la API. Para obtener Azure App Service y Azure Functions, consulte [Configuración de la autenticación mutua de TLS](../app-service/app-service-web-configure-tls-mutual-auth.md) para obtener información sobre cómo habilitar y *validar el certificado desde el código de la API*.  También puede usar Azure API Management como una capa delante de cualquier servicio de API para [comprobar las propiedades del certificado de cliente](
../api-management/api-management-howto-mutual-certificates-for-clients.md) con los valores deseados.

### <a name="renewing-certificates"></a>Renovación de certificados
Le recomendamos que establezca alertas de aviso para cuando expire el certificado. Deberá generar un certificado nuevo y repetir los pasos anteriores cuando los certificados usados estén a punto de expirar. Para "implementar" el uso de un nuevo certificado, el servicio de API puede seguir aceptando certificados antiguos y nuevos temporalmente mientras se implementa el nuevo certificado. 

Para cargar un nuevo certificado en un conector de API existente, seleccione el conector de API en **Conectores de API** y haga clic en **Cargar certificado nuevo**. Azure AD B2C usará automáticamente el certificado cargado más recientemente que no haya expirado y cuya fecha de inicio haya pasado.

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text="Suministro de un nuevo certificado a un conector de API cuando ya existe uno.":::

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-a-client-certificate-policy-key"></a>Adición de una clave de directiva de certificado de cliente

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el filtro **Directorio + suscripción** en el menú superior y elija el directorio de Azure AD B2C.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. En la página de introducción, seleccione **Identity Experience Framework**.
1. Seleccione **Claves de directiva** y, luego, **Agregar**.
1. En el cuadro **Opciones**, seleccione **Cargar**.
1. En el cuadro **Nombre**, escriba **RestApiClientCertificate**.
    El prefijo *B2C_1A_* se agrega automáticamente.
1. En el cuadro **Carga de archivos**, seleccione el archivo .pfx del certificado con una clave privada.
1. En el cuadro **Contraseña**, escriba la contraseña del certificado.
1. Seleccione **Crear**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Configuración del perfil técnico de la API REST para usar la autenticación de certificado de cliente

Después de crear la clave necesaria, configure los metadatos de perfil técnico de la API REST para que hagan referencia al certificado de cliente.

1. Abra el archivo de la directiva de extensión (TrustFrameworkExtensions.xml) en el directorio de trabajo.
1. Busque el perfil técnico de la API REST. Por ejemplo, `REST-ValidateProfile` o `REST-GetProfile`.
1. Busque el elemento `<Metadata>`.
1. Cambie el valor de *AuthenticationType* a `ClientCertificate`.
1. Cambie el valor de *AllowInsecureAuthInProduction* a `false`.
1. Agregue el siguiente fragmento de código XML inmediatamente después del elemento `</Metadata>` de cierre:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

El siguiente fragmento XML es un ejemplo de perfil técnico de RESTful configurado con un certificado de cliente HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>Autenticación de portador OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

La autenticación de token de portador se define en el [Marco de autorización de OAuth2.0: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) (Marco de autorización de OAuth2.0: uso del token de portador [RFC 6750]). En la autenticación de token de portador, Azure AD B2C envía una solicitud HTTP con un token en el encabezado de autorización.

```http
Authorization: Bearer <token>
```

Un token de portador es una cadena opaca. Puede ser un token de acceso de JWT o cualquier cadena que la API REST espera que Azure AD B2C envíe en el encabezado de autorización. Azure AD B2C admite los tipos siguientes:

- **Token de portador**. Para poder enviar el token de portador en el perfil técnico de RESTful, la directiva debe adquirir primero el token de portador y usarlo en el perfil técnico de RESTful.  
- **Token de portador estático**. Use este enfoque cuando la API REST emita un token de acceso a largo plazo. Para usar un token de portador estático, cree una clave de directiva y haga una referencia desde el perfil técnico de RESTful a la clave de la directiva. 


## <a name="using-oauth2-bearer"></a>Uso del portador de OAuth2  

En los pasos siguientes se muestra cómo usar las credenciales del cliente para obtener un token de portador y pasarlo al encabezado de autorización de las llamadas API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definir una notificación para almacenar el token de portador

Una notificación proporciona un almacenamiento temporal de datos durante la ejecución de una directiva de Azure AD B2C. El [esquema de notificaciones](claimsschema.md) es el lugar en el que se declaran las notificaciones. El token de acceso debe almacenarse en una notificación para usarse más tarde. 

1. Abra el archivo de extensiones de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Busque el elemento [BuildingBlocks](buildingblocks.md). Si el elemento no existe, agréguelo.
1. Busque el elemento [ClaimsSchema](claimsschema.md). Si el elemento no existe, agréguelo.
1. Agregue las notificaciones siguientes al elemento **ClaimsSchema**.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Obtención un token de acceso 

Puede obtener un token de acceso de varias maneras: obteniéndolo de un [proveedor de identidades federado](idp-pass-through-user-flow.md), mediante una llamada a una API REST que devuelve un token de acceso, mediante un [flujo de ROPC](../active-directory/develop/v2-oauth-ropc.md) o mediante el [flujo de credenciales del cliente](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). El flujo de credenciales del cliente se usa principalmente para las interacciones entre servidores que se deben ejecutar en segundo plano, sin la interacción inmediata con un usuario.

#### <a name="acquiring-an-azure-ad-access-token"></a>Adquisición de un token de acceso de Azure AD 

En el ejemplo siguiente se usa un perfil técnico de API REST para realizar una solicitud al punto de conexión del token de Azure AD con las credenciales de cliente pasadas como autenticación HTTP básica. Para obtener más información, consulte [La Plataforma de identidad de Microsoft y el flujo de credenciales de cliente de OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). 

Para que el perfil técnico pueda interactuar con Azure AD para obtener un token de acceso, debe registrar una aplicación. Azure AD B2C se basa en la plataforma de Azure AD. Puede crear la aplicación en el inquilino de Azure AD B2C o en cualquier inquilino de Azure AD que administre. Para registrar una aplicación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el filtro **Directorio y suscripción** en el menú superior y, luego, elija el directorio que contiene el inquilino de Azure AD o Azure AD B2C.
1. Seleccione **Azure Active Directory** en el menú izquierdo. O bien seleccione **Todos los servicios** y busque y seleccione **Azure Active Directory**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *App_Aut_Credenciales_Clientes*.
1. En **Tipos de cuenta admitidos**, seleccione **Solo las cuentas de este directorio organizativo**.
1. Seleccione **Registrar**.
2. Registre el **Id. de aplicación (cliente)** . 


Para un flujo de credenciales de cliente, tiene que crear un secreto de aplicación. El secreto de cliente también se conoce como contraseña de la aplicación. La aplicación usará el secreto para adquirir un token de acceso.

1. En la página **Azure AD: Registros de aplicaciones**, seleccione la aplicación que ha creado, por ejemplo, *App_Aut_Credenciales_Clientes*.
1. En el menú de la izquierda, en **Administrar**, seleccione **Certificados y secretos**.
1. Seleccione **Nuevo secreto de cliente**.
1. Escriba una descripción para el secreto de cliente en el cuadro **Descripción**. Por ejemplo, *clientsecret1*.
1. En **Expira**, seleccione el tiempo durante el cual el secreto es válido y, a continuación, seleccione **Agregar**.
1. Registre el **valor** del secreto para usarlo en el código de la aplicación cliente. Este valor secreto no se volverá a mostrar una vez que abandone esta página. Use este valor como secreto de aplicación en el código de la aplicación.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Creación de las claves de directiva de Azure AD B2C

Tiene que almacenar el identificador de cliente y el secreto de cliente que haya registrado previamente en el inquilino de Azure AD B2C.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el filtro **Directorio y suscripciones** del menú superior y elija el directorio que contiene el inquilino.
3. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
4. En la página de introducción, seleccione **Identity Experience Framework**.
5. Seleccione **Claves de directiva** y luego **Agregar**.
6. En **Opciones**, elija `Manual`.
7. Escriba un **Nombre** para la clave de directiva, `SecureRESTClientId`. Se agregará el prefijo `B2C_1A_` automáticamente al nombre de la clave.
8. En **Secreto**, escriba el identificador de cliente que haya registrado previamente.
9. En **Uso de claves**, seleccione `Signature`.
10. Seleccione **Crear**.
11. Cree otra clave de directiva con la siguiente configuración:
    -   **Nombre**: `SecureRESTClientSecret`.
    -   **Secreto**: escriba el secreto de cliente que haya registrado previamente.

Para ServiceUrl, reemplace el nombre del inquilino con el nombre del inquilino de Azure AD. Consulte las opciones disponibles en la referencia del [perfil técnico de RESTful](restful-technical-profile.md).

```xml
<TechnicalProfile Id="REST-AcquireAccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Cambio del perfil técnico de REST para usar la autenticación del token de portador

Para admitir la autenticación de tokens de portador en la directiva personalizada, modifique el perfil técnico de la API REST con lo siguiente:

1. Abra el archivo de directiva de extensión *TrustFrameworkExtensions.xml* desde el directorio de trabajo.
1. Busque el nodo `<TechnicalProfile>` que incluya `Id="REST-API-SignUp"`.
1. Busque el elemento `<Metadata>`.
1. Cambie el valor de *AuthenticationType* a *Bearer*, como se muestra a continuación:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Cambie o agregue *UseClaimAsBearerToken* a *bearerToken* como se indica a continuación. *bearerToken* es el nombre de la notificación de la que se recuperará el token de portador (notificación de salida de `REST-AcquireAccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Asegúrese de agregar la notificación usada anteriormente como notificación de entrada:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Tras agregar los fragmentos de código anteriores, el perfil técnico debe ser similar al siguiente código XML:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Uso de un portador OAuth2 estático 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Adición de la clave de la directiva de token de portador de OAuth2

Para configurar un perfil técnico de la API REST con un token de portador de OAuth2, obtenga un token de acceso del propietario de la API REST. A continuación, cree la siguiente clave criptográfica para almacenar el token de portador.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el filtro **Directorio + suscripción** en el menú superior y elija el directorio de Azure AD B2C.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. En la página de introducción, seleccione **Identity Experience Framework**.
1. Seleccione **Claves de directiva** y, luego, **Agregar**.
1. En **Opciones**, elija `Manual`.
1. Escriba un **nombre** para la clave de directiva. Por ejemplo, `RestApiBearerToken`. Se agregará el prefijo `B2C_1A_` automáticamente al nombre de la clave.
1. En **Secreto**, escriba el secreto de cliente que haya registrado previamente.
1. En **Uso de claves**, seleccione `Encryption`.
1. Seleccione **Crear**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Configuración del perfil técnico de la API REST para usar la clave de directiva de token de portador

Después de crear la clave necesaria, configure los metadatos de perfil técnico de la API REST para que hagan referencia al token de portador.

1. Abra el archivo de la directiva de extensión (TrustFrameworkExtensions.xml) en el directorio de trabajo.
1. Busque el perfil técnico de la API REST. Por ejemplo, `REST-ValidateProfile` o `REST-GetProfile`.
1. Busque el elemento `<Metadata>`.
1. Cambie el valor de *AuthenticationType* a `Bearer`.
1. Cambie el valor de *AllowInsecureAuthInProduction* a `false`.
1. Agregue el siguiente fragmento de código XML inmediatamente después del elemento `</Metadata>` de cierre:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

El siguiente fragmento XML es un ejemplo de perfil técnico RESTful configurado con autenticación de token de portador:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end


## <a name="api-key-authentication"></a>Autenticación mediante clave de API

::: zone pivot="b2c-user-flow"

Algunos servicios usan un mecanismo de "clave de API" para ofuscar el acceso a los puntos de conexión HTTP durante el desarrollo. Para ello, exigen que el autor de llamada incluya una clave única, como un encabezado HTTP o un parámetro de consulta HTTP. Para lograrlo en [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys), incluya `code` como parámetro de consulta en la **dirección URL del punto de conexión** del conector de la API. Por ejemplo, `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>. 

No se trata de un mecanismo que se debe usar por sí solo en el entorno producción. Por lo tanto, siempre se requiere la configuración de autenticación básica o de certificado. Si no quiere implementar ningún método de autenticación (opción no recomendada) con fines de desarrollo, puede elegir la autenticación básica en la configuración del conector de la API y usar valores temporales para `username` y `password` que la API pueda omitir mientras implementa la autorización adecuada.

::: zone-end

::: zone pivot="b2c-custom-policy"

La clave de API es un identificador único que se usa para autenticar a un usuario para acceder a un punto de conexión de la API REST. La clave se envía en un encabezado HTTP personalizado. Por ejemplo, el [desencadenador HTTP de Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) utiliza el encabezado HTTP `x-functions-key` para identificar al solicitante.  

### <a name="add-api-key-policy-keys"></a>Incorporación de claves de directiva de clave de API

Para configurar un perfil técnico de la API REST con autenticación mediante clave de API, cree la siguiente clave criptográfica para almacenar la clave de API:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el filtro **Directorio + suscripción** en el menú superior y elija el directorio de Azure AD B2C.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. En la página de introducción, seleccione **Identity Experience Framework**.
1. Seleccione **Claves de directiva** y, luego, **Agregar**.
1. En **Opciones**, seleccione **Manual**.
1. En **Nombre**, escriba **RestApiKey**.
    Es posible que se agregue automáticamente el prefijo *B2C_1A_* .
1. En el cuadro **Secreto**, escriba la clave de API REST.
1. En **Uso de la clave**, seleccione **Cifrado**.
1. Seleccione **Crear**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Configuración del perfil técnico de la API REST para usar la autenticación mediante clave de API

Después de crear la clave necesaria, configure los metadatos de perfil técnico de la API REST para que hagan referencia a las credenciales.

1. Abra el archivo de la directiva de extensión (TrustFrameworkExtensions.xml) en el directorio de trabajo.
1. Busque el perfil técnico de la API REST. Por ejemplo, `REST-ValidateProfile` o `REST-GetProfile`.
1. Busque el elemento `<Metadata>`.
1. Cambie el valor de *AuthenticationType* a `ApiKeyHeader`.
1. Cambie el valor de *AllowInsecureAuthInProduction* a `false`.
1. Agregue el siguiente fragmento de código XML inmediatamente después del elemento `</Metadata>` de cierre:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

El identificador (**Id**) de la clave criptográfica define el encabezado HTTP. En este ejemplo, la clave de API se envía como **x-functions-key**.

El siguiente fragmento XML es un ejemplo de perfil técnico RESTful configurado para llamar a una función de Azure con autenticación de clave de API:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="next-steps"></a>Pasos siguientes

::: zone pivot="b2c-user-flow"
- Comience a trabajar con nuestros [ejemplos](api-connector-samples.md#api-connector-rest-api-samples).
::: zone-end

::: zone pivot="b2c-custom-policy"
- Obtenga más información sobre el elemento de [perfil técnico RESTful](restful-technical-profile.md) en la referencia de directivas personalizadas.
::: zone-end
