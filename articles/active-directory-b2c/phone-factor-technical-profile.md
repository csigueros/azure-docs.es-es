---
title: Definición de un perfil técnico de factor telefónico en una directiva personalizada
titleSuffix: Azure AD B2C
description: Defina un perfil técnico de factor telefónico en una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 17e21c28961346cd10b436adaa5eab8c8160bcd4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131012745"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definición de un perfil técnico de factor telefónico en una directiva personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) proporciona compatibilidad con la inscripción y comprobación de números de teléfono. Este perfil técnico:

- Proporciona una interfaz de usuario para interactuar con el usuario para comprobar o inscribir un número de teléfono.
- Admite llamadas de teléfono y mensajes de texto para validar el número de teléfono.
- Admite varios números de teléfono. El usuario puede seleccionar uno de los números de teléfono para comprobarlo.  
- Devuelve una notificación que indica si el usuario ha proporcionado un número de teléfono nuevo. Puede usar esta notificación para decidir si el número de teléfono se debe conservar en el perfil de usuario de Azure AD B2C.  
- Usa una [definición de contenido](contentdefinitions.md) para controlar el aspecto.

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `Proprietary`. El atributo **handler** debe contener el nombre completo del ensamblado de controlador de protocolo que usa Azure AD B2C para el factor telefónico: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

En el siguiente ejemplo se muestra un perfil técnico de factor telefónico para la inscripción y la validación:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Transformaciones de notificaciones de entrada

El elemento InputClaimsTransformations puede contener una colección de transformaciones de notificaciones de entrada que se usan para modificar las notificaciones de entrada o generar otras nuevas. La transformación de notificaciones de entrada siguiente genera una notificación `UserId` que se usa más adelante en la colección de notificaciones de entrada.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Notificaciones de entrada

El elemento InputClaims debe contener las siguientes notificaciones. También puede asignar el nombre de la notificación al nombre definido en el perfil técnico de factor telefónico. 

|  Tipo de datos| Obligatorio | Descripción |
| --------- | -------- | ----------- | 
| string| Sí | Identificador único para el usuario. El nombre de la notificación, o PartnerClaimType, debe establecerse en `UserId`. Esta notificación no debe contener información de identificación personal.|
| string| Sí | Lista de tipos de notificaciones. Cada notificación contiene un número de teléfono. Si alguna de las notificaciones de entrada no contiene un número de teléfono, se le pedirá al usuario que inscriba y compruebe un nuevo número de teléfono. El número de teléfono validado se devuelve como una notificación de salida. Si una de las notificaciones de entrada contiene un número de teléfono, se le pide al usuario que la compruebe. Si varias notificaciones de entrada contienen un número de teléfono, se pide al usuario que elija y compruebe uno de los números de teléfono. |

En el siguiente ejemplo se muestra el uso de varios números de teléfono. Para obtener más información, vea una [directiva de ejemplo](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Notificaciones de salida

El elemento OutputClaims contiene una lista de notificaciones devuelta por el perfil técnico de factor telefónico.

|  Tipo de datos| Obligatorio | Descripción |
|  -------- | ----------- |----------- |
| boolean | Sí | Indica si el usuario ha escrito el nuevo número de teléfono. El nombre de la notificación, o PartnerClaimType, debe establecerse en `newPhoneNumberEntered`.|
| string| Sí | El número de teléfono comprobado. El nombre de la notificación, o PartnerClaimType, debe establecerse en `Verified.OfficePhone`.|

El elemento OutputClaimsTransformations puede contener una colección de elementos OutputClaimsTransformation que se usan para modificar las notificaciones de salida o para generar nuevas.

## <a name="cryptographic-keys"></a>Claves de cifrado

El elemento **CryptographicKeys** no se usa.


## <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sí | El identificador de la [definición de contenido](contentdefinitions.md) asociada a este perfil técnico. |
| ManualPhoneNumberEntryAllowed| No | Especifica si un usuario puede o no escribir manualmente un número de teléfono. Valores posibles: `true` o `false` (valor predeterminado).|
| setting.authenticationMode | No | El método para validar el número de teléfono. Valores posibles: `sms`, `phone` o `mixed` (valor predeterminado).|
| setting.autodial| No| Especifique si el perfil técnico debe marcar automáticamente o enviar automáticamente un SMS. Valores posibles: `true` o `false` (valor predeterminado). El marcado automático requiere que los metadatos de `setting.authenticationMode` se establezcan en `sms` o `phone`. La colección de notificaciones de entrada debe tener un único número de teléfono. |

### <a name="ui-elements"></a>Elementos de interfaz de usuario

Los elementos de la interfaz de usuario de la página de autenticación de factor telefónico se pueden [localizar](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Pasos siguientes

- Consulte el paquete de inicio de [cuentas sociales y locales con MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa).
