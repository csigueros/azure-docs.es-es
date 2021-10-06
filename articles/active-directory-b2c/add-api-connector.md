---
title: Adición de conectores de API a flujos de usuario de registro
description: Configure un conector de API para usarlo en un flujo de usuario de registro.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 288cffbaa90db2725d2aa2985c003474b2c7eb7b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570736"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow"></a>Adición de un conector de API a un flujo de usuario de registro

Como desarrollador o administrador de TI, puede usar conectores de API para integrar los flujos de usuario de registro con las API REST a fin de personalizar la experiencia de registro e integrarla con los sistemas externos. Al final de este tutorial podrá crear un flujo de usuario de Azure AD B2C que interactúe con [servicios de la API de REST](api-connectors-overview.md) para modificar sus experiencias de registro. 

::: zone pivot="b2c-user-flow"
Puede crear un punto de conexión de API mediante uno de estos [ejemplos](api-connector-samples.md#api-connector-rest-api-samples).
::: zone-end

::: zone pivot="b2c-custom-policy"

En este escenario, se agregará la posibilidad de que los usuarios escriban un número de fidelidad en la página de inicio de sesión de Azure AD B2C. La API REST valida si la combinación de correo electrónico y número de fidelidad está asignada a un código promocional. Si la API REST encuentra un código promocional para este usuario, se devolverá a Azure AD B2C. Por último, el código promocional se insertará en las notificaciones del token para que la aplicación lo consuma.

La interacción también se puede diseñar como un paso de orquestación. Esto es adecuado cuando la API REST no va a validar datos en pantalla y siempre devuelve notificaciones. Para más información, consulte [Tutorial: Integración de intercambios de notificaciones de API REST en los recorridos de usuario de Azure AD B2C como un paso de orquestación](add-api-connector-token-enrichment.md).

::: zone-end

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="create-an-api-connector"></a>Creación de un conector de API

Para usar un [conector de API](api-connectors-overview.md), primero debe crear el conector de API y, después, habilitarlo en un flujo de usuario.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. En **Servicios de Azure**, seleccione **Azure AD B2C**.
1. Seleccione **Conectores de API** y **New API connector** (Nuevo conector de API).

   ![Captura de pantalla de la configuración básica de un conector de API](media/add-api-connector/api-connector-new.png)

1. Escriba el nombre para mostrar de la llamada. Por ejemplo, **Validar la información de usuario**.
1. Proporcione el valor de **Dirección URL del punto de conexión** de la llamada API.
1. Elija el **tipo de autenticación** y configure la información de autenticación para llamar a la API. Obtenga más información sobre la [Protección de un conector de API](secure-rest-api.md).

   ![Captura de pantalla de la configuración de autenticación de un conector de API](media/add-api-connector/api-connector-config.png)

1. Seleccione **Guardar**.

## <a name="the-request-sent-to-your-api&quot;></a>Solicitud enviada a la API
Un conector de API se materializa como una solicitud **HTTP POST** y envía los atributos de usuario (&quot;claims") como pares de clave y valor en un cuerpo JSON. Los atributos se serializan de forma similar a las propiedades de usuario de [Microsoft Graph](/graph/api/resources/user#properties). 

**Solicitud de ejemplo**
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
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "step": "<step-name>",
 "client_id":"93fd07aa-333c-409d-955d-96008fd08dd9",
 "ui_locales":"en-US"
}
```

Solo se pueden enviar en la solicitud las propiedades de usuario y los atributos personalizados que se enumeran en la experiencia **Azure AD B2C** > **Atributos de usuario**.

Los atributos personalizados existen en el formato **extension_\<extensions-app-id>_CustomAttribute** en el directorio. La API esperará recibir las notificaciones con este mismo formato serializado. Para obtener más información sobre los atributos personalizados, consulte [Definición de atributos personalizados en Azure AD B2C](user-flow-custom-attributes.md).

Además, estas notificaciones normalmente se envían en todas las solicitudes:
- **Configuraciones regionales de interfaz de usuario ("ui_locales")** : configuraciones regionales de un usuario final configuradas en su dispositivo. La API puede usarlas para devolver respuestas internacionalizadas.
- **Paso ("step")** : paso o punto del flujo de usuario en el que se ha invocado al conector de API. Estos valores incluyen:
  - `PostFederationSignup`: corresponde a "Después de la federación con un proveedor de identidades durante el registro".
  - `PostAttributeCollection`: corresponde a "Antes de crear el usuario".
  - `PreTokenIssuance`: corresponde a "Antes de enviar el token (versión preliminar)". [Más información sobre este paso](add-api-connector-token-enrichment.md)
- **Id. de cliente ("client_id"):** valor `appId` de la aplicación en la que se está autenticando un usuario final en un flujo de usuario. *No* es el valor `appId` de la aplicación de recursos de los tokens de acceso.
- **Dirección de correo electrónico ("email")** o [**identidades ("identidades")**](/graph/api/resources/objectidentity): la API puede usar estas notificaciones para identificar al usuario final que se autentica en la aplicación.
  
> [!IMPORTANT]
> Si una notificación no tiene un valor en el momento en que se llama al punto de conexión de la API, la notificación no se enviará a la API. La API debe estar diseñada para comprobar y controlar explícitamente el caso en el que una notificación no está en la solicitud.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Habilitación del conector de API en un flujo de usuario

Siga estos pasos para agregar el conector de API a un flujo de usuario de registro.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. En **Servicios de Azure**, seleccione **Azure AD B2C**.
1. Seleccione **Flujos de usuario** y, después, seleccione el flujo de usuario para el que desea habilitar el conector de API.
1. Seleccione **Conectores de API** y, después, seleccione los puntos de conexión de API que desea invocar en los pasos siguientes del flujo de usuario:

   - **Después de la federación con un proveedor de identidades durante el registro**
   - **Antes de crear el usuario**
   - **Antes de enviar el token (versión preliminar)**

   ![Selección de un conector de API para un paso en el flujo de usuario](media/add-api-connector/api-connectors-user-flow-select.png)

1. Seleccione **Guardar**.

Estos pasos solo existen para **Registrarse e iniciar sesión (recomendado)** y **Registrarse (recomendado)** , pero solo se aplican a la parte de registro de la experiencia.

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Después de la federación con un proveedor de identidades durante el registro

Inmediatamente después de que el usuario se autentique con un proveedor de identidades (como Google, Facebook o Azure AD), se invoca un conector de API en este paso del proceso de registro. Este paso precede a la ***página de recopilación de atributos***, que es el formulario que se muestra al usuario para recopilar los atributos de usuario. Este paso no se invoca si un usuario se registra con una cuenta local.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Solicitud de ejemplo enviada a la API en este paso
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
 "givenName":"John",
 "lastName":"Smith",
 "step": "PostFederationSignup",
 "client_id":"<guid>",
 "ui_locales":"en-US"
}
```

Las notificaciones exactas enviadas a la API dependen de la información proporcionada por el proveedor de identidades. Siempre se envía "email".

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de respuesta esperados de la API web en este paso

Cuando la API web recibe una solicitud HTTP de Azure AD durante un flujo de usuario, puede devolver estas respuestas:

- Respuesta de continuación
- Respuesta de bloqueo

#### <a name="continuation-response"></a>Respuesta de continuación

Una respuesta de continuación indica que el flujo de usuario debe continuar en el paso siguiente: la página de colección de atributos.

En una respuesta de continuación, la API puede devolver notificaciones. Si la API devuelve una notificación, esta realiza lo siguiente:

- Rellena previamente el campo de entrada en la página de colección de atributos.

Vea un ejemplo de una [respuesta de continuación](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Respuesta de bloqueo

Una respuesta de bloqueo termina el flujo de usuario. La API puede emitirla intencionadamente para detener el flujo de usuario y mostrar una página de bloqueo al usuario. La página de bloqueo muestra el mensaje `userMessage` proporcionado por la API.

Vea un ejemplo de una [respuesta de bloqueo](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Antes de crear el usuario

Después de la página de recopilación de atributos, se invoca un conector de API en este paso del proceso de registro, si se incluye uno. Este paso siempre se invoca antes de crear una cuenta de usuario.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Solicitud de ejemplo enviada a la API en este paso

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
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "step": "PostAttributeCollection",
 "client_id":"93fd07aa-333c-409d-955d-96008fd08dd9",
 "ui_locales":"en-US"
}
```

Las notificaciones enviadas a la API dependen de la información recopilada por el usuario o proporcionada por el proveedor de identidades.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de respuesta esperados de la API web en este paso

Cuando la API web recibe una solicitud HTTP de Azure AD durante un flujo de usuario, puede devolver estas respuestas:

- Respuesta de continuación
- Respuesta de bloqueo
- Respuesta de validación

#### <a name="continuation-response"></a>Respuesta de continuación

Una respuesta de continuación indica que el flujo de usuario debe continuar en el paso siguiente: creación del usuario en el directorio.

En una respuesta de continuación, la API puede devolver notificaciones. Si la API devuelve una notificación, esta realiza lo siguiente:

- Invalida cualquier valor que ya haya proporcionado un usuario en la página de colección de atributos.

Para escribir notificaciones en el directorio al realizar el registro que no se deben recopilar del usuario, debe seleccionar las notificaciones en **Atributos de usuario** del flujo de usuario, acción que de forma predeterminada pedirá valores al usuario, pero puede usar [JavaScript o CSS personalizados](customize-ui-with-html.md) para ocultar los campos de entrada a un usuario final.

Vea un ejemplo de una [respuesta de continuación](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Respuesta de bloqueo
Una respuesta de bloqueo termina el flujo de usuario. La API puede emitirla intencionadamente para detener el flujo de usuario y mostrar una página de bloqueo al usuario. La página de bloqueo muestra el mensaje `userMessage` proporcionado por la API.

Vea un ejemplo de una [respuesta de bloqueo](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Respuesta de error de validación
 Cuando la API responde con una respuesta de error de validación, el flujo de usuario permanece en la página de colección de atributos y se muestra `userMessage` al usuario. El usuario puede editar el formulario y volver a enviarlo. Este tipo de respuesta se puede usar para la validación de datos de entrada.

Vea un ejemplo de una [respuesta de error de validación](#example-of-a-validation-error-response).

## <a name="before-sending-the-token-preview"></a>Antes de enviar el token (versión preliminar)

> [!IMPORTANT]
> Los conectores de API usados en este paso están en versión preliminar. Para más información sobre las versiones preliminares, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En este paso se invoca a un conector de API cuando se va a emitir un token durante los inicios de sesión y los registros. Se puede usar un conector de API para este paso para enriquecer el token con valores de notificación de orígenes externos.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Solicitud de ejemplo enviada a la API en este paso

```http
POST <API-endpoint>
Content-type: application/json

{
 "clientId": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenApplicationClaims",
 "ui_locales":"en-US"
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
}
```

Las notificaciones que se envían a la API dependen de la información definida para el usuario.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de respuesta esperados de la API web en este paso

Cuando la API web recibe una solicitud HTTP de Azure AD durante un flujo de usuario, puede devolver estas respuestas:

- Respuesta de continuación

#### <a name="continuation-response"></a>Respuesta de continuación

Una respuesta de continuación indica que el flujo de usuario debe continuar con el paso siguiente: emisión del token.

En una respuesta de continuación, la API puede devolver notificaciones adicionales. Una notificación devuelta por la API que quiera devolver en el token debe ser una notificación integrada o estar [definida como un atributo personalizado](user-flow-custom-attributes.md), y debe seleccionarse en la configuración de **Notificaciones de aplicación** del flujo de usuario. 

El valor de notificación del token es el devuelto por la API, no el valor del directorio. La respuesta de la API no puede sobrescribir algunos valores de notificación. Las notificaciones que puede devolver la API corresponden al conjunto que se encuentra en **Atributos de usuario**, con la excepción de `email`.

Vea un ejemplo de una [respuesta de continuación](#example-of-a-continuation-response).

> [!NOTE]
> La API solo se invoca durante una autenticación inicial. Al usar tokens de actualización para obtener de forma silenciosa nuevos tokens de acceso o de identificador, el token incluye los valores evaluados durante la autenticación inicial. 

## <a name="example-responses"></a>Respuestas de ejemplo

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
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Los valores devueltos pueden sobrescribir los valores recopilados de un usuario.                    |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | No es necesario que la notificación contenga `_<extensions-app-id>_`, es *opcional*. Los valores devueltos pueden sobrescribir los valores recopilados de un usuario. |

### <a name="example-of-a-blocking-response"></a>Ejemplo de una respuesta de bloqueo

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parámetro   | Tipo   | Requerido | Descripción                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Sí      | Versión de la API.                                                    |
| action      | String | Sí      | El valor debe ser `ShowBlockPage`.                                              |
| userMessage | String | Sí      | Mensaje que se va a mostrar al usuario.                                            |

**Experiencia del usuario final con una respuesta de bloqueo**

![Ejemplo de una respuesta de bloqueo](media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Ejemplo de una respuesta de error de validación

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Parámetro   | Tipo    | Requerido | Descripción                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Sí      | Versión de la API.                                                    |
| action      | String  | Sí      | El valor debe ser `ValidationError`.                                           |
| status      | Entero/cadena | Sí      | Debe ser un valor `400` o `"400"` para una respuesta ValidationError.  |
| userMessage | String  | Sí      | Mensaje que se va a mostrar al usuario.                                            |

> [!NOTE]
> El código de estado HTTP debe ser "400" además del valor de "status" en el cuerpo de la respuesta.

**Experiencia del usuario final con una respuesta de error de validación**

![Ejemplo de una respuesta de error de validación](media/add-api-connector/validation-error-postal-code.png)

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prepare-a-rest-api-endpoint"></a>Preparación del punto de conexión de API REST

Para este tutorial, debe tener una API REST que valide si una dirección de correo electrónico está registrada en el sistema de back-end con un identificador de fidelidad. Si lo está, la API REST debe devolver un código de promoción de registro, que el cliente puede usar para comprar mercancías desde la aplicación. De lo contrario, la API REST debería devolver un mensaje de error HTTP 409 similar al siguiente: "El id. de fidelidad "{id. fidelidad}" no está asociado con la dirección de correo "{correo}"".

El siguiente código JSON muestra los datos que Azure AD B2C enviará al punto de conexión de la API REST. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Una vez que la API REST valide los datos, debe devolver un código HTTP 200 (correcto), con los siguientes datos JSON:

```json
{
    "promoCode": "24534"
}
```

Si se produce un error en la validación, la API REST debe devolver un código HTTP 409 (conflicto), con el elemento JSON `userMessage`. IEF espera la notificación `userMessage` que devuelve la API REST. Esta notificación se presentará como una cadena para el usuario si se produce un error en la validación.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

La configuración del punto de conexión de API REST está fuera del ámbito de este artículo. Hemos creado una muestra de [Azure Functions](../azure-functions/functions-reference.md). Puede acceder al código completo de la función de Azure en [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definición de notificaciones

Una notificación proporciona un almacenamiento temporal de datos durante la ejecución de una directiva de Azure AD B2C. Puede declarar notificaciones dentro de la sección del [esquema de notificaciones](claimsschema.md). 

1. Abra el archivo de extensiones de la directiva. Por ejemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**</em>.
1. Busque el elemento [BuildingBlocks](buildingblocks.md). Si el elemento no existe, agréguelo.
1. Busque el elemento [ClaimsSchema](claimsschema.md). Si el elemento no existe, agréguelo.
1. Agregue las notificaciones siguientes al elemento **ClaimsSchema**.  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Incorporación del perfil técnico de la API RESTful 

Un [perfil técnico de RESTful](restful-technical-profile.md) proporciona compatibilidad para interactuar con su propio servicio RESTful. Azure AD B2C envía datos al servicio RESTful en una colección `InputClaims` y recibe los datos en una colección `OutputClaims`. Busque el elemento **ClaimsProviders** y agregue un nuevo proveedor de notificaciones como se indica a continuación:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

En este ejemplo, `userLanguage` se enviará al servicio REST como `lang` desde la carga de JSON. El valor de la notificación `userLanguage` contiene el identificador de idioma del usuario actual. Para obtener más información, consulte el [solucionador de notificaciones](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Configuración del perfil técnico de la API RESTful 

Después de implementar la API REST, configure los metadatos del perfil técnico `REST-ValidateProfile` para que reflejen su propia API REST, incluidos:

- **ServiceUrl**. Establezca la dirección URL del punto de conexión de la API REST.
- **SendClaimsIn**. Especifique cómo se envían las notificaciones de entrada al proveedor de notificaciones RESTful.
- **AuthenticationType**. Establezca el tipo de autenticación realizada por el proveedor de notificaciones RESTful. 
- **AllowInsecureAuthInProduction**. En un entorno de producción, asegúrese de establecer estos metadatos en `true`.
    
Consulte los [metadatos del perfil técnico de RESTful](restful-technical-profile.md#metadata) para obtener más configuraciones.

Los comentarios anteriores `AuthenticationType` y `AllowInsecureAuthInProduction` especifican los cambios que se deben realizar al pasar a un entorno de producción. Para aprender a proteger las API RESTful para la producción, consulte [Proteger la API RESTful](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Validación de la entrada del usuario

Para obtener el número de fidelidad del usuario durante el registro, debe permitir que el usuario escriba estos datos en la pantalla. Agregue la notificación de salida **loyaltyId** a la página de registro. Para hacerlo, agréguela al elemento `OutputClaims` de la sección del perfil técnico de registro existente. Especifique la lista completa de notificaciones de salida para controlar el orden en que se presentan las notificaciones en la pantalla.  

Agregue la referencia del perfil técnico de validación al perfil técnico de registro, que llama a `REST-ValidateProfile`. El nuevo perfil técnico de validación se agregará al principio de la colección `<ValidationTechnicalProfiles>` definida en la directiva base. Este comportamiento significa que, solo después de la validación correcta, Azure AD B2C pasa a crear la cuenta en el directorio.   

1. Busque el elemento **ClaimsProviders**. Agregue un nuevo proveedor de notificaciones como se muestra a continuación:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Incorporación de una notificación en el token 

Para devolver la notificación del código de promoción a la aplicación de usuario de confianza, agregue una notificación de salida al archivo <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. La notificación de salida permitirá agregar la notificación al token después de un recorrido del usuario correcto y se enviará a la aplicación. Modifique el elemento de perfil técnico en la sección de usuario de confianza para agregar `promoCode` como una notificación de salida.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Prueba de la directiva personalizada

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD. Para ello, seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD en la lista **Nombre de directorio** y, después, seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Registros de aplicaciones**.
1. Seleccione **Marco de experiencia de identidad**.
1. Seleccione **Cargar directiva personalizada** y cargue los archivos de directiva modificados: *TrustFrameworkExtensions.xml* y *SignUpOrSignin.xml*. 
1. Seleccione la directiva de registro o inicio de sesión que cargó y haga clic en el botón **Ejecutar ahora**.
1. Debe poder registrarse con una dirección de correo electrónico.
1. Haga clic en el vínculo **Registrarse ahora**.
1. En **Your loyalty ID** (Id. de fidelidad), escriba 1234 y haga clic en **Continuar**. Llegados a este punto, debería obtener un mensaje de error de validación.
1. Cambie a otro valor y haga clic en **Continuar**.
1. El token enviado de vuelta a la aplicación contiene la notificación `promoCode`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

::: zone-end

## <a name="best-practices-and-how-to-troubleshoot"></a>Procedimientos recomendados y solución de problemas

::: zone pivot="b2c-user-flow"

### <a name="using-serverless-cloud-functions"></a>Uso de funciones de nube sin servidor

Las funciones sin servidor, como los [desencadenadores HTTP de Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md), proporcionan una manera de crear puntos de conexión de API para usarlos con el conector de API. Puede usar la función de nube sin servidor para, [por ejemplo](api-connector-samples.md#api-connector-rest-api-samples), crear la lógica de validación y limitar los registros a dominios específicos. La función de nube sin servidor también puede llamar e invocar a otras API web, almacenes de datos y otros servicios en la nube para escenarios complejos.

### <a name="best-practices"></a>Procedimientos recomendados
Asegúrese de que:
* La API sigue los contratos de solicitud y respuesta de la API, tal y como se describe anteriormente. 
* La **URL del punto de conexión** del conector de API apunta al punto de conexión de API correcto.
* La API busca explícitamente valores NULL de las notificaciones recibidas de las que depende.
* La API implementa un método de autenticación descrito en [Protección de un conector de API](secure-rest-api.md).
* La API responde lo más rápido posible para garantizar una experiencia de usuario fluida.
    * Azure AD B2C espera un máximo de *20 segundos* para recibir una respuesta. Si no se recibe ninguna, realiza *un intento más (reintento)* de llamar a la API.
    * Si usa una función sin servidor o un servicio web escalable, use un plan de hospedaje que mantenga la API "activa" o "caliente" en producción. En Azure Functions, se recomienda usar como mínimo el [plan Premium](../azure-functions/functions-scale.md) en producción.
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

![Ejemplo de una transacción del conector de API durante la autenticación de usuario](media/add-api-connector/example-anonymized-audit-log.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="using-serverless-cloud-functions"></a>Uso de funciones de nube sin servidor

Las funciones en la nube sin servidor, como los [desencadenadores HTTP en Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md), proporcionan una manera sencilla, de alta disponibilidad y de alto rendimiento de crear puntos de conexión de API para usarlos como conectores de API.

### <a name="best-practices"></a>Procedimientos recomendados
Asegúrese de que:
* La API busca explícitamente valores NULL de las notificaciones recibidas de las que depende.
* La API implementa un método de autenticación descrito en [Protección de un conector de API](secure-rest-api.md).
* La API responde lo más rápido posible para garantizar una experiencia de usuario fluida. 
    * Si usa una función sin servidor o un servicio web escalable, use un plan de hospedaje que mantenga la API "activa" o "caliente" en producción. En Azure Functions, se recomienda usar como mínimo el [plan Premium](../azure-functions/functions-scale.md).
* Garantice una alta disponibilidad de la API.
* Supervise y optimice el rendimiento de las API de nivel inferior, las bases de datos u otras dependencias de la API.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]
 
### <a name="use-logging"></a>Uso del registro

En general, resulta útil usar las herramientas de registro que habilita el servicio de API web, como [Application Insights](../azure-functions/functions-monitoring.md), para supervisar la API en busca de códigos de error inesperados, excepciones y rendimiento deficiente.
* Supervise los códigos de estado HTTP que no sean HTTP 200 ni 400.
* Un código de estado HTTP 401 o 403 suele indicar que hay un problema con la autenticación. Compruebe la capa de autenticación de la API y la configuración correspondiente en el conector de API.
* Use niveles más agresivos de registro (por ejemplo, "trace" o "debug") en el desarrollo, si es necesario.
* Supervise la API en busca de tiempos de respuesta prolongados.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

::: zone pivot="b2c-user-flow"

- Comience a trabajar con nuestros [ejemplos](api-connector-samples.md#api-connector-rest-api-samples).
- [Protección de un conector de API](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

- [Tutorial: Integración de intercambios de notificaciones de API REST en los recorridos de usuario de Azure AD B2C como un paso de orquestación](add-api-connector-token-enrichment.md)
- [Protección de un conector de API](secure-rest-api.md)
- [Referencia: Perfil técnico de RESTful](restful-technical-profile.md)

::: zone-end
