---
title: Ejemplos de la transformación de notificaciones de cuentas de redes sociales para directivas personalizadas
titleSuffix: Azure AD B2C
description: Ejemplos de transformación de notificaciones de cuentas de redes sociales para el esquema de Identity Experience Framework (IEF) de Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 03aecaabbe5a5b3948837db69bc7fc0dfe816b3e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131032361"
---
# <a name="social-accounts-claims-transformations"></a>Transformaciones de notificaciones de cuentas de redes sociales

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En Azure Active Directory B2C (Azure AD B2C), las identidades de cuentas de redes sociales se almacenan en un atributo `userIdentities` de un tipo de notificación **alternativeSecurityIdCollection**. Cada elemento de **alternativeSecurityIdCollection** especifica el emisor (nombre del proveedor de identidades, como facebook.com) y `issuerUserId`, que es un identificador de usuario único para el emisor.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

En este artículo, se proporcionan ejemplos de uso de las transformaciones de notificaciones de cuenta de red social del esquema Marco de experiencia de identidad en Azure AD B2C. Para más información, vea [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Crea una representación JSON de la propiedad alternativeSecurityId del usuario que puede usarse en las llamadas Azure Active Directory. Para más información, consulte el esquema de [AlternativeSecurityId](/graph/api/resources/alternativesecurityid).

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | string | Elemento ClaimType que especifica el identificador de usuario único usado por el proveedor de identidades de redes sociales. |
| InputClaim | identityProvider | string | Elemento ClaimType que especifica el nombre del proveedor de identidades de cuentas de redes sociales, como facebook.com. |
| OutputClaim | alternativeSecurityId | string | Elemento ClaimType que se produce después de invocar a ClaimsTransformation. Contiene información sobre la identidad de un usuario de cuenta de red social. El **emisor** es el valor de la notificación `identityProvider`. El elemento **issuerUserId** es el valor de la notificación `key` en formato Base 64. |

Use esta transformación de notificaciones para generar un elemento ClaimType `alternativeSecurityId`. Se usa en todos los perfiles técnicos de proveedores de identidades de redes sociales, como `Facebook-OAUTH`. La siguiente transformación de notificaciones recibe el id. de cuenta de red social del usuario y el nombre del proveedor de identidades. El resultado de este perfil técnico es un formato de cadena JSON que puede usarse en los servicios de directorio de Azure AD.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
    - **key**: 12334
    - **identityProvider**: facebook.com
- Notificaciones de salida:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Agrega un elemento `AlternativeSecurityId` a una notificación `alternativeSecurityIdCollection`.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Elemento ClaimType que se agregará a la notificación de salida. |
| InputClaim | collection | alternativeSecurityIdCollection | Elementos ClaimTypes que usa la transformación de notificaciones (si están disponibles en la directiva). Si se especifica, la transformación de notificaciones agrega el elemento `item` al final de la colección. |
| OutputClaim | collection | alternativeSecurityIdCollection | Elementos ClaimTypes que se producen después de que se invoque este elemento ClaimsTransformation. La nueva colección que contiene los dos elementos de entrada `collection` y `item`. |

En el ejemplo siguiente, se vincula una nueva identidad de red social a una cuenta existente. Para vincular una nueva identidad de red social:
1. En los perfiles técnicos **AAD-UserReadUsingAlternativeSecurityId** y **AAD-UserReadUsingObjectId**, se muestra la salida de la notificación **alternativeSecurityIds** del usuario.
1. Pida al usuario que inicie sesión con uno de los proveedores de identidades que no esté asociado a este usuario.
1. Al usar la transformación de notificaciones **CreateAlternativeSecurityId**, se crea un tipo de notificación **alternativeSecurityId** con el nombre `AlternativeSecurityId2`.
1. Realice una llamada a la transformación de notificaciones **AddItemToAlternativeSecurityIdCollection** para agregar la notificación **AlternativeSecurityId2** a la notificación **AlternativeSecurityIds** existente.
1. Hacer persistir la notificación **alternativeSecurityIds** en la cuenta de usuario

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Notificaciones de salida:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Muestra la lista de emisores de la notificación **alternativeSecurityIdCollection** en una nueva notificación **stringCollection**.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | El elemento ClaimType que se usará para obtener la lista de proveedores de identidades (emisor). |
| OutputClaim | identityProvidersCollection | stringCollection | Elementos ClaimTypes que se producen después de que se invoque este elemento ClaimsTransformation. Lista de proveedores de identidades asociados a la notificación de entrada alternativeSecurityIdCollection |

La siguiente transformación de notificaciones lee la notificación **alternativeSecurityIds** del usuario y extrae la lista de nombres de proveedores de identidades asociados a esa cuenta. Use el resultado de **identityProvidersCollection** para mostrar al usuario la lista de proveedores de identidades asociados a la cuenta. O bien, en la página de selección de proveedor de identidades, filtre la lista de proveedores de identidades según la notificación **identityProvidersCollection** de salida. Por lo tanto, el usuario puede vincular una nueva identidad de red social que aún no esté asociada a la cuenta.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Notificaciones de entrada:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Notificaciones de salida:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Quita un elemento **AlternativeSecurityId** de una notificación **alternativeSecurityIdCollection**.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | string | Elemento ClaimType que contiene el nombre del proveedor de identidades que se quitará de la colección. |
| InputClaim | collection | alternativeSecurityIdCollection | Elemento ClaimTypes que se usará en la transformación de notificaciones. La transformación de notificaciones quita el elemento identityProvider de la colección. |
| OutputClaim | collection | alternativeSecurityIdCollection | Elementos ClaimTypes que se producen después de que se invoque este elemento ClaimsTransformation. Nueva colección, después de quitar el elemento identityProvider de la colección. |

En el ejemplo siguiente, se desvincula una de las identidades de red social de una cuenta existente. Para desvincular una identidad de red social:
1. En los perfiles técnicos **AAD-UserReadUsingAlternativeSecurityId** y **AAD-UserReadUsingObjectId**, se muestra la salida de la notificación **alternativeSecurityIds** del usuario.
2. Pida al usuario que seleccione la cuenta de red social que quiere quitar de la lista de proveedores de identidades asociados a este usuario.
3. Realiza una llamada a un perfil técnico de transformación de notificaciones que llama a la transformación de notificaciones **RemoveAlternativeSecurityIdByIdentityProvider**, que ha quitado la identidad de red social seleccionada, mediante el nombre del proveedor de identidades.
4. Hace persistir la notificación **alternativeSecurityIds** en la cuenta de usuario.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
    - **identityProvider**: facebook.com
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Notificaciones de salida:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]