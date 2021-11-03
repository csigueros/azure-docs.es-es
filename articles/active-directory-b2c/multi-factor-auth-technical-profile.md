---
title: Perfiles técnicos de Azure AD MFA en directivas personalizadas
titleSuffix: Azure AD B2C
description: Referencia de directivas personalizadas para perfiles técnicos de Azure AD Multi-Factor Authentication (MFA) en Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 3570804af6dd59027907afa03e8ee432f1ed718f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131044900"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definición de un perfil técnico de Azure AD MFA en una directiva personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) proporciona compatibilidad con la verificación de un número de teléfono mediante Azure AD Multi-Factor Authentication (MFA). Use este perfil técnico para generar y enviar un código a un número de teléfono y, a continuación, verifique el código. El perfil técnico de Azure AD MFA también puede devolver un mensaje de error.  El perfil técnico de validación valida los datos que proporciona el usuario antes de que continúe el recorrido del usuario. Con el perfil técnico de validación, se muestra un mensaje de error en una página autoafirmada.

Este perfil técnico:

- No proporciona una interfaz para interactuar con el usuario. En su lugar, se llama a la interfaz de usuario desde un perfil técnico [autoafirmado](self-asserted-technical-profile.md) o desde un [control de pantalla](display-controls.md) como [perfil técnico de validación](validation-technical-profile.md).
- Usa el servicio Azure AD MFA para generar un código y enviarlo a un número de teléfono y, después, verifica el código.  
- Valida un número de teléfono a través de mensajes de texto.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `Proprietary`. El atributo **handler** debe contener el nombre completo del ensamblado del controlador de protocolo que usa Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

En el ejemplo siguiente se muestra un perfil técnico de Azure AD MFA:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Envío de SMS

El primer modo de este perfil técnico es generar un código y enviarlo. Pueden configurarse las opciones siguientes para este modo.

### <a name="input-claims"></a>Notificaciones de entrada

El elemento **InputClaims** contiene una lista de notificaciones para enviar a Azure AD MFA. También puede asignar el nombre de la notificación al nombre definido en el perfil técnico de MFA.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| userPrincipalName | Sí | Identificador del usuario que posee el número de teléfono. |
| phoneNumber | Sí | Número de teléfono al que se va a enviar un código de SMS. |
| companyName | No |Nombre de la empresa en el SMS. Si no se proporciona, se usa el nombre de la aplicación. |
| locale | No | Configuración regional del SMS. Si no se proporciona, se usa la configuración regional del explorador del usuario. |

El elemento **InputClaimsTransformations** puede contener una colección de elementos **InputClaimsTransformation** que se usan para modificar las notificaciones de entrada o generar otras nuevas antes del envío al servicio Azure AD MFA.

### <a name="output-claims"></a>Notificaciones de salida

El proveedor del protocolo de Azure AD MFA no devuelve ningún elemento **OutputClaims**, por lo que no es necesario especificar notificaciones de salida. Pero puede incluir notificaciones no devueltas por el proveedor de identidades de Azure AD MFA siempre que establezca el atributo `DefaultValue`.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Operación | Sí | Debe ser **OneWaySMS**.  |

#### <a name="ui-elements"></a>Elementos de interfaz de usuario

Los metadatos siguientes se pueden usar para configurar los mensajes de error que se muestran cuando se produce un error en envío de mensajes de texto. Los metadatos se deben configurar en el perfil técnico [autoafirmado](self-asserted-technical-profile.md). Los mensajes de error se pueden [localizar](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | No | Mensaje de error del usuario si el número de teléfono proporcionado no acepta SMS. |
| UserMessageIfInvalidFormat | No | Mensaje de error del usuario si el número de teléfono proporcionado no es válido. |
| UserMessageIfServerError | No | Mensaje de error del usuario si el servidor ha detectado un error interno. |
| UserMessageIfThrottled| No | Mensaje de error del usuario si se ha limitado una solicitud.|

### <a name="example-send-an-sms"></a>Ejemplo: envío de un SMS

En el ejemplo siguiente se muestra un perfil técnico de Azure AD MFA que se usa para enviar un código a través de SMS.

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Compruebe el código.

El segundo modo de este perfil técnico es verificar un código. Pueden configurarse las opciones siguientes para este modo.

### <a name="input-claims"></a>Notificaciones de entrada

El elemento **InputClaims** contiene una lista de notificaciones para enviar a Azure AD MFA. También puede asignar el nombre de la notificación al nombre definido en el perfil técnico de MFA.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Sí | El mismo número de teléfono que se usó anteriormente para enviar un código. También se usa para buscar una sesión de verificación del teléfono. |
| verificationCode  | Sí | Código de verificación que proporciona el usuario y que se va a verificar |

El elemento **InputClaimsTransformations** puede contener una colección de elementos **InputClaimsTransformation** que se usan para modificar las notificaciones de entrada o generar otras nuevas antes de llamar al servicio Azure AD MFA.

### <a name="output-claims"></a>Notificaciones de salida

El proveedor del protocolo de Azure AD MFA no devuelve ningún elemento **OutputClaims**, por lo que no es necesario especificar notificaciones de salida. Pero puede incluir notificaciones no devueltas por el proveedor de identidades de Azure AD MFA siempre que establezca el atributo `DefaultValue`.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Operación | Sí | Debe ser **Verify**. |

#### <a name="ui-elements"></a>Elementos de interfaz de usuario

Los metadatos siguientes se pueden usar para configurar los mensajes de error que se muestran cuando se produce un error en la comprobación de código. Los metadatos se deben configurar en el perfil técnico [autoafirmado](self-asserted-technical-profile.md). Los mensajes de error se pueden [localizar](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| No | Mensaje de error del usuario si este ha intentado un código de verificación demasiadas veces. |
| UserMessageIfServerError | No | Mensaje de error del usuario si el servidor ha detectado un error interno. |
| UserMessageIfThrottled| No | Mensaje de error del usuario si la solicitud está limitada.|
| UserMessageIfWrongCodeEntered| No| Mensaje de error del usuario si el código especificado para la verificación es incorrecto.|

### <a name="example-verify-a-code"></a>Ejemplo: verificación de un código

En el ejemplo siguiente se muestra un perfil técnico de Azure AD MFA que se usa para verificar el código.

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
