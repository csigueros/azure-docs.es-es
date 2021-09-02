---
title: Definición de un perfil técnico de Azure AD en una directiva personalizada
titleSuffix: Azure AD B2C
description: Defina un perfil técnico de Azure Active Directory en una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a1894fe63bcf24e19671a146cd16bb6fe59b0b
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2021
ms.locfileid: "122651880"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definición de un perfil técnico de Azure Active Directory en una directiva personalizada en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) proporciona compatibilidad con la administración de usuarios de Azure Active Directory. En este artículo se describen los detalles para que un perfil técnico interactúe con un proveedor de notificaciones que admita este protocolo estandarizado.

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `Proprietary`. El atributo **handler** debe contener el nombre completo del ensamblado de controlador de protocolo `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Siguiendo el [paquete de inicio de directivas personalizadas](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack), los perfiles técnicos de Azure AD incluyen el perfil técnico **AAD-Common**. Los perfiles técnicos de Azure AD no especifican el protocolo porque este está configurado en el perfil técnico **AAD-Common**:
 
- **AAD-UserReadUsingAlternativeSecurityId** y **AAD-UserReadUsingAlternativeSecurityId-NoError**: buscar una cuenta de redes sociales en el directorio.
- **AAD-UserWriteUsingAlternativeSecurityId**: crear una cuenta de redes sociales.
- **AAD-UserReadUsingEmailAddress**: buscar una cuenta local en el directorio.
- **AAD-UserWriteUsingLogonEmail**: crear una cuenta local.
- **AAD-UserWritePasswordUsingObjectId**: actualizar una contraseña de una cuenta local.
- **AAD-UserWriteProfileUsingObjectId**: actualizar un perfil de usuario de una cuenta local o social.
- **AAD-UserReadUsingObjectId**: leer un perfil de usuario de una cuenta local o social.
- **AAD-UserWritePhoneNumberUsingObjectId**: escribir el número de teléfono de MFA de una cuenta local o social

En el ejemplo siguiente se muestra el perfil técnico **AAD-Common**:

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

El elemento InputClaims contiene una notificación, que se usa para buscar una cuenta en el directorio o crear una nueva. Debe haber exactamente un elemento InputClaim en la colección de notificaciones de entrada para todos los perfiles técnicos de Azure AD. Es posible que tenga que asignar el nombre de la notificación definida en la directiva al nombre definido en Azure Active Directory.

Para leer, actualizar o eliminar una cuenta de usuario existente, la notificación de entrada es una clave que identifica de forma única la cuenta en el directorio de Azure AD. Por ejemplo, **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName** o **alternativeSecurityId**. 

Para crear una nueva cuenta de usuario, la notificación de entrada es una clave que identifica de forma única una cuenta local o federada. Por ejemplo, cuenta local: **signInNames.emailAddress** o **signInNames.userName**. En el caso de una cuenta federada: **alternativeSecurityId**.

El elemento [InputClaimsTransformations](technicalprofiles.md#input-claims-transformations) puede contener una colección de elementos de transformación de notificaciones de entrada que se usan para modificar las notificaciones de entrada o generar otras nuevas.

## <a name="outputclaims"></a>OutputClaims

El elemento **OutputClaims** contiene una lista de notificaciones devuelta por el perfil técnico de Azure AD. Es posible que tenga que asignar el nombre de la notificación definida en la directiva al nombre definido en Azure Active Directory. También puede incluir las notificaciones que Azure Active Directory no devuelve, siempre y cuando establezca el atributo `DefaultValue`.

El elemento [OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations) puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

Por ejemplo, el perfil técnico **AAD-UserWriteUsingLogonEmail** crea una cuenta local y devuelve las notificaciones siguientes:

- **objectId**, que es el identificador de la cuenta nueva
- **newUser**, que indica si el usuario es nuevo
- **authenticationSource**, que establece la autenticación en `localAccountAuthentication`
- **userPrincipalName**, que es el nombre principal de usuario de la cuenta nueva
- **signInNames.emailAddress**, que es el nombre de inicio de sesión de la cuenta, similar a la notificación de entrada **email**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

El elemento **PersistedClaims** contiene todos los valores que Azure AD debe conservar con la información de asignaciones posibles entre un tipo de notificación ya definido en la sección [ClaimsSchema](claimsschema.md) de la directiva y el nombre de atributo de Azure AD.

El perfil técnico **AAD-UserWriteUsingLogonEmail** crea una cuenta local y conserva las notificaciones siguientes:

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

El nombre de la notificación es el del atributo de Azure AD, a menos que se especifique el atributo **PartnerClaimType**, que contiene el nombre de atributo de Azure AD.

## <a name="requirements-of-an-operation"></a>Requisitos de una operación

- Debe haber exactamente un elemento **InputClaim** en el contenedor de notificaciones para todos los perfiles técnicos de Azure AD.
- En el [artículo sobre los atributos de perfil de usuario](user-profile-attributes.md) se describen los atributos del perfil de usuario de Azure AD B2C admitidos que puede usar en las notificaciones de entrada, las de salida y las persistentes. 
- Si la operación es `Write` o `DeleteClaims`, también debe aparecer en un elemento **PersistedClaims**.
- El valor de la notificación **userPrincipalName** debe tener el formato `user@tenant.onmicrosoft.com`.
- La notificación **displayName** es necesaria y no puede ser una cadena vacía.

## <a name="azure-ad-technical-provider-operations"></a>Operaciones de proveedores técnicos de Azure AD

### <a name="read"></a>Lectura

La operación **Read** lee los datos sobre una sola cuenta de usuario. El perfil técnico siguiente lee los datos sobre una cuenta de usuario mediante el valor objectId del usuario:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Escritura

La operación **Write** crea o actualiza una sola cuenta de usuario. El perfil técnico siguiente crea la cuenta de red social:

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

La operación **DeleteClaims** borra la información de una lista de notificaciones proporcionada. El perfil técnico siguiente elimina notificaciones:

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

La operación **DeleteClaimsPrincipal** elimina una sola cuenta de usuario del directorio. El perfil técnico siguiente elimina una cuenta de usuario del directorio mediante el nombre principal de usuario:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

El perfil técnico siguiente elimina una cuenta de usuario de red social mediante **alternativeSecurityId**:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Operación | Sí | La operación que se va a realizar. Valores posibles: `Read`, `Write`, `DeleteClaims` o `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | No | Genera un error si el objeto de usuario no existe en el directorio. Valores posibles: `true` o `false`. |
| RaiseErrorIfClaimsPrincipalAlreadyExists | No | Genera un error si el objeto de usuario ya existe. Valores posibles: `true` o `false`.|
| ApplicationObjectId | No | El identificador de objeto de aplicación para los atributos de extensión. Valor: ObjectId de una aplicación. Para más información, consulte [Atributos personalizados](user-flow-custom-attributes.md?pivots=b2c-custom-policy). |
| ClientId | No | El identificador de cliente para acceder al inquilino como un tercero. Para más información, vea [Uso de los atributos personalizados en una directiva de edición de perfil personalizada](user-flow-custom-attributes.md?pivots=b2c-custom-policy). |
| IncludeClaimResolvingInClaimsHandling  | No | En el caso de las notificaciones de entrada y salida, especifica si se incluye la [resolución de notificaciones](claim-resolver-overview.md) en el perfil técnico. Valores posibles: `true` o `false` (valor predeterminado). Si desea utilizar un solucionador de notificaciones en el perfil técnico, establézcalo en `true`. |

### <a name="ui-elements"></a>Elementos de interfaz de usuario
 
La configuración siguiente se puede usar para establecer el mensaje de error que se muestra cuando se produce un error. Los metadatos se deben configurar en el perfil técnico [autoafirmado](self-asserted-technical-profile.md). Los mensajes de error se pueden [localizar](localization.md).

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | No | Si se va a generar un error (vea la descripción del atributo RaiseErrorIfClaimsPrincipalAlreadyExists), especifique el mensaje para mostrar al usuario si el objeto de usuario ya existe. |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Si se va a generar un error (vea la descripción del atributo RaiseErrorIfClaimsPrincipalDoesNotExist), especifique el mensaje para mostrar al usuario si el objeto de usuario no existe. |


## <a name="next-steps"></a>Pasos siguientes

Consulte el siguiente artículo para obtener un ejemplo del uso de un perfil técnico de Azure AD:

- [Adición de notificaciones y personalización de la entrada del usuario mediante directivas personalizadas en Azure Active Directory B2C](configure-user-input.md)














