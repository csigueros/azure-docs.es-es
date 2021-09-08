---
title: Punto de conexión UserInfo | Microsoft Docs
description: Defina un punto de conexión UserInfo en una directiva personalizada en Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/07/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 94b00fb293913e5501bd70ecb782304c56314ed9
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430131"
---
# <a name="userinfo-endpoint"></a>Punto de conexión de UserInfo

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

El punto de conexión UserInfo forma parte de la especificación del [estándar de OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC), y está diseñado para devolver notificaciones sobre el usuario que se ha autenticado. El punto de conexión UserInfo se define en la directiva de usuario de confianza mediante el elemento [EndPoint](relyingparty.md#endpoints).  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Introducción al punto de conexión UserInfo

La información de usuario UserJourney especifica:

- **Autorización**: El punto de conexión UserInfo está protegido con un token de portador. Un token de acceso emitido se presenta en el encabezado de autorización para el punto de conexión UserInfo. La directiva especifica el perfil técnico que valida el token entrante y extrae las notificaciones, como el objectId del usuario. El objectId del usuario se usa para recuperar las notificaciones que se van a devolver en la respuesta del recorrido del punto de conexión UserInfo. 
- **Paso de orquestación**: 
  - Se usa un paso de orquestación para recopilar información sobre el usuario. En función de las notificaciones dentro del token de acceso de entrada, el recorrido del usuario invoca un [perfil técnico de Azure Active Directory](active-directory-technical-profile.md) para recuperar datos sobre el usuario, por ejemplo, leyendo el usuario por objectId. 
  - **Pasos de orquestación opcionales**: Puede agregar más pasos de orquestación, como un perfil técnico de API REST para recuperar más información sobre el usuario. 
  - **Emisor de UserInfo**: Especifica la lista de notificaciones que devuelve el punto de conexión UserInfo.

## <a name="create-a-userinfo-endpoint"></a>Creación de un punto de conexión UserInfo

### <a name="1-add-the-token-issuer-technical-profile"></a>1. Adición del perfil técnico del emisor de tokens

1. Abra el archivo *TrustFrameworkExtensions.xml*.
1. Si aún no existe, agregue un elemento ClaimsProvider y sus elementos secundarios como primer elemento dentro del elemento BuildingBlocks.
1. Agregue el siguiente proveedor de notificaciones:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. La sección InputClaims del perfil técnico **UserInfoIssuer** especifica los atributos que quiere devolver. Se llama al perfil técnico UserInfoIssuer al final del recorrido del usuario. 
1. El perfil técnico **UserInfoAuthorization** valida la firma, el nombre del emisor y la audiencia del token y extrae la notificación del token de entrada. Cambie los siguientes metadatos para que reflejen su entorno:
    1. **issuer**: Este valor debe ser idéntico a la notificación `iss` dentro de la notificación del token de acceso. Los tokens emitidos por Azure AD B2C usan un emisor con el formato `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/`. Obtenga más información sobre la [personalización de tokens](configure-tokens.md).
    1. **IdTokenAudience**: Debe ser idéntico a la notificación `aud` dentro de la notificación del token de acceso. En Azure AD B2C, la notificación `aud` es el identificador de la aplicación de usuario de confianza. Este valor es una colección y admite varios valores mediante una coma delimitadora.

        En el siguiente token de acceso, el valor de la notificación `iss` es `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/`. El valor de la notificación `aud` es `22222222-2222-2222-2222-222222222222`.

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  El elemento OutputClaims del perfil técnico **UserInfoAuthorization** especifica los atributos que quiere leer desde el token de acceso. **ClaimTypeReferenceId** es la referencia a un tipo de notificación. El elemento opcional **PartnerClaimType** es el nombre de la notificación definida en el token de acceso.



### <a name="2-add-the-userjourney-element"></a>2. Adición del elemento UserJourney 

El elemento [UserJourney](userjourneys.md) define la ruta de acceso que el usuario toma al interactuar con la aplicación. Agregue el elemento **UserJourneys**, si no existe, con el elemento **UserJourney** identificado como `UserInfoJourney`:

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. Inclusión del punto de conexión en la directiva de usuario de confianza

Para incluir el punto de conexión UserInfo en la aplicación de usuario de confianza, agregue un elemento [Endpoint](relyingparty.md#endpoints) al archivo *SocialAndLocalAccounts/SignUpOrSignIn.xml*. 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

El elemento de usuario de confianza completado será como el siguiente:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. Carga de los archivos

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de usar el directorio que contiene el inquilino de Azure AD B2C. Para ello, seleccione el filtro **Directorio y suscripción** en el menú superior y luego el directorio que contiene el inquilino.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. Seleccione **Marco de experiencia de identidad**.
1. En la página **Directivas personalizadas**, seleccione **Cargar directiva personalizada**.
1. Seleccione **Sobrescribir la directiva personalizada si ya existe**, y busque y seleccione el archivo *TrustframeworkExtensions.xml*.
1. Haga clic en **Cargar**.
1. Repita los pasos 5 a 7 para el archivo del usuario de confianza, como *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Llamada al punto de conexión de UserInfo

El punto de conexión UserInfo usa la API de token de portador de OAuth2 estándar, a la que se llama mediante el token de acceso recibido al obtener un token para la aplicación. Devuelve una respuesta JSON que contiene notificaciones sobre el usuario. El punto de conexión UserInfo se hospeda en Azure AD B2C en:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

El punto de conexión /.well-known configure (documento de detección de OpenID Connect) muestra el campo `userinfo_endpoint`. Se puede descubrir mediante programación el punto de conexión UserInfo con el punto de conexión /.well-known configure, en: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Prueba de la directiva

1. En **Directivas personalizadas**, seleccione la directiva con la que ha integrado el punto de conexión UserInfo. Por ejemplo, *B2C_1A_SignUpOrSignIn*.
1. Seleccione **Ejecutar ahora**. 
1. En **Seleccionar aplicación**, seleccione la aplicación que registró anteriormente. En **Seleccionar dirección URL de respuesta**, elija `https://jwt.ms`. Para obtener más información, consulte [Registro de una aplicación web en Azure Active Directory B2C](tutorial-register-applications.md).
1. Regístrese o inicie sesión con una dirección de correo electrónico o una cuenta de red social.
1. Copie el id_token en su formato codificado desde el sitio web [https://jwt.ms](https://jwt.ms). Puede usarlo para enviar una solicitud GET al punto de conexión UserInfo y recuperar la información del usuario.
1. Envíe una solicitud GET al punto de conexión UserInfo y recupere la información del usuario.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

Una respuesta correcta tiene el siguiente aspecto:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="provide-optional-claims"></a>Proporcionar notificaciones opcionales

Para proporcionar más notificaciones a la aplicación, siga estos pasos:

1. [Agregue atributos de usuario y personalice la entrada de usuario](configure-user-input.md).
1. Modifique el elemento OutputClaims del [perfil técnico de la directiva de usuario de confianza](relyingparty.md#technicalprofile) con las notificaciones que quiere proporcionar. Use el atributo `DefaultValue` para establecer un valor predeterminado. También puede establecer el valor predeterminado en un [solucionador de notificaciones](claim-resolver-overview.md), como `{Context:CorrelationId}`. Para forzar el uso del valor predeterminado, establezca el atributo `AlwaysUseDefaultValue` en `true`. En el ejemplo siguiente, se agrega la notificación city (ciudad) con un valor predeterminado.
    
    ```xml
    <RelyingParty>
      ...
      <TechnicalProfile Id="PolicyProfile">
        ...
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="city" DefaultValue="Berlin" />
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```
  
1. Modifique el elemento InputClaims del perfil técnico UserInfoIssuer con las notificaciones que quiere proporcionar. Utilice el atributo `PartnerClaimType` para cambiar el nombre del valor devuelto de la notificación a la aplicación. En el ejemplo siguiente, se agrega la notificación city (ciudad) y se cambia el nombre de algunas de las notificaciones.

    ```xml
    <TechnicalProfile Id="UserInfoIssuer">
      ...
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="city" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="familyName" />
        <InputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <InputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email" />
      </InputClaims>
      ...
    </TechnicalProfile>
    ```

## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar un ejemplo de una directiva de punto de conexión UserInfo en [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end
