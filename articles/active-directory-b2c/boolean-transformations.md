---
title: Ejemplos de transformación de notificaciones booleanas para directivas personalizadas
titleSuffix: Azure AD B2C
description: Ejemplos de transformación de notificaciones booleanas para el esquema de Identity Experience Framework (IEF) de Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 7ec9e12d8418845e163bd1cc2e578814720c96f2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008130"
---
# <a name="boolean-claims-transformations"></a>Transformaciones de notificaciones booleanas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se proporcionan ejemplos de uso de las transformaciones de notificaciones booleanas del esquema de Identity Experience Framework en Azure Active Directory B2C (Azure AD B2C). Para más información, vea [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Realiza una operación And de dos inputClaims booleanos y establece el elemento outputClaim con el resultado de la operación.

| Elemento  | TransformationClaimType  | Tipo de datos  | Notas |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | El primer ClaimType que se va a evaluar. |
| InputClaim | inputClaim2  | boolean | El segundo ClaimType que se va a evaluar. |
|OutputClaim | outputClaim | boolean | Los ClaimTypes que se generan después de que se haya invocado esta transformación de notificaciones (true o false). |

La siguiente transformación de notificaciones explica cómo aplicar And a dos argumentos ClaimType booleanos: `isEmailNotExist` y `isSocialAccount`. La notificación de salida `presentEmailSelfAsserted` está establecida en `true` si el valor de ambas notificaciones de entrada es `true`. En un paso de orquestación, puede usar una condición previa para preestablecer una página autoafirmada solo si la dirección de correo electrónico de la cuenta de redes sociales está vacía.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-andclaims"></a>Ejemplo de AndClaims

- Notificaciones de entrada:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Notificaciones de salida:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Comprueba que los valores booleanos de dos notificaciones son iguales y lanza una excepción si no lo son.

| Elemento | TransformationClaimType  | Tipo de datos  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | ClaimType que se va a afirmar. |
| InputParameter |valueToCompareTo | boolean | El valor que se va a comparar (true o false). |

La transformación de notificaciones **AssertBooleanClaimIsEqualToValue** siempre se ejecuta desde un [perfil técnico de validación](validation-technical-profile.md) llamado por un [perfil técnico autoafirmado](self-asserted-technical-profile.md). Los metadatos de un perfil técnico autoafirmado **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** controlan el mensaje de error que el perfil técnico presenta al usuario. Los mensajes de error se pueden [localizar](localization-string-ids.md#claims-transformations-error-messages).

![Ejecución de AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

La siguiente transformación de notificaciones explica cómo comprobar el valor de un argumento ClaimType booleano con un valor `true`. Si el valor del ClaimType `accountEnabled` es false, se desencadena un mensaje de error.

```xml
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


El perfil técnico de validación `login-NonInteractive` llama a la transformación de notificaciones `AssertAccountEnabledIsTrue`.

```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

El perfil técnico autoafirmado llama al perfil técnico **login-NonInteractive** de validación.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example-of-assertbooleanclaimisequaltovalue"></a>Ejemplo de AssertBooleanClaimIsEqualToValue

- Notificaciones de entrada:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Resultado: aparece un error

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Comprueba que el valor booleano de una notificación es igual a `true` o `false` y que devuelve el resultado de la compresión.

| Elemento | TransformationClaimType  | Tipo de datos  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | boolean | ClaimType que se va a afirmar. |
| InputParameter |valueToCompareTo | boolean | El valor que se va a comparar (true o false). |
| OutputClaim | compareResult | boolean | El valor ClaimType que se genera después de que se haya invocado esta ClaimsTransformation. |

La siguiente transformación de notificaciones explica cómo comprobar el valor de un argumento ClaimType booleano con un valor `true`. Si el valor de ClaimType `IsAgeOver21Years` es igual a `true`, la transformación de notificación devuelve `true`. De lo contrario, `false`.

```xml
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-comparebooleanclaimtovalue"></a>Ejemplo de CompareBooleanClaimToValue

- Notificaciones de entrada:
    - **inputClaim**: false
- Parámetros de entrada:
    - **valueToCompareTo**: true
- Notificaciones de salida:
    - **compareResult**: false

## <a name="notclaims"></a>NotClaims

Realiza una operación Not del inputClaim booleano y establece el elemento outputClaim con el resultado de la operación.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | La notificación que se va a realizar. |
| OutputClaim | outputClaim | boolean | Elementos ClaimTypes que se producen después de que se invoque este elemento ClaimsTransformation (true o false). |

Use esta transformación de notificaciones para realizar la negación lógica en una notificación.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-notclaims"></a>Ejemplo de NotClaims

- Notificaciones de entrada:
    - **inputClaim**: false
- Notificaciones de salida:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

Procesa una operación Or de dos inputClaims booleanos y establece el elemento outputClaim con el resultado de la operación.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | El primer ClaimType que se va a evaluar. |
| InputClaim | inputClaim2 | boolean | El segundo ClaimType que se va a evaluar. |
| OutputClaim | outputClaim | boolean | Los ClaimTypes que se generan después de que se haya invocado esta ClaimsTransformation (true o false). |

La siguiente transformación de notificaciones explica cómo aplicar `Or` a dos argumentos ClaimType booleanos. En el paso de orquestación, puede usar una condición previa para preestablecer una página autoafirmada si el valor de una de las notificaciones es `true`.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-orclaims"></a>Ejemplo de OrClaims

- Notificaciones de entrada:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Notificaciones de salida:
    - **outputClaim**: true
