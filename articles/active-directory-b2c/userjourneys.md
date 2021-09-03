---
title: UserJourneys | Microsoft Docs
description: Especifique el elemento UserJourneys de una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/27/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee0fbab517a34f6986d20ea3271cb4325bf6aabd
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112981023"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Los recorridos del usuario especifican rutas de acceso explícitas con las que una directiva permite que una aplicación de usuario de confianza obtenga las notificaciones deseadas para un usuario. Se conduce al usuario por estas rutas de acceso para recuperar las notificaciones que se van a presentar al usuario de confianza. En otras palabras, los recorridos del usuario definen la lógica de negocios por la que pasa un usuario final mientras el marco de experiencia de identidad de Azure AD B2C procesa la solicitud.

Estos recorridos del usuario pueden considerarse como plantillas disponibles para satisfacer la necesidad principal de los diferentes usuarios de confianza de la comunidad de interés. Los recorridos del usuario facilitan la definición de la parte del usuario de confianza de una directiva. Una directiva puede definir varios recorridos del usuario. Cada recorrido del usuario es una secuencia de pasos de orquestación.

Para definir los recorridos del usuario compatibles con la directiva, se agrega un elemento **UserJourneys** debajo del elemento de nivel superior del archivo de directiva.

El elemento **UserJourneys** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Un recorrido del usuario que define todas las construcciones necesarias para un flujo de usuario completo. |

El elemento **UserJourney** contiene los siguientes atributos:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Identificador | Sí | Un identificador de un recorrido del usuario que se puede usar para hacer referencia a él desde otros elementos de la directiva. El elemento **DefaultUserJourney** de la [directiva del usuario de confianza](relyingparty.md) apunta a este atributo. |
| DefaultCpimIssuerTechnicalProfileReferenceId| No | Identificador de referencia del perfil técnico del emisor de tokens predeterminado. Por ejemplo, [Emisor de tokens JWT](userjourneys.md), [Emisor de tokens SAML](saml-issuer-technical-profile.md) o [Error personalizado de OAuth2](oauth2-error-technical-profile.md). Si el recorrido del usuario o el subrecorrido ya tiene otro paso de orquestación `SendClaims`, establezca el atributo `DefaultCpimIssuerTechnicalProfileReferenceId` en el perfil técnico del emisor del token del recorrido del usuario. |

El elemento **UserJourney** contiene los siguientes elementos:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | Lista de los perfiles técnicos de autorización. | 
| OrchestrationSteps | 1:n | Una secuencia de orquestación por la que hay que pasar para lograr una transacción correcta. Cada recorrido del usuario consta de una lista ordenada de los pasos de orquestación que se ejecutan en secuencia. Si se produce un error en algún paso, la transacción no prospera. |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

Supongamos que un usuario completó un elemento UserJourney y obtuvo un token de acceso o de identificador. Para administrar los recursos adicionales, como el [punto de conexión UserInfo](userinfo-endpoint.md), se debe identificar al usuario. Para comenzar este proceso, el usuario debe presentar el token de acceso emitido anteriormente como prueba de que se autenticó originalmente mediante una directiva de Azure AD B2C válida. Siempre debe haber presente un token válido para el usuario durante este proceso para asegurar que el usuario puede realizar esta solicitud. Los perfiles técnicos de autorización validan el token entrante y extraen las notificaciones del token.

El elemento **AuthorizationTechnicalProfiles** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | Lista de los perfiles técnicos de autorización. | 

El elemento **AuthorizationTechnicalProfile** contiene el atributo siguiente:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Sí | El identificador del perfil técnico que se va a ejecutar. |

En el ejemplo siguiente se muestra un elemento de recorrido del usuario con perfiles técnicos de autorización:

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

El recorrido del usuario se representa como una secuencia de orquestación por la que hay que pasar para lograr una transacción correcta. Si se produce un error en algún paso, la transacción no prospera. Estos pasos de orquestación hacen referencia tanto a los bloques de construcción como a los proveedores de notificaciones permitidos en el archivo de directiva. Cualquier paso de orquestación que se encargue de mostrar o representar una experiencia de usuario también hace referencia al identificador de la definición de contenido correspondiente.

Los pasos de orquestación se pueden ejecutar de forma condicional en función de las condiciones previas definidas en el elemento del paso de orquestación. Por ejemplo, puede intentar llevar a cabo un paso de orquestación solo si existe una notificación específica o si una notificación es igual o no al valor especificado.

Para especificar la lista ordenada de los pasos de orquestación, se agrega un elemento **OrchestrationSteps** como parte de la directiva. Este elemento es obligatorio.

El elemento **OrchestrationSteps** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Un paso de orquestación ordenada. |

El elemento **OrchestrationStep** contiene los siguientes atributos:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| `Order` | Sí | El orden de los pasos de orquestación. |
| `Type` | Sí | El tipo de paso de orquestación. Valores posibles: <ul><li>**ClaimsProviderSelection**: indica que el paso de orquestación presenta diversos proveedores de notificaciones al usuario para que seleccione uno.</li><li>**CombinedSignInAndSignUp**: indica que el paso de orquestación presenta una página combinada de inicio de sesión en el proveedor social y de registro en la cuenta local.</li><li>**ClaimsExchange**: indica que el paso de orquestación intercambia notificaciones con un proveedor de notificaciones.</li><li>**GetClaims**: especifica que el paso de orquestación debe procesar los datos de notificaciones enviados a Azure AD B2C del usuario de confianza mediante su configuración `InputClaims`.</li><li>**InvokeSubJourney**: indica que el paso de orquestación intercambia notificaciones con un [subrecorrido](subjourneys.md) (en versión preliminar pública).</li><li>**SendClaims**: indica que el paso de orquestación envía las notificaciones al usuario de confianza con un token emitido por un emisor de notificaciones.</li></ul> |
| ContentDefinitionReferenceId | No | El identificador de la [definición de contenido](contentdefinitions.md) asociada a este paso de orquestación. Normalmente, el identificador de referencia de la definición de contenido se define en el perfil técnico autoafirmado. Pero hay algunos casos en los que Azure AD B2C necesita mostrar contenido sin un perfil técnico. Hay dos ejemplos: si el tipo de paso de orquestación es uno de los siguientes: `ClaimsProviderSelection` o `CombinedSignInAndSignUp`, Azure AD B2C debe mostrar la selección del proveedor de identidades sin tener ningún perfil técnico. |
| CpimIssuerTechnicalProfileReferenceId | No | El tipo de paso de orquestación es `SendClaims`. Esta propiedad define el identificador de perfil técnico del proveedor de notificaciones que emite el token del usuario de confianza.  Si no aparece, no se crea ningún token para el usuario de confianza. |

El elemento **OrchestrationStep** puede contener los siguientes elementos:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | Una lista de las condiciones previas que deben cumplirse para que se ejecute el paso de orquestación. |
| ClaimsProviderSelections | 0:n | Una lista de las selecciones del proveedor de notificaciones para el paso de orquestación. |
| ClaimsExchanges | 0:n | Una lista de los intercambios de notificaciones para el paso de orquestación. |
| JourneyList | 0:1 | Una lista de candidatos de subrecorrido para el paso de orquestación. |

### <a name="preconditions"></a>Preconditions

Los pasos de orquestación se pueden ejecutar de forma condicional en función de las condiciones previas definidas en el paso de orquestación. El elemento `Preconditions` contiene una lista de condiciones previas que se deben evaluar. Cuando se cumple la evaluación de la condición previa, el paso de orquestación asociado pasa al siguiente paso de orquestación. 

Cada condición previa evalúa una única notificación. Hay dos tipos de condiciones previas:
 
- **Claims exist** (Existen notificaciones): especifica que las acciones deben realizarse si las notificaciones especificadas existen en el conjunto de notificaciones actual del usuario.
- **Claim equals** (La notificación es igual a): especifica que las acciones deben realizarse si la notificación especificada existe y su valor es igual al valor especificado. La comprobación realiza una comparación ordinal con distinción entre mayúsculas y minúsculas. Al comprobar el tipo de notificación booleano, use `True` o `False`.

Azure AD B2C evalúa las condiciones previas en orden de lista. Las condiciones previas basadas en orden permiten establecer el orden en el que se aplican las condiciones previas. La primera condición previa que se cumple invalida todas las siguientes. El paso de orquestación solo se ejecuta si no se cumplen todas las condiciones previas. 

El elemento **Preconditions** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Condición previa | 1:n | Condición previa que se va a evaluar. |

#### <a name="precondition"></a>Condición previa

El elemento **Precondition** contiene los atributos siguientes:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| `Type` | Sí | El tipo de comprobación o la consulta que hay que llevar a cabo para esta condición previa. El valor puede ser **ClaimsExist**, que especifica que se deben realizar las acciones si existen las notificaciones especificadas en el conjunto de notificaciones actual del usuario, o **ClaimEquals**, que especifica que las acciones deben realizarse si existe la notificación especificada y su valor es igual al valor especificado. |
| `ExecuteActionsIf` | Sí | Decide cómo se considera que se cumple la condición previa. Valores posibles: `true` (opción predeterminada) o `false`. Si el valor se establece en `true`, se considera que se ha cumplido cuando la notificación coincide con la condición previa.  Si el valor se establece en `false`, se considera que se ha cumplido cuando la notificación no coincide con la condición previa.  |

El elemento **Precondition** contiene los siguientes elementos:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Valor | 1:2 | Identificador de un tipo de notificación. La notificación ya se ha definido en la sección del esquema de notificaciones del archivo de directiva o del archivo de directiva primario. Cuando la condición previa es de tipo `ClaimEquals`, un segundo elemento `Value` contiene el valor que se va a comprobar. |
| Acción | 1:1 | Acción que se debe realizar si la se cumple la evaluación de la condición previa. Valor posible: `SkipThisOrchestrationStep`. El paso de orquestación asociado pasa al siguiente. |

#### <a name="preconditions-examples"></a>Ejemplos de condiciones previas

Con las siguientes condiciones previas se comprueba si existe el objectId del usuario. En el recorrido del usuario, el usuario ha optado por iniciar sesión con una cuenta local. Si existe el valor objectId, omita este paso de orquestación.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Con las siguientes condiciones previas se comprueba si el usuario ha iniciado sesión con una cuenta social. Se intenta encontrar la cuenta de usuario en el directorio. Si el usuario inicia sesión o se registra con una cuenta local, este paso de orquestación se omite.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Las condiciones previas pueden comprobar varias condiciones previas. En el ejemplo siguiente se comprueba si existe “objectId” o “email”. Si la primera condición es true, el recorrido se omite hasta el siguiente paso de orquestación.

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claims-provider-selection"></a>Selección del proveedor de notificaciones

La selección del proveedor de identidades permite a los usuarios seleccionar una acción de una lista de opciones. La selección del proveedor de identidades consta de un par de dos pasos de orquestación: 

1. **Botones**: comienza con el tipo de `ClaimsProviderSelection` o `CombinedSignInAndSignUp`, que contiene una lista de opciones entre las que puede elegir un usuario. El orden de las opciones dentro del elemento `ClaimsProviderSelections` controla el orden de los botones de inicio de sesión que se presentan al usuario.
2. **Acciones**: seguido del tipo de `ClaimsExchange`. ClaimsExchange contiene una lista de acciones. La acción es una referencia a un perfil técnico, como [OAuth2](oauth2-technical-profile.md), [OpenID Connect](openid-connect-technical-profile.md), [transformación de notificaciones](claims-transformation-technical-profile.md) o [autoafirmado](self-asserted-technical-profile.md). Cuando un usuario hace clic en uno de los botones, se ejecuta la acción correspondiente.

El elemento **ClaimsProviderSelections** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | Proporciona la lista de proveedores de notificaciones que se pueden seleccionar.|

El elemento **ClaimsProviderSelections** contiene los atributos siguientes:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| DisplayOption| No | Controla el comportamiento de un caso en el que solo hay disponible una selección del proveedor de notificaciones. Valores posibles: `DoNotShowSingleProvider` (valor predeterminado), el usuario se redirige inmediatamente al proveedor de identidades federadas. O `ShowSingleProvider`Azure AD B2C presenta la página de inicio de sesión con la selección del proveedor de identidades única. Para usar este atributo, la [versión de definición de contenido](page-layout.md) debe ser `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` y superior.|

El elemento **ClaimsProviderSelection** contiene los atributos siguientes:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | No | El identificador del intercambio de notificaciones, que se ejecuta en el siguiente paso de orquestación de la selección del proveedor de notificaciones. Hay que especificar este atributo o el atributo ValidationClaimsExchangeId, pero no ambos. |
| ValidationClaimsExchangeId | No | El identificador del intercambio de notificaciones, que se ejecuta en el paso de orquestación actual para validar la selección del proveedor de notificaciones. Hay que especificar este atributo o el atributo TargetClaimsExchangeId, pero no ambos. |

### <a name="claims-provider-selection-example"></a>Ejemplo de selección del proveedor de notificaciones

En el siguiente paso de orquestación, el usuario puede iniciar sesión con una cuenta local o con una cuenta de Facebook, LinkedIn, Twitter o Google. Si el usuario selecciona uno de los proveedores de identidades sociales, el segundo paso de orquestación se ejecuta con el intercambio de notificaciones seleccionado especificado en el atributo `TargetClaimsExchangeId`. El segundo paso de orquestación redirige al usuario al proveedor de identidades sociales para completar el proceso de inicio de sesión. Si el usuario decide iniciar sesión con la cuenta local, Azure AD B2C se mantiene en el mismo paso de orquestación (la misma página de registro o de inicio de sesión) y omite el segundo paso de orquestación.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

El elemento **ClaimsExchanges** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | En función del perfil técnico que se esté usando, puede ser que se redireccione al cliente según la selección del proveedor de notificaciones que se ha seleccionado o que se haga una llamada al servidor para intercambiar notificaciones. |

El elemento **ClaimsExchange** contiene los atributos siguientes:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Identificador | Sí | Identificador del paso de intercambio de notificaciones. El identificador se usa para hacer referencia al intercambio de notificaciones a partir de un paso de selección del proveedor de notificaciones en la directiva. |
| TechnicalProfileReferenceId | Sí | El identificador del perfil técnico que se va a ejecutar. |

## <a name="journeylist"></a>JourneyList

El elemento **JourneyList** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Candidato | 1:1 | Una referencia a un subrecorrido al que se va a llamar. |

### <a name="candidate"></a>Candidato

El elemento **Candidate** contiene los siguientes atributos:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Sí | El identificador del [subrecorrido](subjourneys.md) que se va a ejecutar. |
