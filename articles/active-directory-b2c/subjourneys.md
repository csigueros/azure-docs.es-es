---
title: Subrecorridos en Azure Active Directory B2C
description: Especifique el elemento de subrecorridos de una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 5cb3ebfbc3125e26f1b922ee43360c7527c7fb35
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063970"
---
# <a name="sub-journeys"></a>Subrecorridos

Los subrecorridos se pueden usar para organizar y simplificar el flujo de los pasos de orquestación dentro de un recorrido del usuario. Los [recorridos del usuario](userjourneys.md) especifican rutas de acceso explícitas con las que una directiva permite que una aplicación de usuario de confianza obtenga las notificaciones deseadas para un usuario. Se conduce al usuario por estas rutas de acceso para recuperar las notificaciones que se van a presentar al usuario de confianza. En otras palabras, los recorridos del usuario definen la lógica de negocios por la que pasa un usuario final mientras el marco de experiencia de identidad de Azure AD B2C procesa la solicitud. El recorrido del usuario se representa como una secuencia de orquestación por la que hay que pasar para lograr una transacción correcta. El elemento [ClaimsExchange](userjourneys.md#claimsexchanges) de un paso de orquestación está asociado a un solo [perfil técnico](technicalprofiles.md) que se ejecuta.

Un subrecorrido es una agrupación de pasos de orquestación que se pueden invocar en cualquier punto dentro de un recorrido del usuario. Puede usar subrecorridos para crear secuencias de pasos reutilizables o implementar una bifurcación que represente mejor la lógica de negocios.

## <a name="user-journey-branching"></a>Bifurcación del recorrido del usuario

Los subrecorridos se comportan como [recorridos del usuario](userjourneys.md), ya que ambos se representan como una secuencia de orquestación por la que hay que pasar para lograr una transacción correcta. Los recorridos de usuario se pueden invocar por sí mismos y requieren un paso SendClaims para ejecutarse. Los subrecorridos son componentes de los recorridos del usuario y no se pueden invocar de forma independiente y siempre se llaman desde un recorrido del usuario.

El componente clave de la bifurcación es permitir un mejor procesamiento de la lógica de negocios en un recorrido del usuario. Los pasos de orquestación comunes se agrupan en fragmentos individuales que se invocan por separado. Un subrecorrido puede simplificar un recorrido en el que varios pasos de orquestación están acoplados (con las mismas condiciones previas). Un subrecorrido solo se llama desde un recorrido del usuario y no debe llamar a otro subrecorrido.

Existen dos tipos de subrecorrido:

- **Call**: devuelve el control al autor de la llamada. El subrecorrido se ejecuta y, a continuación, se devuelve el control al paso de orquestación que se ejecuta actualmente en el recorrido del usuario.
- **Transfer**: transfiere el control al subrecorrido (bifurcación irreversible). El subrecorrido debe tener un paso SendClaims para devolver las notificaciones a la aplicación de usuario de confianza.

## <a name="example-scenarios"></a>Escenarios de ejemplo

### <a name="call-sub-journey"></a>Llamada al subrecorrido

Un subrecorrido de llamada resulta útil en los escenarios siguientes:

- Restricción de acceso por edad: En el caso de la restricción de acceso por edad, hay muchos componentes compartidos entre los recorridos del usuario. La bifurcación permite a compilar los elementos comunes en componentes que se pueden compartir.  
- Consentimiento parental: La bifurcación facilita el diseño del consentimiento parental, ya que nos permite acceder a las notificaciones del recorrido de usuario que el menor ha ejecutado, junto con la posibilidad de bifurcar a un recorrido de usuario de consentimiento parental, después de detectar que el usuario requiere consentimiento. 
- Registrarse para iniciar sesión: Considere un escenario en el que ya existe un usuario en el directorio, pero puede que haya olvidado que había creado una cuenta. En tal caso, en lugar de indicar al usuario que las credenciales que ha escrito ya existen y obligarle a reiniciar el recorrido, puede ser conveniente que la directiva pueda realizar el cambio desde el flujo de registro a un flujo de inicio de sesión para ese usuario.  

### <a name="transfer-sub-journey"></a>Subrecorrido de transferencia

Un subrecorrido de transferencia resulta útil en los escenarios siguientes:

- Visualización de una página de bloque.
- Pruebas A/B mediante el enrutamiento de la solicitud a un subrecorrido para ejecutar y emitir un token.

## <a name="adding-a-subjourneys-element"></a>Adición de un elemento SubJourneys

A continuación, se muestra un ejemplo de un elemento `SubJourney` de tipo `Call`, que devuelve el control al recorrido del usuario.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

A continuación, se muestra un ejemplo de un elemento `SubJourney` de tipo `Transfer`, que devuelve un token a la aplicación de usuario de confianza.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Invocación de un paso de subrecorrido

Se usa un nuevo paso de orquestación del tipo `InvokeSubJourney` para ejecutar un subrecorrido. A continuación, se muestra un ejemplo que incluye todos los elementos de ejecución de este paso de orquestación.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Un subrecorrido no debe llamar a otro subrecorrido.

## <a name="components"></a>Componentes

Para definir los subrecorridos compatibles con la directiva, agregue un elemento **SubJourneys** bajo el elemento de nivel superior del archivo de directiva.

El elemento **UserJourneys** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| SubJourney | 1:n | Un subrecorrido que define todas las construcciones necesarias para un flujo de usuario completo. |

El elemento **SubJourneys** contiene los siguientes atributos:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Identificador | Sí | El identificador del subrecorrido que puede usar el recorrido del usuario para hacer referencia al subrecorrido de la directiva. El elemento **SubJourneyReferenceId** del elemento [Candidate](userjourneys.md#journeylist) apunta a este atributo. |
| Tipo | Sí | Valores posibles: `Call` o `Transfer`. Para más información, consulte [Bifurcación del recorrido del usuario](#user-journey-branching).|

El elemento **UserJourney** contiene el elemento siguiente:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Una secuencia de orquestación por la que hay que pasar para lograr una transacción correcta. Cada recorrido del usuario consta de una lista ordenada de los pasos de orquestación que se ejecutan en secuencia. Si se produce un error en algún paso, la transacción no prospera. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Para una lista completa de los elementos de paso de orquestación, vea [UserJourneys](userjourneys.md).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [UserJourneys](userjourneys.md)
