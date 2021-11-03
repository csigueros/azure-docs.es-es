---
title: Ejemplos de transformación de notificaciones StringCollection para directivas personalizadas
titleSuffix: Azure AD B2C
description: Ejemplos de transformación de notificaciones StringCollection para el esquema de Identity Experience Framework (IEF) de Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 26c4eb195275fe7bf48a188df3f15d0f2cb61db8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131046420"
---
# <a name="stringcollection-claims-transformations"></a>Transformaciones de notificaciones StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se proporcionan ejemplos para usar las transformaciones de notificaciones de la colección de cadenas del esquema de Identity Experience Framework en Azure Active Directory B2C (Azure AD B2C). Para más información, vea [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Agrega una notificación de cadena a una nueva notificación stringCollection de valores únicos.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Elemento ClaimType que se agregará a la notificación de salida. |
| InputClaim | collection | stringCollection | El colección de cadena que se agregará a la notificación de salida. Si la colección contiene elementos, la transformación de notificaciones copia los elementos y agrega el elemento al final de la notificación de la colección de salida. |
| OutputClaim | collection | stringCollection | Valor ClaimType que se genera después de que se haya invocado a esta transformación de notificaciones, con el valor especificado en la notificación de entrada. |

Use esta transformación de notificaciones para agregar una cadena a una clase stringCollection nueva o existente. Se usa normalmente en un perfil técnico **AAD-UserWriteUsingAlternativeSecurityId**. Antes de crear una nueva cuenta de redes sociales, la transformación de notificaciones **CreateOtherMailsFromEmail** lee el ClaimType y agrega el valor al ClaimType **otherMails**.

La siguiente transformación de notificaciones agrega el ClaimType **email** al ClaimType **otherMails**.

```xml
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **collection**: ["someone@outlook.com"]
  - **item**: "admin@contoso.com"
- Notificaciones de salida:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Agrega un parámetro de cadena a una nueva notificación stringCollection de valores únicos.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | El colección de cadena que se agregará a la notificación de salida. Si la colección contiene elementos, la transformación de notificaciones copia los elementos y agrega el elemento al final de la notificación de la colección de salida. |
| InputParameter | item | string | El valor que se agregará a la notificación de salida. |
| OutputClaim | collection | stringCollection | El valor ClaimType que se genera después de que se haya invocado esta transformación de notificaciones, con el valor especificado en el parámetro de entrada. |

Use esta transformación de notificaciones para agregar un valor de cadena a una clase stringCollection nueva o existente. En el ejemplo siguiente se agrega una dirección de correo electrónico constante (admin@contoso.com) a la notificación **otherMails**.

```xml
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **collection**: ["someone@outlook.com"]
- Parámetros de entrada
  - **item**: "admin@contoso.com"
- Notificaciones de salida:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtiene el primer elemento de la colección de la cadena proporcionada.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | ClaimTypes que usa la transformación de notificaciones para obtener el elemento. |
| OutputClaim | extractedItem | string | Elementos ClaimTypes que se producen después de que se invoque este elemento ClaimsTransformation. El primer elemento de la colección. |

En el ejemplo siguiente se lee la notificación **otherMails** y se devuelve el primer elemento de la notificación de **correo electrónico**.

```xml
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **collection**: ["someone@outlook.com", "someone@contoso.com"]
- Notificaciones de salida:
  - **extractedItem**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Comprueba si un tipo de notificación StringCollection contiene un elemento.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Tipo de la notificación que se va a buscar. |
|InputParameter|item|string|Valor que se va a buscar.|
|InputParameter|ignoreCase|string|Especifica si la comparación distingue entre mayúsculas y minúsculas en las cadenas que se están comparando.|
| OutputClaim | outputClaim | boolean | El valor ClaimType que se genera después de que se haya invocado esta ClaimsTransformation. Indicador booleano si la colección contiene una cadena de este tipo. |

En el siguiente ejemplo se comprueba si el tipo de notificación stringCollection `roles` contiene el valor **admin**.

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Notificaciones de entrada:
    - **inputClaim**: ["reader", "author", "admin"]
- Parámetros de entrada:
    - **item**: "Admin"
    - **ignoreCase**: "true"
- Notificaciones de salida:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Comprueba si un tipo de notificación StringCollection contiene un valor de notificación.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Tipo de la notificación que se va a buscar. |
| InputClaim | item|string| Tipo de notificación que contiene el valor que se va a buscar.|
|InputParameter|ignoreCase|string|Especifica si la comparación distingue entre mayúsculas y minúsculas en las cadenas que se están comparando.|
| OutputClaim | outputClaim | boolean | El valor ClaimType que se genera después de que se haya invocado esta ClaimsTransformation. Indicador booleano si la colección contiene una cadena de este tipo. |

En el siguiente ejemplo se comprueba si el tipo de notificación del elemento stringCollection `roles` contiene el valor del tipo de notificación `role`.

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Notificaciones de entrada:
    - **collection**: ["reader", "author", "admin"]
    - **item**: "Admin"
- Parámetros de entrada:
    - **ignoreCase**: "true"
- Notificaciones de salida:
    - **outputClaim**: "true"
