---
title: Comprobación de notificaciones con controles de pantalla
titleSuffix: Azure AD B2C
description: Obtenga información sobre cómo usar los controles de pantalla de Azure AD B2C para comprobar las notificaciones en los recorridos de usuario proporcionados por las directivas personalizadas.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 2a6a7a22d5d9ee19d396739b508de6afe9484c8e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037298"
---
# <a name="verification-display-control"></a>Control de pantalla de verificación

Use un [control de pantalla](display-controls.md) de verificación para comprobar una notificación, por ejemplo, una dirección de correo electrónico o un número de teléfono, con un código de verificación enviado al usuario.

## <a name="verificationcontrol-actions"></a>Acciones de VerificationControl

El control de pantalla de comprobación se compone de dos pasos (acciones):

1. Solicite un destino del usuario, como una dirección de correo electrónico o un número de teléfono, al que se debe enviar el código de verificación. Cuando el usuario selecciona el botón **Enviar código**, se ejecuta la acción **SendCode** del control de pantalla de verificación. La **acción SendCode** genera un código, construye el contenido que se va a enviar y lo envía al usuario. El valor de la dirección se puede rellenar previamente y servir como autenticación de segundo factor.

    ![Página de ejemplo de la acción de enviar código](media/display-control-verification/display-control-verification-email-action-01.png)

1. Una vez enviado el código, el usuario lee el mensaje, escribe el código de verificación en el control proporcionado por el control de pantalla y selecciona **Verificar código**. Al seleccionar **Verificar código**, se ejecuta la **acción VerifyCode** para verificar el código asociado a la dirección. Si el usuario selecciona **Enviar nuevo código**, la primera acción se ejecuta de nuevo.

    ![Página de ejemplo para la acción de comprobación de código](media/display-control-verification/display-control-verification-email-action-02.png)

## <a name="verificationcontrol-required-elements"></a>Elementos requeridos de la acción VerificationControl

La acción **VerificationControl** debe contener los siguientes elementos:

- El tipo de `DisplayControl` es `VerificationControl`.
- `DisplayClaims`
  - **Enviar a**: una o más notificaciones que especifican a dónde enviar el código de verificación. Por ejemplo, *correo electrónico* o *código de país* y *número de teléfono*.
  - **Código de verificación**: la notificación del código de verificación que proporciona el usuario una vez enviado el código. Esta notificación debe establecerse como necesaria y el `ControlClaimType` debe establecerse en `VerificationCode`.
- Notificación de salida (opcional) que se va a devolver a la página autoevaluada después de que el usuario complete el proceso de verificación. Por ejemplo, *correo electrónico* o *código de país* y *número de teléfono*. El perfil técnico autoafirmado utiliza las notificaciones para conservar los datos o propagar las notificaciones de salida en el siguiente paso de orquestación.
- Dos clases `Action` con los siguientes nombres:
  - **SendCode**: envía un código al usuario. Normalmente, esta acción contiene dos perfiles técnicos de validación, para generar un código y enviarlo.
  - **VerifyCode**: verifica el código. Esta acción normalmente contiene un único perfil técnico de validación.

En el ejemplo siguiente, se muestra un cuadro de texto de **correo electrónico** en la página. Cuando el usuario escribe su dirección de correo electrónico y selecciona **SendCode**, se desencadena la acción **SendCode** en el back-end de Azure AD B2C.

Después, el usuario especifica la acción **verificationCode** y selecciona **VerifyCode** para desencadenar la acción **VerifyCode** en el back-end. Si se pasan todas las validaciones, se considera que la clase **VerificationControl** se ha completado y el usuario puede continuar con el paso siguiente.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
