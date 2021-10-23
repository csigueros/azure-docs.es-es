---
title: Solucionadores de notificaciones en las directivas personalizadas
titleSuffix: Azure AD B2C
description: Aprenda a usar solucionadores de notificaciones en una directiva personalizada de Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 50b386c353e947aadbc196d3040f5cec2b995f82
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037878"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Acerca de los solucionadores de notificaciones en las directivas personalizadas de Azure Active Directory B2C

Los solucionadores de notificaciones de [directivas personalizadas](custom-policy-overview.md) de Azure Active Directory B2C (Azure AD B2C) proporcionan información de contexto sobre una solicitud de autorización, como el nombre de la directiva, el identificador de correlación de la solicitud, el idioma de la interfaz de usuario, etc.

Para utilizar un solucionador de notificaciones en una notificación de entrada o salida, se define una cadena **ClaimType** en el elemento [ClaimsSchema](claimsschema.md) y, a continuación, se establece **DefaultValue** en el solucionador de notificaciones del elemento de notificación de entrada o salida. Azure AD B2C lee el valor del solucionador de notificaciones y usa el valor en el perfil técnico.

En el ejemplo siguiente, un tipo de notificación llamada `correlationId` se define con un **DataType** de `string`.

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

En el perfil técnico, asigne el solucionador de notificaciones al tipo de notificación. Azure AD B2C rellena el valor del solucionador de notificaciones `{Context:CorrelationId}` en la notificación `correlationId` y envía la notificación al perfil técnico.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Tipos de solucionadores de notificaciones

Las secciones siguientes enumeran los solucionadores de notificaciones disponibles.

### <a name="culture"></a>Referencia cultural

| Notificación | Descripción | Ejemplo |
| ----- | ----------- | --------|
| {Culture:LanguageName} | Código ISO de dos letras para el idioma. | en |
| {Culture:LCID}   | El identificador de configuración regional del código de idioma. | 3082 |
| {Culture:RegionName} | Código ISO de dos letras para la región. | US |
| {Culture:RFC5646} | Código de idioma RFC5646. | es-ES |

### <a name="policy"></a>Directiva

| Notificación | Descripción | Ejemplo |
| ----- | ----------- | --------|
| {Policy:PolicyId} | Nombre de la directiva del usuario de confianza. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | El identificador de inquilino de la directiva del usuario de confianza. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | El identificador de objeto de inquilino de la directiva del usuario de confianza. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | El identificador de inquilino del marco de confianza. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Notificación | Descripción | Ejemplo |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |El parámetro de cadena de consulta `acr_values`. | N/D |
| {OIDC:ClientId} |El parámetro de cadena de consulta `client_id`. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |El parámetro de cadena de consulta `domain_hint`. | facebook.com |
| {OIDC:LoginHint} |  El parámetro de cadena de consulta `login_hint`. | someone@contoso.com |
| {OIDC:MaxAge} | El parámetro de cadena de consulta `max_age`. | N/D |
| {OIDC:Nonce} |El parámetro de cadena de consulta `Nonce`. | defaultNonce |
| {OIDC:Password}| Contraseña de usuario del [flujo de credenciales de contraseña del propietario del recurso](add-ropc-policy.md).| password1| 
| {OIDC:Prompt} | El parámetro de cadena de consulta `prompt`. | login |
| {OIDC:RedirectUri} |El parámetro de cadena de consulta `redirect_uri`. | https://jwt.ms |
| {OIDC:Resource} |El parámetro de cadena de consulta `resource`. | N/D |
| {OIDC:Scope} |El parámetro de cadena de consulta `scope`. | openid |
| {OIDC:Username}| Nombre de usuario del usuario del [flujo de credenciales de contraseña de propietario del recurso](add-ropc-policy.md).| emily@contoso.com| 

### <a name="context"></a>Context

| Notificación | Descripción | Ejemplo |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Versión del marco de experiencia de identidad (número de compilación).  | 1.0.507.0 |
| {Context:CorrelationId} | Identificador de correlación.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Hora y fecha en UTC.  | 10/10/2018 12:00:00 p. m. |
| {Context:DeploymentMode} |Modo de implementación de la directiva.  | Producción |
| {Context:HostName} | El nombre de host de la solicitud actual.  | contoso.b2clogin.com |
| {Context:IPAddress} | Dirección IP del usuario. | 11.111.111.11 |
| {Context:KMSI} | Indica si se ha seleccionado la casilla [Mantener la sesión iniciada](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi). |  true |

### <a name="claims"></a>Notificaciones 

| Notificación | Descripción | Ejemplo |
| ----- | ----------- | --------|
| {Claim:claim type} | Identificador de un tipo de notificación que ya se ha definido en la sección ClaimsSchema del archivo de directiva o del archivo de directiva principal.  Por ejemplo: `{Claim:displayName}` o `{Claim:objectId}`. | Valor de tipo de notificación.|


### <a name="oauth2-key-value-parameters"></a>Parámetros clave-valor de OAuth2

Cualquier nombre de parámetro incluido como parte de una solicitud OIDC u OAuth2 se puede asignar a una notificación en el recorrido del usuario. Por ejemplo, la solicitud de la aplicación puede incluir un parámetro de cadena de consulta con el nombre de `app_session`, `loyalty_number` o cualquier cadena de consulta personalizada.

| Notificación | Descripción | Ejemplo |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Parámetro de cadena de consulta. | Hawái |
| {OAUTH-KV:app_session} | Parámetro de cadena de consulta. | A3C5R |
| {OAUTH-KV:loyalty_number} | Parámetro de cadena de consulta. | 1234 |
| {OAUTH-KV:any custom query string} | Parámetro de cadena de consulta. | N/D |

### <a name="oauth2"></a>OAuth2

| Notificación | Descripción | Ejemplo |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | El token de acceso. | N/D |
| {oauth2:refresh_token} | El token de actualización. | N/D |


### <a name="saml"></a>SAML

| Notificación | Descripción | Ejemplo |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | Valor del elemento `AuthnContextClassRef` de la solicitud SAML. | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | Atributo `Format` del elemento `NameIDPolicy` de la solicitud SAML. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Issuer} |  Valor del elemento `Issuer` SAML de la solicitud SAML.| `https://contoso.com` |
| {SAML: AllowCreate} | Valor del atributo `AllowCreate` del elemento `NameIDPolicy` de la solicitud SAML. | True |
| {SAML:ForceAuthn} | Valor del atributo `ForceAuthN` del elemento `AuthnRequest` de la solicitud SAML. | True |
| {SAML:ProviderName} | Valor del atributo `ProviderName` del elemento `AuthnRequest` de la solicitud SAML.| Contoso.com |
| {SAML:RelayState} | El parámetro de cadena de consulta `RelayState`.| 
| {SAML:Subject} | El valor de `Subject` del elemento NameId de la solicitud de autenticación de SAML.| 

## <a name="using-claim-resolvers"></a>Uso de solucionadores de notificaciones

Puede usar solucionadores de notificaciones con los siguientes elementos:

| Elemento | Elemento | Configuración |
| ----- | ----------------------- | --------|
|Perfil técnico de Application Insights |`InputClaim` | |
|Perfil técnico de [Azure Active Directory](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico de [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico de [OpenID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico de [transformación de notificaciones](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico de un [proveedor RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Perfil técnico de un [proveedor de identidades SAML](identity-provider-generic-saml.md)| `OutputClaim`| 1, 2|
|Perfil técnico [autoafirmado](self-asserted-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Perfil técnico de [RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Configuración:
1. Los metadatos de `IncludeClaimResolvingInClaimsHandling` se deben establecer en `true`.
1. El atributo de notificaciones de entrada o salida `AlwaysUseDefaultValue` se debe establecer en `true`.

## <a name="claim-resolvers-samples"></a>Ejemplos de solucionadores de notificaciones

### <a name="restful-technical-profile"></a>Perfil técnico de RESTful

En un perfil técnico de [RESTful](restful-technical-profile.md), es posible que desee enviar el idioma del usuario, el nombre de la directiva, el ámbito y el identificador de cliente. Según las notificaciones, la API de REST puede ejecutar la lógica de negocios personalizada y, si es necesario, generar un mensaje de error localizado.

En el ejemplo siguiente se muestra un perfil técnico de RESTful con este escenario:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Inicio de sesión directo

Al usar solucionadores de notificaciones, puede rellenar previamente el nombre de inicio de sesión o puede iniciar sesión directamente en un proveedor de identidades sociales específico, como Facebook, LinkedIn o una cuenta de Microsoft. Para más información, consulte [Configuración de inicio de sesión directo con Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Personalización de la interfaz de usuario dinámica

Azure AD B2C le permite pasar parámetros de cadena de consulta a los puntos de conexión de la definición de contenido HTML para representar dinámicamente el contenido de la página. Por ejemplo, esta característica permite cambiar la imagen de fondo en la página de inicio de sesión o de registro de Azure AD B2C en función de un parámetro personalizado que se pasa desde la aplicación web o dispositivo móvil. Para más información, consulte [Azure Active Directory B2C: configuración de la interfaz de usuario con contenido dinámico utilizando directivas personalizadas](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri). También puede localizar la página HTML basándose en un parámetro de idioma, o bien puede cambiar el contenido basándose en el identificador de cliente.

El ejemplo siguiente pasa en la cadena de consulta un parámetro denominado **campaignId** con un valor de `Hawaii`, un código de **idioma** de `en-US` y una **aplicación** que representa el identificador de cliente:

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Como resultado, Azure AD B2C envía los parámetros anteriores a la página de contenido HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definición de contenido

En el parámetro `LoadUri` de [ContentDefinition](contentdefinitions.md), puede enviar resoluciones de notificaciones para extraer contenido de distintos lugares, en función de los parámetros utilizados.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Perfil técnico de Application Insights

Con Azure Application Insights y los solucionadores de notificaciones, puede obtener información sobre el comportamiento del usuario. En el perfil técnico de Application Insights, envía notificaciones de entrada que se conservan en Azure Application Insights. Para más información, consulte [Seguimiento del comportamiento del usuario dentro de los recorridos de Azure AD B2C mediante Application Insights](analytics-with-application-insights.md). El ejemplo siguiente envía el identificador de directiva, el identificador de correlación, el idioma y el identificador de cliente a Azure Application Insights.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Directiva del usuario de confianza

En un perfil técnico de directiva de [Usuario de confianza](relyingparty.md), es posible que quiera enviar el identificador de inquilino o el identificador de correlación a la aplicación de usuario de confianza en el JWT.

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
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
