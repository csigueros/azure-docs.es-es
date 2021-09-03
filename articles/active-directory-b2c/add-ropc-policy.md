---
title: Configuración del flujo de credenciales de contraseña del propietario del recurso
titleSuffix: Azure AD B2C
description: Obtenga información sobre cómo configurar el flujo de credenciales de contraseña del propietario del recurso en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4c4d31d7a1d9e67b1c246de50887d65206a12d57
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284624"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Configuración del flujo de credenciales de contraseña del propietario del recurso en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

En Azure Active Directory B2C (Azure AD B2C), el flujo de las credenciales de contraseña de propietario del recurso (ROPC) es un flujo de autenticación estándar de OAuth. En este flujo, una aplicación (que también se conoce como usuario de confianza), intercambia credenciales válidas por tokens. En las credenciales, se incluye un identificador de usuario y una contraseña. Los tokens devueltos son un token de identificador, un token de acceso y un token de actualización.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>Notas del flujo ROPC

En Azure Active Directory B2C (Azure AD B2C), se admiten las siguientes opciones:

- **Cliente nativo**: la interacción del usuario durante la autenticación se produce cuando se ejecuta el código en un dispositivo en el lado del usuario. El dispositivo puede ser una aplicación móvil que se ejecuta en un sistema operativo nativo como, por ejemplo, Android e iOS.
- **Public client flow** (Flujo de cliente público): en la llamada API, solo se envían credenciales de usuario recopiladas por una aplicación. No se envían las credenciales de la aplicación.
- **Add new claims** (Agregar nuevas notificaciones): se puede cambiar el contenido del token de id. para agregar nuevas notificaciones.

No se admiten los siguientes flujos:

- **De servidor a servidor**: el sistema de protección de identidades necesita una dirección IP de confianza que recopile el autor de la llamada (el cliente nativo) como parte de la interacción. En una llamada API del lado servidor, se utiliza sólo la dirección IP del servidor. Si se supera un umbral dinámico de errores de autenticación, el sistema de protección de identidad puede identificar una dirección IP repetida como un atacante.
- **Confidential client flow (Flujo de cliente confidencial)** : se valida el identificador de cliente de la aplicación, pero no el secreto de la aplicación.

Al usar el flujo ROPC, tenga en cuenta lo siguiente:

- ROPC no funciona cuando se produce una interrupción en el flujo de autenticación que requiere la interacción del usuario. Por ejemplo, cuando una contraseña ha expirado o debe cambiarse, se requiere la [autenticación multifactor](multi-factor-authentication.md), o cuando es necesario recopilar más información durante el inicio de sesión (por ejemplo, el consentimiento del usuario).
- ROPC solo admite cuentas locales. Los usuarios no pueden iniciar sesión con [proveedores de identidades federados](add-identity-provider.md), como Microsoft, Google +, Twitter, AD-FS o Facebook.
- La [administración de sesiones](session-behavior.md), incluido el control para [mantener la sesión iniciada (KMSI)](session-behavior.md#enable-keep-me-signed-in-kmsi) no están disponibles.


## <a name="register-an-application"></a>Registro de una aplicación

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Creación de un flujo de usuario del propietario del recurso

1. Inicie sesión en Azure Portal como administrador global del inquilino de Azure AD B2C.
2. Para cambiar al inquilino de Azure AD B2C, seleccione el directorio B2C en la esquina superior derecha del portal.
3. Seleccione **Flujos de usuario** y **Nuevo flujo de usuario**.
4. Seleccione **Inicio de sesión con las credenciales de contraseña del propietario del recurso (ROPC)** .
5. En **Versión**, asegúrese de que se haya seleccionado **Versión preliminar** y, a continuación, seleccione **Crear**.
7. Proporcione un nombre para el flujo de usuario, como *ROPC_Auth*.
8. En **Notificaciones de la aplicación**, haga clic en **Mostrar más**.
9. Seleccione las notificaciones de la aplicación que necesite para su aplicación, como el nombre para mostrar, la dirección de correo electrónico y el proveedor de identidades.
10. Seleccione **Aceptar** y después **Crear**.

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>Creación de una directiva del propietario del recurso

1. Abra el archivo *TrustFrameworkExtensions.xml*.
2. Si aún no existe, agregue un elemento **ClaimsSchema** y sus elementos secundarios como el primer elemento dentro del elemento **BuildingBlocks**:

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Después de **ClaimsSchema**, agregue un elemento **ClaimsTransformations** y sus elementos secundarios al elemento **BuildingBlocks**:

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Busque el elemento **ClaimsProvider** que tenga como **DisplayName**`Local Account SignIn` y agregue el siguiente perfil técnico:

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Reemplace el valor **DefaultValue** de **client_id** por el identificador de aplicación de la aplicación ProxyIdentityExperienceFramework que ha creado en el tutorial de requisitos previos. Reemplace el valor **DefaultValue** de **resource_id** por el identificador de aplicación de la aplicación ProxyIdentityExperienceFramework que también ha creado en el tutorial de requisitos previos.

5. Agregue los siguientes elementos de **ClaimsProvider** con sus perfiles técnicos al elemento **ClaimsProviders**:

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Agregue un elemento **UserJourneys** y sus elementos secundarios al elemento **TrustFrameworkPolicy**:

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. En la página **Directivas personalizadas** del inquilino de Azure AD B2C, seleccione **Cargar directiva**.
8. Habilite **Sobrescribir la directiva, si existe**, y busque y seleccione el archivo *TrustFrameworkExtensions.xml*.
9. Haga clic en **Cargar**.

## <a name="create-a-relying-party-file"></a>Creación de un archivo de usuario de confianza

Después, actualice el archivo del usuario de confianza que inicia el recorrido del usuario que ha creado:

1. Realice una copia del archivo *SignUpOrSignin.xml* en el directorio de trabajo y cambie el nombre a *ROPC_Auth.xml*.
2. Abra el nuevo archivo y actualice el valor del atributo **PolicyId** del elemento **TrustFrameworkPolicy** con un valor único. El identificador de directiva es el nombre de la directiva. Por ejemplo, **B2C_1A_ROPC_Auth**.
3. Cambie el valor del atributo **ReferenceId** en **DefaultUserJourney** a `ResourceOwnerPasswordCredentials`.
4. Cambie el elemento **OutputClaims** para que solo contenga las siguientes notificaciones:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. En la página **Directivas personalizadas** del inquilino de Azure AD B2C, seleccione **Cargar directiva**.
6. Habilite **Sobrescribir la directiva, si existe**, y busque y seleccione el archivo *ROPC_Auth.xml*.
7. Haga clic en **Cargar**.


::: zone-end

## <a name="test-the-ropc-flow"></a>Prueba del flujo ROPC

Use su aplicación favorita de desarrollo de API para generar una llamada de API y revise la respuesta para depurar la directiva. Cree una llamada como en este ejemplo con la siguiente información como el cuerpo de la solicitud POST:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Reemplace `<tenant-name>` por el nombre del inquilino de Azure AD B2C.
- Reemplace `B2C_1A_ROPC_Auth` por el nombre completo de la directiva de credenciales de contraseña del propietario de recursos.

| Clave | Value |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | id_token del token |

- Reemplace `user-account` por el nombre de una cuenta de usuario en el inquilino.
- Reemplace `password1` por la contraseña de la cuenta de usuario.
- Reemplace `application-id` por el identificador de aplicación del registro *ROPC_Auth_app*.
- *Offline_access* es opcional si quiere recibir un token de actualización.

La solicitud POST real es similar al ejemplo siguiente:

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Una respuesta correcta con acceso sin conexión se parece al siguiente ejemplo:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Canjear un token de actualización

Cree una llamada POST como la que se muestra aquí. Use la información de la tabla siguiente como el cuerpo de la solicitud:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Reemplace `<tenant-name>` por el nombre del inquilino de Azure AD B2C.
- Reemplace `B2C_1A_ROPC_Auth` por el nombre completo de la directiva de credenciales de contraseña del propietario de recursos.

| Clave | Value |
| --- | ----- |
| grant_type | refresh_token |
| response_type | ID_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Reemplace `application-id` por el identificador de aplicación del registro *ROPC_Auth_app*.
- Reemplace `refresh-token` por el **refresh_token** que se ha vuelto a enviar en la respuesta anterior.

Una respuesta correcta se parece al siguiente ejemplo:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="troubleshooting"></a>Solución de problemas

### <a name="the-provided-application-is-not-configured-to-allow-the-oauth-implicit-flow"></a>La aplicación proporcionada no está configurada para permitir el flujo implícito de "OAuth"

* **Síntoma**: ejecuta el flujo ROPC y recibe el siguiente mensaje: *AADB2C90057: La aplicación proporcionada no está configurada para permitir el flujo implícito de "OAuth"* .
* **Causas posibles**: no se permite el flujo implícito para la aplicación.
* **Resolución**: al crear el [registro de aplicación](#register-an-application) en Azure AD B2C, debe editar manualmente el manifiesto de aplicación y establecer el valor de la propiedad `oauth2AllowImplicitFlow` en `true`. Después de configurar la propiedad `oauth2AllowImplicitFlow`, el cambio puede tardar unos minutos en aplicarse (por lo general no más de cinco). 

## <a name="use-a-native-sdk-or-app-auth"></a>Usar un SDK nativo o autenticación de aplicación

La implementación de Azure AD B2C cumple los estándares de OAuth 2.0 para las credenciales de contraseña de propietario de recursos del cliente público y es compatible con la mayoría de los SDK de cliente. Para obtener la información más reciente, consulte [Procedimientos recomendados de implementación modernos para el SDK de aplicaciones nativas para OAuth 2.0 y OpenID Connect](https://appauth.io/).

## <a name="next-steps"></a>Pasos siguientes

Descargue ejemplos funcionales que se han configurado para su uso con Azure AD B2C desde GitHub, [para Android](https://aka.ms/aadb2cappauthropc) y [para iOS](https://aka.ms/aadb2ciosappauthropc).
