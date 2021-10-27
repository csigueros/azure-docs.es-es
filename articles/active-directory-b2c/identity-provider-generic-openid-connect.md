---
title: Configuración del registro y del inicio de sesión con OpenID Connect
titleSuffix: Azure AD B2C
description: Configuración del registro y del inicio de sesión con cualquier proveedor de identidades de OpenID Connect (IdP) en Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 688210352385e95c7253ac82d95154eaf707d216
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066344"
---
# <a name="set-up-sign-up-and-sign-in-with-generic-openid-connect-using-azure-active-directory-b2c"></a>Configuración de la suscripción y el inicio de sesión con OpenID Connect genérico mediante Azure Active Directory B2C

[OpenID Connect](openid-connect.md) es un protocolo de autenticación basado en OAuth 2.0 que se puede usar para el inicio de sesión de usuario seguro. La mayoría de los proveedores de identidades que usan este protocolo se admiten en Azure AD B2C. 

En este artículo se explica cómo puede agregar proveedores de identidades personalizados de OpenID Connect en sus flujos de usuario.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="add-the-identity-provider"></a>Agregar el proveedor de identidades

::: zone pivot="b2c-user-flow"

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Proveedores de identidades** y luego **Nuevo proveedor de OpenID Connect**.
1. Escriba un **nombre**. Por ejemplo, escriba *Contoso*.

::: zone-end

::: zone pivot="b2c-custom-policy"

Para definir el proveedor de identidades de OpenId Connect, agréguelo al elemento **ClaimsProvider** en el archivo de extensión de la directiva.

1. Abra el archivo *TrustFrameworkExtensions.xml*.
2. Busque el elemento **ClaimsProviders**. Si no existe, agréguelo debajo del elemento raíz.
3. Agregue un nuevo elemento **ClaimsProvider** tal como se muestra a continuación:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Login with Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://your-identity-provider.com/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <!-- <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
          </CryptographicKeys> -->
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

::: zone-end

## <a name="configure-the-identity-provider"></a>Configurar el proveedor de identidades

Cada proveedor de identidades de OpenID Connect describe un documento de metadatos que contiene la mayor parte de la información necesaria para iniciar sesión. Este documento de metadatos incluye las direcciones URL que se usan y la ubicación de las claves de firma pública del servicio. El documento de metadatos de OpenID Connect siempre se encuentra en un punto de conexión que termina en `.well-known/openid-configuration`. En cuanto al proveedor de identidades de OpenID Connect que quiere agregar, escriba su URL de metadatos.

::: zone pivot="b2c-user-flow"

En la **URL de metadatos**, escriba la dirección URL del documento de metadatos de OpenID Connect.

::: zone-end

::: zone pivot="b2c-custom-policy"

En los metadatos del perfil técnico `<Item Key="METADATA">`, escriba la dirección URL del documento de metadatos de OpenID Connect.

::: zone-end

## <a name="client-id-and-secret"></a>Id. de cliente y secreto

Para permitir que los usuarios inicien sesión, el proveedor de identidades pide a los desarrolladores que registren una aplicación en su servicio. Esta aplicación tiene un identificador que se denomina **ID de cliente** y un **secreto de cliente**. 

El secreto del cliente es opcional. Sin embargo, debe proporcionar un secreto de cliente si el [Tipo de respuesta](#response-type) es `code`, que usa el secreto para intercambiar el código para el token.

::: zone pivot="b2c-user-flow"

Para agregar el id. de cliente y el secreto de cliente, copie estos valores del proveedor de identidades y escríbalos en los campos correspondientes.

::: zone-end

::: zone pivot="b2c-custom-policy"

En los metadatos del perfil técnico `<Item Key="client_id">`, escriba el id. de cliente.

### <a name="create-a-policy-key"></a>Creación de una clave de directiva

Si se requiere el secreto de cliente, almacene el secreto de cliente que haya registrado previamente en el inquilino de Azure AD B2C.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el filtro **Directorio y suscripción** en la barra de herramientas del portal.
3. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
4. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
5. En la página de introducción, seleccione **Identity Experience Framework**.
6. Seleccione **Claves de directiva** y luego **Agregar**.
7. En **Opciones**, elija `Manual`.
8. Escriba un **nombre** para la clave de directiva. Por ejemplo, `ContosoSecret`. Se agregará el prefijo `B2C_1A_` automáticamente al nombre de la clave.
9. En **Secreto**, escriba el secreto de cliente que haya registrado previamente.
10. En **Uso de claves**, seleccione `Signature`.
11. Haga clic en **Crear**.
12. En el elemento XML `CryptographicKeys`, agregue el siguiente elemento:
    
    ```xml
    <CryptographicKeys>
      <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
    </CryptographicKeys>
    ```

::: zone-end

## <a name="scope"></a>Ámbito

El ámbito define la información y los permisos que quiere recopilar del proveedor de identidades, por ejemplo `openid profile`. Para recibir el token de identificación del proveedor de identidades, se debe especificar el ámbito `openid`. 

Sin ese token de identificador, los usuarios no pueden iniciar sesión en Azure AD B2C mediante el proveedor de identidades personalizado. Pueden anexarse otros ámbitos separados con espacios. Consulte la documentación del proveedor de identidades personalizado para ver otros ámbitos disponibles.

::: zone pivot="b2c-user-flow"

En **Ámbito**, escriba los ámbitos del proveedor de identidades. Por ejemplo, `openid profile`.

::: zone-end

::: zone pivot="b2c-custom-policy"

En los metadatos del perfil técnico `<Item Key="scope">`, escriba los ámbitos del proveedor de identidades. Por ejemplo, `openid profile`.

::: zone-end

## <a name="response-type"></a>Tipo de respuesta

El tipo de respuesta describe qué tipo de información se envía en la llamada inicial al valor `authorization_endpoint` del proveedor de identidades personalizado. Pueden usarse estos tipos de respuesta:

* `code`: según el [flujo de código de autorización](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), se devolverá un código a Azure AD B2C. Azure AD B2C llama a `token_endpoint` para intercambiar el código del token.
* `id_token`: se devuelve un token de identificador a Azure AD B2C desde el proveedor de identidades personalizado.

::: zone pivot="b2c-user-flow"

En **Tipo de respuesta**, seleccione `code` o `id_token`, según la configuración del proveedor de identidades.

::: zone-end

::: zone pivot="b2c-custom-policy"

En los metadatos del perfil técnico `<Item Key="response_types">`, seleccione `code` o `id_token`, según la configuración del proveedor de identidades.

::: zone-end

## <a name="response-mode"></a>Modo de respuesta

El modo de respuesta define el método que se debe usar para devolver los datos a Azure AD B2C desde el proveedor de identidades personalizado. Se pueden usar estos modos de respuesta:

* `form_post`: este modo de respuesta se recomienda para mayor seguridad. La respuesta se transmite a través del método HTTP `POST`, con el código o token codificado en el cuerpo mediante el formato `application/x-www-form-urlencoded`.
* `query`: el código o el token se devuelve como un parámetro de consulta.

::: zone pivot="b2c-user-flow"

En **Modo de respuesta**, seleccione `form_post` o `query`, según la configuración del proveedor de identidades.

::: zone-end

::: zone pivot="b2c-custom-policy"

En los metadatos del perfil técnico `<Item Key="response_mode">`, seleccione `form_post` o `query`, según la configuración del proveedor de identidades.

::: zone-end

## <a name="domain-hint"></a>Sugerencia de dominio

La [sugerencia de dominio](direct-signin.md) se puede usar para ir directamente a la página de inicio de sesión del proveedor de identidades especificado, en lugar de hacer que el usuario lo seleccione en la lista de proveedores de identidades disponibles. 

Para permitir este tipo de comportamiento, escriba un valor en la sugerencia de dominio. Para ir al proveedor de identidades personalizado, agregue el parámetro `domain_hint=<domain hint value>` al final de la solicitud cuando llame a Azure AD B2C para iniciar sesión.

::: zone pivot="b2c-user-flow"

En **Sugerencia de dominio**, escriba un nombre de dominio usado en la sugerencia de dominio.

::: zone-end

::: zone pivot="b2c-custom-policy"

En el elemento XML del perfil técnico `<Domain>contoso.com</Domain>`, escriba un nombre de dominio usado en la sugerencia de dominio. Por ejemplo, `contoso.com`.

::: zone-end

## <a name="claims-mapping"></a>Asignación de notificaciones

Una vez que el proveedor de identidades personalizado devuelve un token de id. a Azure AD B2C, Azure AD B2C debe poder asignar las notificaciones del token recibido a aquellas que Azure AD B2C ya reconoce y usa. Para cada una de las asignaciones que se detallan aquí, lea la documentación del proveedor de identidades personalizado para comprender las notificaciones que se devuelven en los tokens del proveedor de identidades:

::: zone pivot="b2c-user-flow"

* **Id. de usuario**: especifique la notificación que proporciona el *identificador único* del usuario con sesión iniciada.
* **Nombre para mostrar**: especifique la notificación que proporciona el *nombre para mostrar* o el *nombre completo* del usuario.
* **Nombre propio**: especifique la notificación que proporciona el *nombre* del usuario.
* **Apellido**: especifique la notificación que proporciona el *apellido* del usuario.
* **Correo electrónico**: especifique la notificación que proporciona la *dirección de correo electrónico* del usuario.


::: zone-end

::: zone pivot="b2c-custom-policy"

El elemento `OutputClaims` contiene una lista de notificaciones devueltas por el proveedor de identidades. Asigne el nombre de la notificación definida en la directiva al nombre definido en el proveedor de identidades. En el elemento `<OutputClaims>`, configure el atributo `PartnerClaimType` con el nombre de notificación correspondiente tal como lo define el proveedor de identidades. 

|ClaimTypeReferenceId  |PartnerClaimType  |
|---------|---------|
| `issuerUserId`| especifique la notificación que proporciona el *identificador único* del usuario con sesión iniciada.|
| `displayName`| especifique la notificación que proporciona el *nombre para mostrar* o el *nombre completo* del usuario. |
| `givenName`| especifique la notificación que proporciona el *nombre* del usuario.|
| `surName`| especifique la notificación que proporciona el *apellido* del usuario.|
| `email`| especifique la notificación que proporciona la *dirección de correo electrónico* del usuario.|
| `identityProvider` | Escriba la notificación que proporciona el nombre del emisor del token. Por ejemplo, `iss`. Si el proveedor de identidades no incluye la notificación del emisor en el token, establezca el atributo `DefaultValue` con un identificador único del proveedor de identidades. Por ejemplo, `DefaultValue="contoso.com"`.|


::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-the-identity-provider-to-a-user-flow"></a>Adición del proveedor de identidades a un flujo de usuario 

1. En el inquilino de Azure AD B2C, seleccione **Flujos de usuario**.
1. Haga clic en el flujo de usuario al que quiere agregar el proveedor de identidades. 
1. En **Proveedores de identidades sociales**, seleccione el proveedor de identidades que ha agregado. Por ejemplo, *Contoso*.
1. Seleccione **Guardar**.

## <a name="test-your-user-flow"></a>Prueba del flujo de usuario

1. Para probar la directiva, seleccione **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *testapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar flujo de usuario**.
1. En la página de registro o de inicio de sesión, seleccione el proveedor de identidades en el que desea iniciar sesión. Por ejemplo, *Contoso*.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

1. Seleccione la directiva de usuarios de confianza, por ejemplo `B2C_1A_signup_signin`.
1. En **Aplicación**, seleccione la aplicación web que [registró anteriormente](tutorial-register-applications.md). La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar ahora**.
1. En la página de registro o de inicio de sesión, seleccione **Contoso** para iniciar sesión con la cuenta de Google.

Si el proceso de inicio de sesión se completa correctamente, el explorador se redirige a `https://jwt.ms`, que muestra el contenido del token devuelto por Azure AD B2C.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte la guía de referencia [Perfil técnico de OpenId Connect](openid-connect-technical-profile.md).

::: zone-end
