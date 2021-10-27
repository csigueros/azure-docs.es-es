---
title: Conceptos de distribución de trabajos para Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre los conceptos de distribución del enrutador de trabajos de Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 97f954f3906b95fb530fba06ea09b5b0d171c9ed
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074975"
---
# <a name="job-distribution-concepts"></a>Conceptos de distribución de trabajos

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

El enrutador de trabajos de Azure Communication Services usa un proceso de distribución flexible, que implica el uso de una directiva y un ciclo de vida de la oferta de trabajo para asignar roles de trabajo. En este artículo, se describen las distintas formas en que se puede distribuir un trabajo, cuál es el ciclo de vida de la oferta de trabajo y el efecto que este proceso tiene en los roles de trabajo.

## <a name="job-distribution-overview"></a>Introducción a la distribución de trabajos

Decidir cómo distribuir trabajos a los roles de trabajo es una característica clave del enrutador de trabajos y el SDK ofrece un modelo flexible y extensible similar para personalizar el entorno. Como se describe en la guía de [conceptos de clasificación](classification-concepts.md), una vez clasificado un trabajo, el enrutador de trabajos buscará un rol de trabajo adecuado en función de las características del trabajo y la directiva de distribución. Como alternativa, si los roles de trabajo están ocupados, el enrutador de trabajos buscará un trabajo adecuado cuando haya un rol de trabajo disponible. La idoneidad del rol de trabajo se decide con tres características: [un canal disponible](#channel-configurations), sus [capacidades](#worker-abilities) y el [estado](#worker-status). Una vez que se ha encontrado un rol de trabajo adecuado, se realiza una comprobación para asegurarse de que tenga un canal abierto al que se pueda asignar el trabajo.

Estos dos enfoques son los conceptos clave de cómo el enrutador de trabajos inicia la detección de trabajos o roles de trabajo.

### <a name="finding-workers-for-a-job"></a>Búsqueda de roles de trabajo para un trabajo

Una vez que un trabajo ha completado el [proceso de clasificación](classification-concepts.md), el enrutador de trabajos aplicará la directiva de distribución configurada en la cola para seleccionar uno o varios roles de trabajo que cumplan los selectores de rol de trabajo del trabajo y generar ofertas para que esos roles de trabajo tomen el trabajo. 

### <a name="finding-a-job-for-a-worker"></a>Búsqueda de un trabajo para un rol de trabajo

Hay varios escenarios que desencadenarán que el enrutador de trabajos busque un trabajo para un rol de trabajo:

- Cuando un rol de trabajo se registra en el enrutador de trabajos
- Cuando se cierra un trabajo y se libera el canal
- Cuando se rechaza o revoca una oferta de trabajo

El proceso de distribución es el mismo que para buscar roles de trabajo para un trabajo. Cuando se encuentra un rol de trabajo, se genera una [oferta](#job-offer-overview).

## <a name="worker-overview"></a>Información general de los roles de trabajo

Los roles de trabajo se **registran** con el enrutador de trabajos mediante el SDK y suministran la siguiente información básica:

- Un identificador y un nombre de rol de trabajo
- Identificadores de cola
- Capacidad total (número)
- Una lista de **configuraciones de canales**
- Un conjunto de etiquetas 

El enrutador de trabajos siempre mantiene una referencia a cualquier rol de trabajo registrado, incluso si se **anula el registro** de forma manual o automática.

### <a name="channel-configurations"></a>Configuraciones de canales

Cada trabajo requiere una propiedad de identificador de canal que represente un canal del enrutador de trabajos preconfigurado o un canal personalizado. Una configuración de canal consta de una cadena `channelId` y un número `capacityCostPerJob`. Juntos representan un modo abstracto de comunicación y el costo de ese modo. Por ejemplo, la mayoría de las personas solo pueden estar en una llamada de teléfono a la vez, por lo que un canal de tipo `Voice` puede tener un costo elevado de `100`. Como alternativa, ciertas cargas de trabajo, como el chat, pueden tener una mayor simultaneidad, lo que significa que tienen un costo menor. Puede pensar en las configuraciones de canales como ranuras abiertas en las que se puede asignar o adjuntar un trabajo. Esto se ilustra en el ejemplo siguiente:

```csharp
await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new[] { "XBOX_Queue", "XBOX_Escalation_Queue" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "Edmonton" },
        { "XBOX_Hardware", 7 },
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100
        ),
        new (
            channelId: ManagedChannels.AcsChatChannel,
            capacityCostPerJob: 33
        )
    }
);
```

El rol trabajo anterior se ha registrado con dos configuraciones de canal, cada una con costos únicos por canal. El resultado efectivo es que `EdmontonWorker` puede controlar tres trabajos `ManagedChannels.AcsChatChannel` simultáneos o un trabajo `ManagedChannels.AcsVoiceChannel`.

El enrutador de trabajos incluye los siguientes identificadores de canales preconfigurados que puede usar:

- ManagedChannels.AcsChatChannel
- ManagedChannels.AcsVoiceChannel
- ManagedChannels.AcsSMSChannel

Se pueden crear nuevos canales abstractos mediante el SDK del enrutador de trabajos, como se muestra a continuación:

```csharp
await client.SetChannelAsync(
    id: "MakePizza",
    name: "Make a pizza"
);

await client.SetChannelAsync(
    id: "MakeDonairs",
    name: "Make a donair"
);

await client.SetChannelAsync(
    id: "MakeBurgers",
    name: "Make a burger"
);
```

A continuación, puede usar el canal al registrar el rol de trabajo para representar su capacidad de asumir un trabajo que coincida con ese identificador de canal, como se muestra a continuación:

```csharp
await client.RegisterWorkerAsync(
    id: "PizzaCook",
    queueIds: new[] { "PizzaOrders", "DonairOrders", "BurgerOrders" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: MakePizza,
            capacityCostPerJob: 50
        ),
        new (
            channelId: MakeDonair,
            capacityCostPerJob: 33
        ),
        new (
            channelId: MakeBurger,
            capacityCostPerJob: 25        
        )
    }
);
```

En el ejemplo anterior, se muestran tres canales abstractos, cada uno con su propio costo por trabajo. Por lo tanto, son posibles los siguientes ejemplos de simultaneidad de trabajos para el rol de trabajo `PizzaCook`:

| MakePizza | MakeDonair | MakeBurger | Puntuación |
|--|--|--|--|
| 2         |            |            | 100   |
|           | 3          |            | 99    |
| 1         | 1          |            | 83    |
|           | 2          | 1          | 91    |
|           |            | 4          | 100   |
|           | 1          | 2          | 83    |

### <a name="worker-abilities"></a>Capacidades del rol de trabajo

Aparte de los canales disponibles que pueda tener un rol de trabajo, el proceso de distribución usa la colección de etiquetas del rol de trabajo registrado para determinar su idoneidad para un trabajo. En el ejemplo anterior de cocinado de pizzas, el rol de trabajo tiene una colección de etiquetas que consta de:

```csharp
new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    }
```

Cuando se envía un trabajo, se utilizan los **selectores de rol de trabajo** para definir los requisitos de esa unidad de trabajo específica. Si un trabajo requiere una persona de habla inglesa que sea buena para realizar pizzas donair, la llamada al SDK sería la siguiente:

```csharp
await client.CreateJobAsync(
    channelId: "MakeDonair",
    channelReference: "ReceiptNumber_555123",
    queueId: "DonairOrders",
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "DonairMaker",
            @operator: LabelOperator.GreaterThanEqual,
            value: 8),
        new (
            key: "English",
            @operator: LabelOperator.GreaterThan,
            value: 5)
    });
```

### <a name="worker-status"></a>Estado del rol de trabajo

Puesto que el enrutador de trabajos puede controlar trabajos simultáneos para un rol de trabajo en función de sus configuraciones de canales, el concepto de disponibilidad se representa mediante tres estados:

**Activo:** el rol de trabajo está registrado en el enrutador de trabajos y está dispuesto a aceptar un trabajo.

**En purga:** el rol de trabajo ha anulado el registro en el enrutador de trabajos, pero actualmente tiene asignados uno o varios trabajos activos.

**Inactivo:** el rol de trabajo ha anulado el registro en el enrutador de trabajos y no tiene trabajos activos.

## <a name="job-offer-overview"></a>Información general de la oferta de trabajo

Cuando el proceso de distribución localiza un rol de trabajo adecuado que tiene un canal abierto y tiene el estado correcto, se genera una oferta de trabajo y se envía un evento. La directiva de distribución contiene las siguientes propiedades configurables para la oferta:

**OfferTTL:** período de vida de cada oferta generada

**Mode:** **modos de distribución**, que contiene las propiedades `minConcurrentOffers` y `maxConcurrentOffers`.

> [!Important]
> Cuando se genera una oferta de trabajo para un rol de trabajo, este consume una de las configuraciones de canales que coinciden con el identificador de canal del trabajo. El consumo de este canal significa que el rol de trabajo no recibirá otra oferta a menos que haya capacidad adicional para ese canal disponible en el rol de trabajo. Si el rol de trabajo rechaza la oferta o la oferta expira, se libera el canal.

### <a name="job-offer-lifecycle"></a>Ciclo de vida de la oferta de trabajo

Los siguientes eventos del ciclo de vida de la oferta de trabajo se pueden observar mediante la suscripción de Event Grid:

- RouterWorkerOfferIssued
- RouterWorkerOfferAccepted
- RouterWorkerOfferDeclined
- RouterWorkerOfferExpired
- RouterWorkerOfferRevoked

> [!NOTE]
> Un rol de trabajo puede aceptar o rechazar una oferta mediante el SDK, mientras todos los demás eventos se generan internamente.

## <a name="distribution-modes"></a>Modos de distribución

El enrutador de trabajos incluye los siguientes modos de distribución:

**LongestIdleMode:** genera una oferta para el rol de trabajo que lleva más tiempo inactivo en una cola.

**RoundRobinMode:** dada una colección de roles de trabajo, elige el siguiente rol de trabajo después del último que se ha seleccionado ordenado por identificador.

**BestWorkerMode:** utiliza el [motor de reglas](router-rule-concepts.md) del enrutador de trabajos para elegir un rol de trabajo en función de sus etiquetas.

## <a name="distribution-summary"></a>Resumen de la distribución

En función de varios factores, como el estado de un rol de trabajo, la configuración y la capacidad de los canales, el modo de la directiva de distribución y la simultaneidad de ofertas pueden influir en la forma en la que se generan las ofertas de trabajo. Se recomienda comenzar con una implementación sencilla y agregar complejidad según lo dicten los requisitos.