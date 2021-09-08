---
title: Ejemplos de transformación de notificaciones de enteros para directivas personalizadas
titleSuffix: Azure AD B2C
description: Ejemplos de transformación de notificaciones de entero para el esquema de Identity Experience Framework (IEF) de Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7583698bd78ecb3a56d00b1af845f6d5f94c78c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724898"
---
# <a name="integer-claims-transformations"></a>Transformaciones de notificaciones de entero

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se proporcionan ejemplos de uso de las transformaciones de notificaciones de enteros del esquema Identity Experience Framework en Azure Active Directory B2C (Azure AD B2C). Para más información, vea [ClaimsTransformations](claimstransformations.md).

## <a name="adjustnumber"></a>AdjustNumber

Aumenta o disminuye una notificación numérica y devuelve una nueva notificación.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | int | Tipo de notificación, que contiene el número que se va a aumentar o disminuir. Si el valor de la notificación `inputClaim` es NULL, se usa el valor predeterminado de 0. |
| InputParameter | Operador | string | Valores posibles: `INCREMENT` (opción predeterminada) o `DECREMENT`.|
| OutputClaim | outputClaim | int | El tipo de notificación que se genera después de que se haya invocado esta transformación de notificaciones. |

Use esta transformación de notificación para aumentar o disminuir un valor de notificación numérico. 

```xml
<ClaimsTransformation Id="UpdateSteps" TransformationMethod="AdjustNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="steps" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="INCREMENT" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="steps" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Ejemplo 1

- Notificaciones de entrada:
    - **inputClaim**: 1
- Parámetros de entrada:
    - **Operador**: INCREMENT
- Notificaciones de salida:
    - **outputClaim**: 2

### <a name="example-2"></a>Ejemplo 2

- Notificaciones de entrada:
    - **inputClaim**: NULL
- Parámetros de entrada:
    - **Operador**: INCREMENT
- Notificaciones de salida:
    - **outputClaim**: 1


## <a name="assertnumber"></a>AssertNumber

Determina si una notificación numérica es mayor, menor, igual o no igual que un número. 

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | int | Primera notificación numérica que se va a comparar si es mayor, menor, igual o no igual que el segundo número. Un valor nulo inicia una excepción. |
| InputParameter | CompareToValue | int | Segundo número que se va a comparar si es mayor, menor, igual o no igual que el primer número. |
| InputParameter | Operador | string | Valores posibles: `LESSTHAN`, `GREATERTHAN`, `GREATERTHANOREQUAL`, `LESSTHANOREQUAL`, `EQUAL` y `NOTEQUAL`. |
| InputParameter | throwError | boolean | Especifica si esta aserción debe producir un error si el resultado de la comparación es `true`. Valores posibles: `true` (opción predeterminada) o `false`. <br />&nbsp;<br />Cuando se establece en `true` (modo de aserción) y el resultado de la comparación es `true`, se producirá una excepción. Cuando se establece en `false` (modo de evaluación), el resultado es un nuevo tipo de notificación booleana con un valor de `true` o `false`.| 
| OutputClaim | outputClaim | boolean | Si `ThrowError` se establece en `false`, esta notificación de salida contiene `true`, o `false` según el resultado de la comparación. |

### <a name="assertion-mode"></a>Modo de aserción

Cuando el parámetro de entrada `throwError` es `true` (valor predeterminado), la transformación de notificaciones **AssertNumber** siempre se ejecuta desde un [perfil técnico de validación](validation-technical-profile.md) llamado por un [perfil técnico autofirmado](self-asserted-technical-profile.md). 

Los metadatos de un perfil técnico autoafirmado **AssertNumberError** controlan el mensaje de error que el perfil técnico presenta al usuario. Los mensajes de error se pueden [localizar](localization-string-ids.md#claims-transformations-error-messages).

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="AssertNumberError">You've reached the maximum logon attempts</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

Para obtener más información sobre cómo llamar a la transformación de notificaciones en un modo de aserción, vea las transformaciones de notificaciones [AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal), [AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) y [AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan).

### <a name="assertion-mode-example"></a>Ejemplo del modo de aserción

En el ejemplo siguiente se impone que el número de intentos es mayor que cinco. La transformación de notificaciones produce un error según el resultado de la comparación. 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```

- Notificaciones de entrada:
    - **inputClaim**: 10
- Parámetros de entrada:
    - **Operator**: GREATERTHAN
    - **CompareToValue**: 5
    - **throwError**: true
- Resultado: aparece un error

### <a name="evaluation-mode-example"></a>Ejemplo del modo de evaluación

En el ejemplo siguiente se evalúa si el número de intentos es mayor que cinco. La notificación de salida contiene un valor booleano según el resultado de la comparación. La transformación de notificaciones no producirá un error. 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="attemptsCountExceeded" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Notificaciones de entrada:
    - **inputClaim**: 10
- Parámetros de entrada:
    - **Operator**: GREATERTHAN
    - **CompareToValue**: 5
    - **throwError**: false
- Notificaciones de salida:
    - **outputClaim**: true


## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Convierte a un tipo de datos Long en un tipo de datos String.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | Tipo de notificación que se va a convertir en cadena. |
| OutputClaim | outputClaim | string | El tipo de notificación que se genera después de que se haya invocado esta transformación de notificaciones. |

En este ejemplo, la notificación `numericUserId` con un tipo de valor de Long se convierte en una notificación `UserId` con un tipo de valor de String.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
    - **inputClaim**: 12334 (Long)
- Notificaciones de salida:
    - **outputClaim**: "12334" (cadena)

