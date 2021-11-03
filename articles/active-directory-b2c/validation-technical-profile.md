---
title: Definición de un perfil técnico de validación en una directiva personalizada
titleSuffix: Azure AD B2C
description: Valide las notificaciones mediante un perfil técnico de validación en una directiva personalizada de Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 3a394a793a6c681a73aace76d191f571db0a9089
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131032219"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definición de un perfil técnico de validación en una directiva personalizada en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un perfil técnico de validación es un perfil técnico normal de cualquier protocolo, como [Azure Active Directory](active-directory-technical-profile.md) o una [API de REST](restful-technical-profile.md). El perfil técnico de validación devuelve notificaciones de salida o el código de estado HTTP 4xx, con los siguientes datos. Para más información, consulte [Devolución de mensajes de error](restful-technical-profile.md#returning-validation-error-message).

```json
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

El ámbito de las notificaciones de salida de un perfil técnico de validación se limita al [perfil técnico autofirmado](self-asserted-technical-profile.md) que invoca el perfil técnico de validación y sus perfiles técnicos de validación. Si desea usar las notificaciones de salida en el siguiente paso de orquestación, agregue las notificaciones de salida al perfil técnico autofirmado que invoca el perfil técnico de validación.

Los perfiles técnicos de validación se ejecutan en la secuencia en que aparecen en el elemento **ValidationTechnicalProfiles**. Puede configurar un perfil técnico de validación si la ejecución de los perfiles técnicos de validación posteriores debe continuar, si el perfil técnico de validación genera un error o si es correcto.

Un perfil técnico de validación se puede ejecutar condicionalmente en función de las condiciones previas definidas en el elemento **ValidationTechnicalProfile**. Por ejemplo, puede comprobar si existe una notificación específica o si una notificación es igual o no al valor especificado.

Un perfil técnico autoafirmado puede definir un perfil técnico de validación que se use para validar algunas o todas las notificaciones de salida. Todas las notificaciones de entrada del perfil técnico referido tienen que aparecer en las notificaciones de salida del perfil técnico de validación de referencia.

> [!NOTE]
> Solo los perfiles técnicos autoafirmados pueden usar perfiles técnicos de validación. Si tiene que validar las notificaciones de salida de perfiles técnicos no autoafirmados, considere la posibilidad de usar un paso de orquestación adicional en el recorrido del usuario para dar cabida al perfil técnico a cargo de la validación.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

El elemento **ValidationTechnicalProfiles** contiene los elementos siguientes:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Un perfil técnico se usa para validar algunas o todas las notificaciones de salida del perfil técnico de referencia. |

El elemento **ValidationTechnicalProfile** contiene el atributo siguiente:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| ReferenceId | Sí | Un identificador de un perfil técnico que ya se ha definido en la directiva o en la directiva principal. |
|ContinueOnError|No| Indica si la validación de los perfiles técnicos de validación posteriores debe continuar si este perfil técnico de validación genera un error. Los valores posibles: `true` o `false` (de forma predeterminada, se detendrá el procesamiento de más perfiles de validación y se devolverá un error). |
|ContinueOnSuccess | No | Indica si la validación de los perfiles de validación posteriores debe continuar si este perfil técnico de validación es correcto. Valores posibles: `true` o `false`. El valor predeterminado es `true`, lo que significa que el procesamiento de los perfiles de validación adicionales continuará. |

El elemento **ValidationTechnicalProfile** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | Lista de las condiciones previas que deben cumplirse para que se ejecute el perfil técnico de validación. |

El elemento **Precondition** contiene el atributo siguiente:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| `Type` | Sí | El tipo de comprobación o consulta que hay que llevar a cabo para la condición previa. Se especifica `ClaimsExist` para asegurar que las acciones deben llevarse a cabo si las notificaciones especificadas existen en el conjunto de notificaciones actual del usuario, o `ClaimEquals` especifica que las acciones deben realizarse si la notificación especificada existe y su valor es igual al valor especificado. |
| `ExecuteActionsIf` | Sí | Indica si las acciones en la condición previa deben realizarse si la prueba es verdadera o falsa. |

El elemento **Precondition** contiene los elementos siguientes:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Valor | 1:n | Los datos que se usan en la comprobación. Si el tipo de esta comprobación es `ClaimsExist`, este campo especifica un ClaimTypeReferenceId para la consulta. Si el tipo de comprobación es `ClaimEquals`, este campo especifica un ClaimTypeReferenceId para la consulta. Mientras que otro elemento de valor contiene el valor que se va a comprobar.|
| Acción | 1:1 | La acción que debe realizarse si se cumple la comprobación de condición previa dentro de un paso de orquestación. El valor de la **Action** está establecido en `SkipThisValidationTechnicalProfile`. Especifica que el perfil técnico de validación asociado no debe ejecutarse. |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se usan estos perfiles técnicos de validación:

1. El primer perfil técnico de validación comprueba las credenciales de usuario y no continúa si se produce un error, como un nombre de usuario no válido o una contraseña incorrecta.
2. El siguiente perfil técnico de validación no se ejecuta si la notificación userType no existe, o si el valor de userType es `Partner`. El perfil técnico de validación intenta leer el perfil de usuario de la base de datos interna de clientes y continúa si se produce un error, por ejemplo, si el servicio de la API de REST no está disponible o si hay un error interno.
3. El último perfil técnico de validación no se ejecuta si la notificación userType no ha existido, o si el valor de userType es `Customer`. El perfil técnico de validación intenta leer el perfil de usuario de la base de datos interna del asociado y continúa si se produce un error, por ejemplo, si el servicio de la API de REST no está disponible o si hay un error interno.

```xml
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
