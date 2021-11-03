---
title: Ejemplos de transformación de notificaciones generales para directivas personalizadas
titleSuffix: Azure AD B2C
description: Ejemplos de transformación de notificaciones generales para el esquema de Identity Experience Framework (IEF) de Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 7024079269481792b45b121cee97223580aabe28
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007284"
---
# <a name="general-claims-transformations"></a>Transformaciones de notificaciones generales

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se proporcionan ejemplos para usar las transformaciones de notificaciones generales del esquema de Identity Experience Framework en Azure Active Directory B2C (Azure AD B2C). Para más información, vea [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim

Copiar el valor de una notificación en otra. Ambas notificaciones deben ser del mismo tipo.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string, int | Tipo de la notificación que se va a copiar. |
| OutputClaim | outputClaim | string, int | El valor ClaimType que se genera después de que se haya invocado esta ClaimsTransformation. |

Use esta transformación de notificaciones para copiar un valor de una notificación de cadena o numérica a otra notificación. En el ejemplo siguiente se copia el valor de la notificación externalEmail a una notificación por correo electrónico.

```xml
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
    - **inputClaim**: bob@contoso.com
- Notificaciones de salida:
    - **outputClaim**: bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

Comprueba si el elemento **inputClaim** existe o no y establece **outputClaim** en true o false según corresponda.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Any | La notificación de entrada cuya existencia debe comprobarse. |
| OutputClaim | outputClaim | boolean | El valor ClaimType que se genera después de que se haya invocado esta ClaimsTransformation. |

Use esta transformación de notificaciones para comprobar si una notificación existe o contiene algún valor. El valor devuelto es un valor booleano que indica si existe la notificación. En el ejemplo siguiente se comprueba si existe la dirección de correo electrónico.

```xml
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **inputClaim**: someone@contoso.com
- Notificaciones de salida:
  - **outputClaim**: true

## <a name="hash"></a>Hash

Hash del texto sin formato proporcionado con el valor salt y un secreto. El algoritmo hash utilizado es SHA-256.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | La notificación de entrada que se va a cifrar. |
| InputClaim | salt | string | El parámetro salt. Puede crear un valor aleatorio, utilizando la transformación de notificaciones `CreateRandomString`. |
| InputParameter | randomizerSecret | string | Apunta a una **clave de directiva** de Azure AD B2C existente. Para crear una nueva clave de directiva: En su inquilino de Azure AD B2C, en **Administrar**, seleccione **Identity Experience Framework**. Seleccione **Claves de directiva** para ver las claves disponibles en el inquilino. Seleccione **Agregar**. En **Opciones**, seleccione **Manual**. Proporcione un nombre (es posible que se agregue automáticamente el prefijo *B2C_1A_* ). En el cuadro de texto **Secreto**, escriba el secreto que quiere usar, por ejemplo, 1234567890. En **Uso de claves**, seleccione **Firma**. Seleccione **Crear**. |
| OutputClaim | hash | string | El valor ClaimType que se genera después de que se haya invocado esta transformación de notificaciones. La notificación configurada en el elemento inputClaim `plaintext`. |

```xml
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **plaintext**: MyPass@word1
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Notificaciones de salida:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
