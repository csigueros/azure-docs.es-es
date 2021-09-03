---
title: Ejemplos de transformación de notificaciones de JSON para directivas personalizadas
titleSuffix: Azure AD B2C
description: Ejemplos de transformación de notificaciones de JSON para el esquema de Identity Experience Framework (IEF) de Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/27/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d6381b6355f22ab5b60fe734b0b100c29bd6505
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112981059"
---
# <a name="json-claims-transformations"></a>Transformaciones de notificaciones de JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se proporcionan ejemplos de uso de las transformaciones de notificaciones de JSON del esquema Identity Experience Framework en Azure Active Directory B2C (Azure AD B2C). Para más información, vea [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

Use valores de notificaciones o constantes para generar una cadena JSON. La cadena de ruta de acceso que sigue a la notación de puntos se usa para indicar dónde insertar los datos en una cadena JSON. Después de dividirlos por puntos, los números enteros se interpretan como el índice de una matriz JSON y los no enteros se interpretan como el índice de un objeto JSON.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Cualquier cadena que siga a la notación de puntos | string | JsonPath del JSON donde se insertará el valor de la notificación. |
| InputParameter | Cualquier cadena que siga a la notación de puntos | string | JsonPath del JSON donde se insertará el valor de cadena constante. |
| OutputClaim | outputClaim | string | Cadena JSON generada. |

### <a name="example-1"></a>Ejemplo 1

En el ejemplo siguiente se genera una cadena JSON basada en el valor de notificación de "email" y "OTP", así como cadenas de constantes.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

La siguiente transformación de notificaciones genera una notificación de cadena JSON que será el cuerpo de la solicitud enviada a SendGrid (un proveedor de correo electrónico de terceros). La estructura del objeto JSON se define mediante los identificadores de la notación de puntos de InputParameters y TransformationClaimTypes del objeto InputClaims. Los números de la notación de puntos implican matrices. Los valores provienen de los valores de InputClaims y de las propiedades "value" de InputParameters.

- Notificaciones de entrada:
  - **email**, tipo de notificación de transformación **personalizations.0.to.0.email**: "someone@example.com"
  - **otp**, tipo de notificación de transformación **personalizations.0.dynamic_template_data.otp** "346349"
- Parámetro de entrada:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **from.email**: "service@contoso.com"
  - **personalizations.0.subject** "Código de comprobación del correo electrónico de la cuenta de Contoso"
- Notificación de salida:
  - **requestBody**: Valor JSON

```json
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

### <a name="example-2"></a>Ejemplo 2

En el ejemplo siguiente se genera una cadena JSON basada en los valores de notificación, así como cadenas de constantes.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

La siguiente transformación de notificaciones genera una notificación de cadena JSON que será el cuerpo de la solicitud enviada a una API de REST. La estructura del objeto JSON se define mediante los identificadores de la notación de puntos de InputParameters y TransformationClaimTypes del objeto InputClaims. Los valores provienen de los valores de InputClaims y de las propiedades "value" de InputParameters.

- Notificaciones de entrada:
  - **email**, tipo de notificación de transformación **customerEntity.email**: "john.s@contoso.com"
  - **objectId**, tipo de notificación de transformación **customerEntity.userObjectId** "01234567-89ab-cdef-0123-456789abcdef"
  - **givenName**, tipo de notificación de transformación **customerEntity.firstName** "John"
  - **surname**, tipo de notificación de transformación **customerEntity.lastName** "Smith"
- Parámetro de entrada:
  - **customerEntity.role.name**: "Administrator"
  - **customerEntity.role.id** 1
- Notificación de salida:
  - **requestBody**: Valor JSON

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obtener un elemento especificado de datos JSON.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | ClaimTypes que usa la transformación de notificaciones para obtener el elemento. |
| InputParameter | claimToExtract | string | El nombre del elemento JSON que se va a extraer. |
| OutputClaim | extractedClaim | string | ClaimType que se genera después de que se haya invocado esta transformación de notificaciones, con el valor especificado en el parámetro de entrada _claimToExtract_. |

En el ejemplo siguiente, se muestra que la transformación de notificaciones extrajo el elemento `emailAddress` a partir de los datos JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parámetro de entrada:
    - **claimToExtract**: emailAddress
- Notificaciones de salida:
  - **extractedClaim**: someone@example.com

La transformación de notificaciones GetClaimFromJson obtiene un único elemento a partir de los datos JSON. En el ejemplo anterior, emailAddress. Para obtener displayName, cree otra transformación de notificaciones. Por ejemplo:

```xml
<ClaimsTransformation Id="GetDispalyNameClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="displayName" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Notificaciones de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parámetro de entrada:
    - **claimToExtract**: displayName
- Notificaciones de salida:
  - **extractedClaim**: Someone

## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obtener una lista de los elementos especificados a partir de datos JSON.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | ClaimTypes que usa la transformación de notificaciones para obtener las notificaciones. |
| InputParameter | errorOnMissingClaims | boolean | Especifica si se emite un error cuando falta una de las notificaciones. |
| InputParameter | includeEmptyClaims | string | Especifica si se incluyen notificaciones vacías. |
| InputParameter | jsonSourceKeyName | string | Nombre de la clave del elemento. |
| InputParameter | jsonSourceValueName | string | Nombre del valor del elemento. |
| OutputClaim | Colección | string, int, boolean y datetime |Lista de las notificaciones que se van a extraer. El nombre de la notificación debe ser igual al especificado en la notificación de entrada _jsonSourceClaim_. |

En el ejemplo siguiente, la transformación de notificaciones extrae estas notificaciones: email (string), displayName (string), membershipNum (int), active (boolean) y birthdate (datetime) a partir de los datos JSON.

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Notificaciones de entrada:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Parámetros de entrada:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Notificaciones de salida:
  - **email**: "someone@example.com"
  - **displayName**: "Someone"
  - **membershipNum**: 6353399
  - **active**: true
  - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtiene un elemento numérico especificado (long) a partir de datos JSON.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | ClaimTypes usadas por la transformación de notificaciones para obtener la notificación. |
| InputParameter | claimToExtract | string | El nombre del elemento JSON que se va a extraer. |
| OutputClaim | extractedClaim | long | El valor ClaimType que se genera después de que se haya invocado esta ClaimsTransformation, con el valor del elemento especificado en los parámetros de entrada _claimToExtract_. |

En el ejemplo siguiente, la transformación de notificaciones extrae el elemento `id` a partir de datos JSON.

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- Parámetros de entrada
    - **claimToExtract**: id
- Notificaciones de salida:
    - **extractedClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Obtiene el primer elemento de los datos JSON.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | ClaimTypes usados por la transformación de notificaciones para obtener el elemento de los datos JSON. |
| OutputClaim | key | string | La clave del primer elemento de los datos JSON. |
| OutputClaim | value | string | El valor del primer elemento de los datos JSON. |

En el ejemplo siguiente, la transformación de notificaciones extrae el primer elemento (givenName) de los datos JSON.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- Notificaciones de salida:
  - **clave**: givenName
  - **value**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtiene el primer elemento de una matriz de datos JSON.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | ClaimTypes usados por la transformación de notificaciones para obtener el elemento de la matriz JSON. |
| OutputClaim | extractedClaim | string | El valor ClaimType que se genera después de que se haya invocado esta ClaimsTransformation, con el primer elemento de la matriz JSON. |

En el ejemplo siguiente, la transformación de notificaciones extrae el primer elemento (dirección de correo electrónico) de la matriz JSON `["someone@example.com", "Someone", 6353399]`.

```xml
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- Notificaciones de salida:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Convierte los datos XML en formato JSON.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | string | ClaimTypes usados por la transformación de notificaciones para convertir los datos XML en formato JSON. |
| OutputClaim | json | string | El valor ClaimType que se genera después de que se haya invocado esta ClaimsTransformation, con los datos en formato JSON. |

```xml
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

En el ejemplo siguiente, la transformación de notificaciones convierte los siguientes datos XML en formato JSON.

#### <a name="example"></a>Ejemplo
Notificación de entrada:

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Notificación de salida:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


