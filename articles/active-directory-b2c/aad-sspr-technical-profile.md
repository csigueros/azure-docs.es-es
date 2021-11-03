---
title: Perfiles técnicos de SSPR de Azure AD en directivas personalizadas
titleSuffix: Azure AD B2C
description: Referencia de directivas personalizadas para perfiles técnicos de SSPR de Azure AD en Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 8c1bac41a0c70a2d9dff2a8ce1ac5544ad687fe5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008377"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definición de un perfil técnico de SSPR de Azure AD en una directiva personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) proporciona compatibilidad para comprobar una dirección de correo electrónico para el autoservicio de restablecimiento de contraseña (SSPR). Use el perfil técnico de SSPR de Azure AD para generar y enviar un código a una dirección de correo electrónico y, a continuación, verificar el código. El perfil técnico de SSPR de Azure AD también puede devolver un mensaje de error. El perfil técnico de validación valida los datos que proporciona el usuario antes de que continúe el recorrido del usuario. Con el perfil técnico de validación, se muestra un mensaje de error en una página autoafirmada.

Este perfil técnico:

- No proporciona una interfaz para interactuar con el usuario. En su lugar, se llama a la interfaz de usuario desde un perfil técnico [autoafirmado](self-asserted-technical-profile.md) o desde un [control de pantalla](display-controls.md) como [perfil técnico de validación](validation-technical-profile.md).
- Usa el servicio de SSPR de Azure AD para generar y enviar un código a una dirección de correo electrónico y, a continuación, verifica el código.
- Valida una dirección de correo electrónico mediante un código de verificación.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `Proprietary`. El atributo **handler** debe contener el nombre completo del ensamblado del controlador de protocolo que usa Azure AD B2C:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

En el ejemplo siguiente se muestra un perfil técnico de SSPR de Azure AD:

```xml
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>Enviar correo electrónico

El primer modo de este perfil técnico es generar un código y enviarlo. Pueden configurarse las opciones siguientes para este modo.

### <a name="input-claims"></a>Notificaciones de entrada

El elemento **InputClaims** contiene una lista de notificaciones para enviar a SSPR de Azure AD. También puede asignar el nombre de la notificación al nombre definido en el perfil técnico de SSPR.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| emailAddress | Sí | Identificador del usuario que posee la dirección de correo electrónico. La propiedad `PartnerClaimType` de la notificación de entrada se debe establecer en `emailAddress`. |

El elemento **InputClaimsTransformations** puede contener una colección de elementos **InputClaimsTransformation** que se usan para modificar las notificaciones de entrada o generar otras nuevas antes del envío al servicio SSPR de Azure AD.

### <a name="output-claims"></a>Notificaciones de salida

El proveedor de protocolo de SSPR de Azure AD no devuelve ningún elemento **OutputClaims**, por lo que no es necesario especificar notificaciones de salida. Sin embargo, puede incluir notificaciones que no devuelve el proveedor de protocolo de SSPR de Azure AD, siempre que establezca el atributo `DefaultValue`.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Operación | Sí | Debe ser **SendCode**.  |

#### <a name="ui-elements"></a>Elementos de interfaz de usuario

Los metadatos siguientes se pueden usar para configurar los mensajes de error que se muestran cuando se produce un error en envío de mensajes de texto. Los metadatos se deben configurar en el perfil técnico [autoafirmado](self-asserted-technical-profile.md). Los mensajes de error se pueden [localizar](localization-string-ids.md#azure-ad-sspr).

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | No | Mensaje de error del usuario si el servidor ha detectado un error interno. |
| UserMessageIfThrottled| No | Mensaje de error del usuario si se ha limitado una solicitud.|

### <a name="example-send-an-email"></a>Ejemplo: envío de un correo electrónico

En el ejemplo siguiente se muestra un perfil técnico de SSPR de Azure AD que se usa para enviar un código mediante correo electrónico.

```xml
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Compruebe el código.

El segundo modo de este perfil técnico es verificar un código. Pueden configurarse las opciones siguientes para este modo.

### <a name="input-claims"></a>Notificaciones de entrada

El elemento **InputClaims** contiene una lista de notificaciones para enviar a SSPR de Azure AD. También puede asignar el nombre de la notificación al nombre definido en el perfil técnico de SSPR.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Sí | La misma dirección de correo electrónico que se usó anteriormente para enviar un código. También se usa para buscar una sesión de comprobación de correo electrónico. La propiedad `PartnerClaimType` de la notificación de entrada se debe establecer en `emailAddress`.|
| verificationCode  | Sí | Código de verificación que proporciona el usuario y que se va a verificar. La propiedad `PartnerClaimType` de la notificación de entrada se debe establecer en `verificationCode`. |

El elemento **InputClaimsTransformations** puede contener una colección de elementos **InputClaimsTransformation** que se usan para modificar las notificaciones de entrada o generar otras nuevas antes de llamar al servicio SSPR de Azure AD.

### <a name="output-claims"></a>Notificaciones de salida

El proveedor de protocolo de SSPR de Azure AD no devuelve ningún elemento **OutputClaims**, por lo que no es necesario especificar notificaciones de salida. Sin embargo, puede incluir notificaciones que no devuelve el proveedor de protocolo de SSPR de Azure AD, siempre que establezca el atributo `DefaultValue`.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Operación | Sí | Debe ser **VerifyCode** |

#### <a name="ui-elements"></a>Elementos de interfaz de usuario

Los metadatos siguientes se pueden usar para configurar los mensajes de error que se muestran cuando se produce un error en la comprobación de código. Los metadatos se deben configurar en el perfil técnico [autoafirmado](self-asserted-technical-profile.md). Los mensajes de error se pueden [localizar](localization-string-ids.md#azure-ad-sspr).

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | Mensaje que se mostrará al usuario si la sesión de verificación de código ha expirado. El código ha expirado o nunca se ha generado para un identificador determinado.|
|UserMessageIfInternalError | Mensaje de error del usuario si el servidor ha detectado un error interno.|
|UserMessageIfThrottled | Mensaje de error del usuario si se ha limitado una solicitud.|
|UserMessageIfVerificationFailedNoRetry | Mensaje que se mostrará al usuario si ha proporcionado un código no válido y no tiene permitido proporcionar el código correcto.|
|UserMessageIfVerificationFailedRetryAllowed | Mensaje que se mostrará al usuario si ha proporcionado un código no válido y tiene permitido proporcionar el código correcto.|

### <a name="example-verify-a-code"></a>Ejemplo: verificación de un código

En el ejemplo siguiente se muestra un perfil técnico de SSPR de Azure AD que se usa para verificar el código.

```xml
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```
