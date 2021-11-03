---
title: Habilitación de la verificación de la contraseña de un solo uso (OTP)
titleSuffix: Azure AD B2C
description: Obtenga información sobre cómo configurar un escenario de contraseña de un solo uso (OTP) mediante directivas personalizadas de Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 4815eec021e4ebecda065667dca4568ded703ac5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131044881"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definición de un perfil técnico de una contraseña de un solo uso en una directiva personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) proporciona compatibilidad para administrar la generación y verificación de una contraseña de un solo uso. Use un perfil técnico para generar un código y, a continuación, verifique el código más adelante.

El perfil técnico de la contraseña de un solo uso también puede devolver un mensaje de error durante la verificación del código. Puede diseñar la integración con la contraseña de un solo uso mediante el uso de un **perfil técnico de validación**. Un perfil técnico de validación llama al perfil técnico de la contraseña de un solo uso para verificar un código. El perfil técnico de validación valida los datos que proporciona el usuario antes de que continúe el recorrido del usuario. Con el perfil técnico de validación, se muestra un mensaje de error en una página autoafirmada.

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `Proprietary`. El atributo **handler** debe contener el nombre completo del ensamblado del controlador de protocolo que usa Azure AD B2C:

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

En el ejemplo siguiente se muestra un perfil técnico de contraseña de un solo uso:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generación de código

El primer modo de este perfil técnico es para generar un código. A continuación, se muestran las opciones que se pueden configurar para este modo. Durante la sesión se realiza un seguimiento de los códigos generados y de los intento. 

### <a name="input-claims"></a>Notificaciones de entrada

El elemento **InputClaims** contiene una lista de las notificaciones que se deben enviar al proveedor de protocolo de contraseña de un solo uso. También puede asignar el nombre de la notificación al nombre que se define a continuación.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| identificador | Sí | Identificador que se usa identificar al usuario que debe verificar el código más adelante. Normalmente se usa como el identificador del destino en el que se entrega el código, por ejemplo, la dirección de correo electrónico o el número de teléfono. |

El elemento **InputClaimsTransformations** puede contener una colección de elementos **InputClaimsTransformation** que se usan para modificar las notificaciones de entrada o generar otras nuevas antes del envío al proveedor de protocolo de contraseña de un solo uso.

### <a name="output-claims"></a>Notificaciones de salida

El elemento **InputClaims** contiene una lista de las notificaciones que genera el proveedor de protocolo de contraseña de un solo uso. También puede asignar el nombre de la notificación al nombre que se define a continuación.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| otpGenerated | Sí | Código generado cuya sesión se administra mediante Azure AD B2C. |

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="metadata"></a>Metadatos

La configuración siguiente se puede usar para establecer el modo de generación de código:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | No | Tiempo en segundos hasta la expiración del código. Mínimo: `60`; máximo: `1200`; valor predeterminado: `600`. Cada vez que se proporciona un código (el mismo código que usa `ReuseSameCode`, o un código nuevo), se amplía la expiración del código. También se usa para establecer el tiempo de espera de reintento (una vez alcanzado el máximo de intentos, el usuario no podrá intentar obtener nuevos códigos hasta que expire este tiempo). |
| CodeLength | No | Longitud del código. El valor predeterminado es `6`. |
| CharacterSet | No | Juego de caracteres del código, con formato para usarse en una expresión regular. Por ejemplo, `a-z0-9A-Z`. El valor predeterminado es `0-9`. El juego de caracteres debe incluir un mínimo de 10 caracteres diferentes en el conjunto especificado. |
| NumRetryAttempts | No | Número de intentos de verificación antes de que el código se considere no válido. El valor predeterminado es `5`. |
| NumCodeGenerationAttempts | No | Número máximo de intentos de generación de código por identificador. Si no se especifica, el valor predeterminado es 10. |
| Operación | Sí | La operación que se va a realizar. Valor posible: `GenerateCode`. |
| ReuseSameCode | No | Indica si se debe proporcionar el mismo código en lugar de generar un código nuevo cuando el código proporcionado no ha expirado y sigue siendo válido. El valor predeterminado es `false`.  |



### <a name="example"></a>Ejemplo

En el siguiente ejemplo, `TechnicalProfile` se usa para generar un código:

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Compruebe el código.

El segundo modo de este perfil técnico es verificar un código. A continuación, se muestran las opciones que se pueden configurar para este modo.

### <a name="input-claims"></a>Notificaciones de entrada

El elemento **InputClaims** contiene una lista de las notificaciones que se deben enviar al proveedor de protocolo de contraseña de un solo uso. También puede asignar el nombre de la notificación al nombre que se define a continuación.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| identificador | Sí | Identificador que se usa para identificar al usuario que ha generado previamente un código. Normalmente se usa como el identificador del destino en el que se entrega el código, por ejemplo, la dirección de correo electrónico o el número de teléfono. |
| otpToVerify | Sí | Código de verificación que proporciona el usuario. |

El elemento **InputClaimsTransformations** puede contener una colección de elementos **InputClaimsTransformation** que se usan para modificar las notificaciones de entrada o generar otras nuevas antes del envío al proveedor de protocolo de contraseña de un solo uso.

### <a name="output-claims"></a>Notificaciones de salida

No se proporcionan notificaciones de salida durante la verificación del código de este proveedor de protocolo.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="metadata"></a>Metadatos

La configuración siguiente se puede usar para establecer el modo de comprobación de código:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Operación | Sí | La operación que se va a realizar. Valor posible: `VerifyCode`. |


### <a name="ui-elements"></a>Elementos de interfaz de usuario

Los metadatos siguientes se pueden usar para configurar los mensajes de error que se muestran cuando se produce un error en la comprobación de código. Los metadatos se deben configurar en el perfil técnico [autoafirmado](self-asserted-technical-profile.md). Los mensajes de error se pueden [localizar](localization-string-ids.md#one-time-password-error-messages).

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | No | Mensaje que se mostrará al usuario si la sesión de verificación de código ha expirado. Es posible que el código haya expirado o que nunca se haya generado para un identificador determinado. |
| UserMessageIfMaxRetryAttempted | No | Mensaje que se mostrará al usuario si ha superado el número máximo de intentos de verificación permitidos. |
| UserMessageIfMaxNumberOfCodeGenerated | No | Mensaje que se mostrará al usuario si la generación de código ha superado el número máximo de intentos permitidos. |
| UserMessageIfInvalidCode | No | Mensaje que se mostrará al usuario si ha proporcionado un código no válido. |
| UserMessageIfVerificationFailedRetryAllowed | No | Mensaje que se mostrará al usuario si ha proporcionado un código no válido y tiene permitido proporcionar el correcto.  |
|UserMessageIfSessionConflict|No| Mensaje que se mostrará al usuario si no se puede comprobar el código.|

### <a name="example"></a>Ejemplo

En el siguiente ejemplo, `TechnicalProfile` se usa para verificar un código:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Pasos siguientes

Consulte el siguiente artículo para un ejemplo de uso de un perfil técnico de contraseña de un solo uso con comprobación de correo electrónico personalizado:

- Verificación de correo electrónico personalizado en Azure Active Directory B2C ([Mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md))

