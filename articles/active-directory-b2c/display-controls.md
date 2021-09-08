---
title: Referencia de los controles de visualización
titleSuffix: Azure AD B2C
description: Referencia para los controles de visualización de Azure AD B2C. Use controles de visualización para personalizar los recorridos de usuario definidos en las directivas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4b6ea85cfaf32ab9ba2c04c40befbe1fbf8ce915
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445477"
---
# <a name="display-controls"></a>Controles de visualización

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un **control de visualización** es un elemento de la interfaz de usuario que tiene una funcionalidad especial e interactúa con el servicio en el entorno back-end de Azure Active Directory B2C (Azure AD B2C). Permite al usuario realizar acciones en la página que invocan un [perfil técnico de validación](validation-technical-profile.md) en el entorno back-end. Los controles de visualización se muestran en la página y se hace referencia a ellos mediante un [perfil técnico de aserción automática](self-asserted-technical-profile.md).

En la imagen siguiente se muestra una página de registro de aserción automática con dos controles de visualización que validan una dirección de correo electrónico principal y secundaria.

![Ejemplo de control de visualización representado](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Prerequisites

 En la sección [Metadatos](self-asserted-technical-profile.md#metadata) de un [perfil técnico de aserción automática](self-asserted-technical-profile.md), el elemento [ContentDefinition](contentdefinitions.md) al que se hace referencia debe tener `DataUri` establecido en la versión 2.0.0 o superior del contrato de página. Por ejemplo:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definir controles de visualización

El elemento **DisplayControl** contiene los atributos siguientes:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Identificador | Sí | Un identificador que se usa para el control de visualización. Se puede [referenciar](#referencing-display-controls). |
| UserInterfaceControlType | Sí | Tipo del control de visualización. Actualmente se admite [VerificationControl](display-control-verification.md) |

El elemento **DisplayControl** contiene los elementos siguientes:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** se usa para rellenar previamente el valor de las notificaciones que se van a recopilar del usuario. Para obtener más información, vea el elemento [InputClaims](technicalprofiles.md#input-claims). |
| DisplayClaims | 0:1 | Las **DisplayClaims** se usan para representar las notificaciones que se van a recopilar del usuario. Para obtener más información, vea el elemento [DisplayClaim](technicalprofiles.md#displayclaim).|
| OutputClaims | 0:1 | **OutputClaims** se usa para representar notificaciones que se van a guardar temporalmente para este **DisplayControl**. Para obtener más información, vea el elemento [OutputClaims](technicalprofiles.md#output-claims).|
| Acciones | 0:1 | **Actions** se usa para enumerar los perfiles técnicos de validación que se van a invocar para las acciones del usuario que se producen en front-end. |

### <a name="input-claims"></a>Notificaciones de entrada

En un control de pantalla, puede usar **InputClaims** para rellenar previamente el valor de las notificaciones que se van a recopilar del usuario en la página. Todas las **InputClaimsTransformations** se pueden definir en el perfil técnico de aserción automática que hace referencia a este control de visualización.

En el ejemplo siguiente se rellena previamente la dirección de correo electrónico que se va a comprobar con la dirección ya presente.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Notificaciones de visualización

Cada tipo de control de visualización requiere un conjunto diferente de notificaciones de visualización, [notificaciones de salida](#output-claims) y [acciones](#display-control-actions) que deben realizarse.

De forma similar a las **notificaciones de visualización** definidas en un [perfil técnico de aserción automática](self-asserted-technical-profile.md#display-claims), las notificaciones de visualización representan las notificaciones que se van a recopilar del usuario dentro del control de visualización. El elemento **ClaimType** referenciado debe especificar el elemento **UserInputType** para un tipo de entrada de usuario admitido por Azure AD B2C, como `TextBox` o `DropdownSingleSelect`. Si **Action** requiere un valor de notificación de visualización, establezca el atributo **Required** en `true` para forzar al usuario a proporcionar un valor para esa notificación de pantalla específica.

Determinadas notificaciones de visualización son necesarias para algunos tipos de controles de visualización. Por ejemplo, **VerificationCode** es necesario para el control de visualización de tipo **VerificationControl**. Use el atributo **ControlClaimType** para especificar qué DisplayClaim se designa para la notificación requerida. Por ejemplo:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Notificaciones de salida

Las **notificaciones de salida** de un control de visualización no se envían al siguiente paso de orquestación. Solo se guardan de forma temporal para la sesión de control de visualización actual. Las distintas acciones del mismo control de visualización pueden compartir estas notificaciones temporales.

Para propagar la salida de las notificaciones al siguiente paso de orquestación, use **OutputClaims** del perfil técnico de aserción automática real que hace referencia a este control de visualización.

### <a name="display-control-actions"></a>Acciones de control de visualización

Las **acciones** de un control de visualización son procedimientos que se producen en el entorno back-end de Azure AD B2C cuando un usuario realiza una acción determinada en el lado del cliente (el explorador). Por ejemplo, las validaciones que se deben realizar cuando el usuario selecciona un botón en la página.

Una acción define una lista de **perfiles técnicos de validación**. Se usan para validar algunas o todas las notificaciones de visualización del control de visualización. El perfil técnico de validación valida la entrada del usuario y puede devolver un error al usuario. Puede usar **ContinueOnError**, **ContinueOnSuccess** y **condiciones previas** en la acción de control de visualización de forma similar a como se usan en los [perfiles técnicos de validación](validation-technical-profile.md) de un perfil técnico de aserción automática.

#### <a name="actions"></a>Acciones

El elemento **Actions** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Acción | 1:n | Lista de acciones que se van a ejecutar. |

#### <a name="action"></a>Acción

El elemento **Action** contiene el atributo siguiente:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Identificador | Sí | Tipo de operación. Valores posibles: `SendCode` o `VerifyCode`. El valor `SendCode` envía un código al usuario. Esta acción puede contener dos perfiles técnicos de validación: uno para generar un código y otro para enviarlo. El valor `VerifyCode` comprueba el código escrito por el usuario en el cuadro de texto de entrada. |

El elemento **Action** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | Identificadores de perfiles técnicos que se usan para validar algunas o todas las notificaciones para mostrar del perfil técnico de referencia. Todas las notificaciones de entrada del perfil técnico al que se hace referencia tienen que aparecer en las notificaciones para mostrar del perfil técnico de referencia. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

El elemento **ValidationClaimsExchange** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| ValidationClaimsExchangeTechnicalProfile | 1:n | Perfil técnico que se va a usar para validar algunas o todas las notificaciones para mostrar del perfil técnico de referencia. |

El elemento **ValidationClaimsExchangeTechnicalProfile** contiene el siguiente atributo:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Sí | Un identificador de un perfil técnico que ya se ha definido en la directiva o en la directiva principal. |

El elemento **ValidationClaimsExchangeTechnicalProfile** contiene el siguiente elemento:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | Lista de las condiciones previas que deben cumplirse para que se ejecute el perfil técnico de validación. |

El elemento **Precondition** contiene los atributos siguientes:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| `Type` | Sí | El tipo de comprobación o consulta que hay que llevar a cabo para la condición previa. Valores posibles: `ClaimsExist` o `ClaimEquals`. `ClaimsExist` especifica que las acciones deben realizarse si las notificaciones especificadas existen en el conjunto de notificaciones actual del usuario. `ClaimEquals` especifica que las acciones deben realizarse si la notificación especificada existe y su valor es igual al valor especificado. |
| `ExecuteActionsIf` | Sí | Indica si las acciones en la condición previa deben realizarse si la prueba es verdadera o falsa. |

El elemento **Precondition** contiene los elementos siguientes:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Valor | 1:n | Los datos que se usan en la comprobación. Si el tipo de esta comprobación es `ClaimsExist`, este campo especifica un ClaimTypeReferenceId para la consulta. Si el tipo de comprobación es `ClaimEquals`, este campo especifica un ClaimTypeReferenceId para la consulta. Especifique el valor que se va a comprobar en otro elemento de valor.|
| Acción | 1:1 | La acción que debe realizarse si se cumple la comprobación de condición previa dentro de un paso de orquestación. El valor de **Action** se establece en `SkipThisValidationTechnicalProfile`, que especifica que el perfil técnico de validación asociado no debe ejecutarse. |

En el ejemplo siguiente se envía y se comprueba la dirección de correo electrónico mediante un [perfil técnico de autoservicio de restablecimiento de contraseña de Azure AD](aad-sspr-technical-profile.md).

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

En el ejemplo siguiente se envía un código por correo electrónico o SMS, en función de la selección del usuario de la notificación **mfaType** con condiciones previas.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Referencia a controles de visualización

Se hace referencia a los [controles de visualización](self-asserted-technical-profile.md#display-claims) en la notificación de visualización del [perfil técnico de aserción automática](self-asserted-technical-profile.md).

Por ejemplo:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener ejemplos de uso del control de visualización, vea: 

- [Verificación de correo electrónico personalizado con Mailjet](custom-email-mailjet.md)
- [Verificación de correo electrónico personalizado con SendGrid](custom-email-sendgrid.md)
