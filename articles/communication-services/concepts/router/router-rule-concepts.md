---
title: Conceptos del motor de reglas del enrutador para Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre los conceptos del motor de reglas del enrutador de trabajos de Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5d53f2492888323107fa539283f128ed6b6ebd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319232"
---
# <a name="job-router-rules-engine-concepts"></a>Conceptos del motor de reglas del enrutador de trabajos

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

El enrutador de trabajos de Azure Communication Services usa un motor de reglas extensible para procesar datos y tomar decisiones sobre los trabajos. En este documento se explica lo que hace el motor de reglas y por qué es posible que quiera aplicarlo en la implementación.

## <a name="rules-engine-overview"></a>Información general sobre el motor de reglas

Controlar el comportamiento de la implementación a menudo puede incluir la toma de decisiones complejas. El enrutador de trabajo proporciona una manera flexible de invocar el comportamiento mediante programación a través de varios motores de reglas. Los motores de reglas del enrutador de trabajos suelen tomar un conjunto de **etiquetas** definidas en objetos como un trabajo o una cola como entrada, aplican la regla y generan una salida.

> [!NOTE]
> Aunque el motor de reglas suele usar etiquetas como entrada, también puede establecer valores como un identificador de cola sin usar la evaluación de etiquetas.

El resultado puede variar en función de dónde aplique las reglas en el enrutador de trabajos. Por ejemplo, una directiva de clasificación puede elegir un identificador de cola en función de las etiquetas definidas en la entrada de un trabajo. En otro ejemplo, una directiva de distribución puede encontrar el mejor trabajo mediante el uso de una regla de puntuación personalizada definida por `RouterRule`.

### <a name="example-use-a-static-rule-in-a-classification-policy-to-set-the-queue-id"></a>Ejemplo: Uso de una regla estática en una directiva de clasificación para establecer el identificador de cola

En este ejemplo, se puede usar un `StaticRule`, que es un tipo de `RouterRule`, para establecer el identificador de cola de todos los trabajos, que hacen referencia al identificador de directiva de clasificación `XBOX_QUEUE_POLICY`.

```csharp
await client.SetClassificationPolicyAsync(
    id: "XBOX_QUEUE_POLICY",
    queueSelector: new QueueIdSelector(new StaticRule("XBOX"))
)
```
## <a name="routerrule-types"></a>Tipos de RouterRule

Los tipos `RouterRule` siguientes existen en el enrutador de trabajos para proporcionar flexibilidad en la manera de procesar los trabajos.

**Regla estática**: esta regla se puede usar para especificar un valor estático, como seleccionar un identificador de cola específico.

**Regla de expresión**: una regla de expresión usa el lenguaje [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) para definir la regla como una expresión insertada.

**Regla de Azure Function**: al especificar un URI y un `AzureFunctionRuleCredential`, esta regla permite que el enrutador de trabajos pase las etiquetas de entrada como una carga útil y responde, a su vez, con un valor de salida. Este tipo de regla se puede usar cuando los requisitos son complejos.

> [!NOTE]
> Si bien la **regla de mapa directo** forma parte del SDK del enrutador de trabajos, en este momento solo es compatible con `NearestQueueLabelSelector`.
