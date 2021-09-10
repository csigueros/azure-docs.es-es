---
title: 'Enriquecimiento de tokens: Azure Active Directory B2C'
description: Enriquezca tokens con notificaciones de orígenes externos mediante API.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 20473d6a5dcbdc826605d46928a7bfbb90792e0e
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186072"
---
# <a name="enrich-tokens-with-claims-from-external-sources-using-api-connectors"></a>Enriquecimiento de tokens con notificaciones de orígenes externos mediante conectores de API

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) permite a los desarrolladores de identidades integrar una interacción con una API RESTful en su flujo de usuario mediante [conectores de API](api-connectors-overview.md). Al final de este tutorial va a poder crear un flujo de usuario de Azure AD B2C que interactúe con las API para enriquecer los tokens con información de orígenes externos.

::: zone pivot="b2c-user-flow"

Puede usar los conectores de API aplicados al paso **Antes de enviar el token (versión preliminar)** para enriquecer los tokens de las aplicaciones con información de orígenes externos. Cuando un usuario inicia sesión o se registra, Azure AD B2C llama al punto de conexión de API configurado en el conector de API, que puede consultar información sobre un usuario en servicios de nivel inferior, como servicios en la nube, almacenes de usuarios personalizados, sistemas de permisos personalizados, sistemas de identidad heredados, etc.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Puede crear un punto de conexión de API mediante uno de estos [ejemplos](api-connector-samples.md#api-connector-rest-api-samples).

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-api-connector"></a>Creación de un conector de API

Para usar un [conector de API](api-connectors-overview.md), primero debe crear el conector de API y, después, habilitarlo en un flujo de usuario.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En **Servicios de Azure**, seleccione **Azure AD B2C**.
4. Seleccione **Conectores de API** y **New API connector** (Nuevo conector de API).

   ![Captura de pantalla de la configuración básica de un conector de API](media/add-api-connector-token-enrichment/api-connector-new.png)

5. Escriba el nombre para mostrar de la llamada. Por ejemplo, **Enriquecer token de origen externo**.
6. Proporcione el valor de **Dirección URL del punto de conexión** de la llamada API.
7. Elija el **tipo de autenticación** y configure la información de autenticación para llamar a la API. Obtenga más información sobre la [Protección de un conector de API](secure-rest-api.md).

   ![Captura de pantalla de la configuración de autenticación de un conector de API](media/add-api-connector-token-enrichment/api-connector-config.png)

8. Seleccione **Guardar**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Habilitación del conector de API en un flujo de usuario

Siga estos pasos para agregar el conector de API a un flujo de usuario de registro.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En **Servicios de Azure**, seleccione **Azure AD B2C**.
4. Seleccione **Flujos de usuario** y, después, seleccione el flujo de usuario para el que desea habilitar el conector de API.
5. Seleccione **Conectores de API** y luego el punto de conexión de API que quiere invocar en el paso **Antes de enviar el token (versión preliminar)** del flujo de usuario:

   ![Captura de pantalla de la selección de un conector de API en un paso del flujo de usuario](media/add-api-connector-token-enrichment/api-connectors-user-flow-select.png)

6. Seleccione **Guardar**.

Este paso solo existe en los flujos de usuario **Registro e inicio de sesión (recomendado)** , **Registro (recomendado)** e **Inicio de sesión (recomendado)** .

## <a name="example-request-sent-to-the-api-at-this-step"></a>Solicitud de ejemplo enviada a la API en este paso

En este paso se invoca a un conector de API cuando se va a emitir un token durante los inicios de sesión y los registros. 

Un conector de API se materializa como una solicitud **HTTP POST** y envía los atributos de usuario ("claims") como pares de clave y valor en un cuerpo JSON. Los atributos se serializan de forma similar a las propiedades de usuario de [Microsoft Graph](/graph/api/resources/user#properties). 

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "objectId": "ab3ec3b2-a435-45e4-b93a-56a005e88bb7",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "client_id": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenIssuance",
 "ui_locales":"en-US"
}
```

Las notificaciones que se envían a la API dependen de la información definida para el usuario.

Solo se pueden enviar en la solicitud las propiedades de usuario y los atributos personalizados que se enumeran en la experiencia **Azure AD B2C** > **Atributos de usuario**.

Los atributos personalizados existen en el formato **extension_\<extensions-app-id>_CustomAttribute** en el directorio. La API esperará recibir las notificaciones con este mismo formato serializado. Para obtener más información sobre los atributos personalizados, consulte [Definición de atributos personalizados en Azure AD B2C](user-flow-custom-attributes.md).

Además, estas notificaciones normalmente se envían en todas las solicitudes de este paso:
- **Configuraciones regionales de interfaz de usuario ("ui_locales"):** configuraciones regionales de un usuario final configuradas en su dispositivo. La API puede usar esto para devolver respuestas internacionalizadas.
- **Paso ("step"):** paso o punto del flujo de usuario en el que se ha invocado al conector de API. El valor de este paso es `.
- **Id. de cliente ("client_id"):** valor `appId` de la aplicación en la que se está autenticando un usuario final en un flujo de usuario. *No* es el valor `appId` de la aplicación de recursos de los tokens de acceso.
- **objectId:** identificador del usuario. Puede usarlo para consultar servicios de nivel inferior a fin de obtener información sobre el usuario.
  
> [!IMPORTANT]
> Si una notificación no tiene un valor en el momento en que se llama al punto de conexión de la API, la notificación no se enviará a la API. La API debe estar diseñada para comprobar y controlar explícitamente el caso en el que una notificación no está en la solicitud.

## <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de respuesta esperados de la API web en este paso

Cuando la API web recibe una solicitud HTTP de Azure AD durante un flujo de usuario, puede devolver una "respuesta de continuación".

### <a name="continuation-response"></a>Respuesta de continuación

Una respuesta de continuación indica que el flujo de usuario debe ir al paso siguiente: emisión del token.

En una respuesta de continuación, la API puede devolver notificaciones adicionales. Una notificación devuelta por la API que quiera devolver en el token debe ser una notificación integrada o estar [definida como un atributo personalizado](user-flow-custom-attributes.md), y debe seleccionarse en la configuración **Notificaciones de aplicación** del flujo de usuario. 

El valor de notificación del token es el devuelto por la API, no el valor del directorio. La respuesta de la API no puede sobrescribir algunos valores de notificación. Las notificaciones que puede devolver la API corresponden al conjunto que se encuentra en **Atributos de usuario**, con la excepción de `email`.

> [!NOTE]
> La API solo se invoca durante una autenticación inicial. Al usar tokens de actualización para obtener de forma silenciosa nuevos tokens de acceso o de identificador, el token incluye los valores evaluados durante la autenticación inicial. 

## <a name="example-response"></a>Respuesta de ejemplo

### <a name="example-of-a-continuation-response"></a>Ejemplo de una respuesta de continuación

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parámetro                                          | Tipo              | Requerido | Descripción                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version     | String | Sí      | Versión de la API.                                                    |
| action                                             | String            | Sí      | El valor debe ser `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Se pueden devolver en el token si están seleccionados como **Notificación de aplicación**.                                        |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | No es necesario que la notificación contenga `_<extensions-app-id>_`, es *opcional*. Se pueden devolver en el token si están seleccionados como **Notificación de aplicación**.  |

::: zone-end

::: zone pivot="b2c-custom-policy"

En este escenario, enriquecemos los datos de token del usuario mediante la integración con un flujo de trabajo de línea de negocio corporativo. Durante el registro o inicio de sesión con una cuenta local o federada, Azure AD B2C invoca una API REST para obtener los datos de perfil extendido del usuario de un origen de datos remoto. En este ejemplo, Azure AD B2C envía el identificador único del usuario: objectId. Después, la API REST devuelve el saldo de la cuenta del usuario (un número aleatorio). Use este ejemplo como punto de partida para la integración con su propio sistema CRM, base de datos de marketing o cualquier flujo de trabajo de línea de negocio.

También puede diseñar la interacción como un perfil técnico de validación. Esto es adecuado cuando la API REST va a validar datos en pantalla y devolver notificaciones. Para más información, consulte [Tutorial: Adición de un conector de API a un flujo de usuario de registro](add-api-connector.md).

## <a name="prerequisites"></a>Prerrequisitos

- Realice los pasos del artículo [Introducción a las directivas personalizadas](tutorial-create-user-flows.md?pivots=b2c-custom-policy). Debe tener una directiva personalizada activa para registrar e iniciar sesión de cuentas locales.
- Más información sobre cómo [integrar notificaciones de API REST en la directiva personalizada de Azure AD B2C](api-connectors-overview.md).

## <a name="prepare-a-rest-api-endpoint"></a>Preparación del punto de conexión de API REST

Para este tutorial, debe tener una API REST que valide si un objectId de Azure AD B2C de un usuario está registrado en el sistema de back-end. Si está registrado, la API REST devuelve el saldo de la cuenta de usuario. De lo contrario, la API REST registra la nueva cuenta en el directorio y devuelve el saldo inicial `50.00`.

El siguiente código JSON muestra los datos que Azure AD B2C enviará al punto de conexión de la API REST. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Una vez que la API REST valide los datos, debe devolver un código HTTP 200 (correcto), con los siguientes datos JSON:

```json
{
    "balance": "760.50"
}
```

La configuración del punto de conexión de API REST está fuera del ámbito de este artículo. Hemos creado una muestra de [Azure Functions](../azure-functions/functions-reference.md). Puede acceder al código completo de la función de Azure en [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definición de notificaciones

Una notificación proporciona un almacenamiento temporal de datos durante la ejecución de una directiva de Azure AD B2C. Puede declarar notificaciones dentro de la sección del [esquema de notificaciones](claimsschema.md). 

1. Abra el archivo de extensiones de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Busque el elemento [BuildingBlocks](buildingblocks.md). Si el elemento no existe, agréguelo.
1. Busque el elemento [ClaimsSchema](claimsschema.md). Si el elemento no existe, agréguelo.
1. Agregue las notificaciones siguientes al elemento **ClaimsSchema**.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Incorporación del perfil técnico de la API RESTful 

Un [perfil técnico de RESTful](restful-technical-profile.md) proporciona compatibilidad para interactuar con su propio servicio RESTful. Azure AD B2C envía datos al servicio RESTful en una colección `InputClaims` y recibe los datos en una colección `OutputClaims`. Busque el elemento **ClaimsProviders** en el archivo <em> **`TrustFrameworkExtensions.xml`**</em> y agregue un nuevo proveedor de notificaciones como se indica a continuación:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

En este ejemplo, `userLanguage` se enviará al servicio REST como `lang` desde la carga de JSON. El valor de la notificación `userLanguage` contiene el identificador de idioma del usuario actual. Para obtener más información, consulte el [solucionador de notificaciones](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Configuración del perfil técnico de la API RESTful 

Después de implementar la API REST, configure los metadatos del perfil técnico `REST-GetProfile` para que reflejen su propia API REST, incluidos:

- **ServiceUrl**. Establezca la dirección URL del punto de conexión de la API REST.
- **SendClaimsIn**. Especifique cómo se envían las notificaciones de entrada al proveedor de notificaciones RESTful.
- **AuthenticationType**. Establezca el tipo de autenticación realizada por el proveedor de notificaciones RESTful. 
- **AllowInsecureAuthInProduction**. En un entorno de producción, asegúrese de establecer estos metadatos en `true`.
    
Consulte los [metadatos del perfil técnico de RESTful](restful-technical-profile.md#metadata) para obtener más configuraciones.

Los comentarios anteriores `AuthenticationType` y `AllowInsecureAuthInProduction` especifican los cambios que se deben realizar al pasar a un entorno de producción. Para aprender a proteger las API RESTful para la producción, consulte [Proteger la API RESTful](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Agregar un paso de orquestación

Los [recorridos del usuario](userjourneys.md) especifican rutas de acceso explícitas con las que una directiva permite que una aplicación de usuario de confianza obtenga las notificaciones deseadas para un usuario. El recorrido del usuario se representa como una secuencia de orquestación por la que hay que pasar para lograr una transacción correcta. Puede agregar o quitar pasos de orquestación. En este caso, agregará un nuevo paso de orquestación que se usa para aumentar la información proporcionada a la aplicación después del registro o el inicio de sesión del usuario a través de la llamada a la API REST.

1. Abra el archivo base de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>.
1. Busque el elemento `<UserJourneys>`. Copie todo el elemento y luego elimínelo.
1. Abra el archivo de extensiones de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Pegue `<UserJourneys>` en el archivo de extensiones, después del cierre del elemento `<ClaimsProviders>`.
1. Localice `<UserJourney Id="SignUpOrSignIn">` y agregue el siguiente paso de orquestación antes del último.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refactorice el último paso de orquestación cambiando `Order` a `8`. Los dos pasos finales de la orquestación deben tener un aspecto similar al siguiente:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Repita los dos últimos pasos para los recorridos del usuario **ProfileEdit** y **PasswordReset**.


## <a name="include-a-claim-in-the-token"></a>Incorporación de una notificación en el token 

Para devolver la notificación `balance` a la aplicación de usuario de confianza, agregue una notificación de salida al archivo <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. La incorporación de una notificación de salida emitirá la notificación al token después de un recorrido del usuario correcto y se enviará a la aplicación. Modifique el elemento de perfil técnico en la sección de usuario de confianza para agregar `balance` como una notificación de salida.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Repita este paso para los recorridos del usuario **ProfileEdit.xml** y **PasswordReset.xml**.

Guarde los archivos que ha cambiado: *TrustFrameworkBase.xml* y *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* y *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Prueba de la directiva personalizada

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD. Para ello, seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD en la lista **Nombre de directorio** y, después, seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Registros de aplicaciones**.
1. Seleccione **Marco de experiencia de identidad**.
1. Seleccione **Cargar directiva personalizada** y cargue los archivos de directiva modificados: *TrustFrameworkBase.xml* y *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* y *PasswordReset.xml*. 
1. Seleccione la directiva de registro o inicio de sesión que cargó y haga clic en el botón **Ejecutar ahora**.
1. Debe poder suscribirse con una dirección de correo electrónico o una cuenta de Facebook.
1. El token enviado de vuelta a la aplicación contiene la notificación `balance`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="best-practices-and-how-to-troubleshoot"></a>Procedimientos recomendados y solución de problemas

### <a name="using-serverless-cloud-functions"></a>Uso de funciones de nube sin servidor

Las funciones sin servidor, como los [desencadenadores HTTP de Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md), proporcionan una manera de crear puntos de conexión de API para usarlos con el conector de API. La función de nube sin servidor también puede llamar e invocar a otras API web, almacenes de datos y otros servicios en la nube para escenarios complejos.

### <a name="best-practices"></a>Procedimientos recomendados

Asegúrese de que:
* La API sigue los contratos de solicitud y respuesta de la API, tal y como se describe anteriormente. 
* La **URL del punto de conexión** del conector de API apunta al punto de conexión de API correcto.
* La API busca explícitamente valores NULL de las notificaciones recibidas de las que depende.
* La API implementa un método de autenticación descrito en [Protección de un conector de API](secure-rest-api.md).
* La API responde lo más rápido posible para garantizar una experiencia de usuario fluida.
    * Azure AD B2C espera un máximo de *20 segundos* para recibir una respuesta. Si no se recibe ninguna, realiza *un intento más (reintento)* de llamar a la API.
    * Si usa una función sin servidor o un servicio web escalable, utilice un plan de hospedaje que mantenga la API "activa" o "caliente" en producción. En Azure Functions, se recomienda usar como mínimo el [plan Premium](../azure-functions/functions-scale.md) en producción.
* Garantice una alta disponibilidad de la API.
* Supervise y optimice el rendimiento de las API de nivel inferior, las bases de datos u otras dependencias de la API.
  
[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

### <a name="use-logging"></a>Uso del registro

En general, resulta útil usar las herramientas de registro que habilita el servicio de API web, como [Application Insights](../azure-functions/functions-monitoring.md), para supervisar la API en busca de códigos de error inesperados, excepciones y rendimiento deficiente.
* Supervise los códigos de estado HTTP que no sean HTTP 200 ni 400.
* Un código de estado HTTP 401 o 403 suele indicar que hay un problema con la autenticación. Compruebe la capa de autenticación de la API y la configuración correspondiente en el conector de API.
* Use niveles más agresivos de registro (por ejemplo, "trace" o "debug") en el desarrollo, si es necesario.
* Supervise la API en busca de tiempos de respuesta prolongados. 

Además, Azure AD B2C registra los metadatos sobre las transacciones de API que se producen durante las autenticaciones de usuario por medio de un flujo de usuario. Para encontrarlos:
1. Vaya a **Azure AD B2C**.
2. En **Actividades**, seleccione **Registros de auditoría**.
3. Filtre la vista de lista: en **Fecha**, seleccione el intervalo de tiempo que quiere y, en **Actividad**, seleccione **Se llamó a una API como parte de un flujo de usuario**.
4. Inspeccione los registros individuales. Cada fila representa un conector de API al que intenta llamarse durante un flujo de usuario. Si se produce un error en una llamada API y se efectúa un reintento, se sigue representando como una sola fila. `numberOfAttempts` indica el número de veces que se ha llamado a la API. Este valor puede ser `1` o `2`. En los registros se detalla otra información sobre la llamada API.

   ![Captura de pantalla de un registro de auditoría de ejemplo con una transacción de conector de API](media/add-api-connector-token-enrichment/example-anonymized-audit-log.png)

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

::: zone pivot="b2c-user-flow"

- Comience a trabajar con nuestros [ejemplos](api-connector-samples.md#api-connector-rest-api-samples).
- [Protección de un conector de API](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

Para obtener información sobre cómo proteger las API, vea los siguientes artículos:

- [Tutorial: Integración de intercambios de notificaciones de API REST en los recorridos de usuario de Azure AD B2C como un paso de orquestación](add-api-connector-token-enrichment.md)
- [Protección de la API RESTful](secure-rest-api.md)
- [Referencia: Perfil técnico de RESTful](restful-technical-profile.md)

::: zone-end


