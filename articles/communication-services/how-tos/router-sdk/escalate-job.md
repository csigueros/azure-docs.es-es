---
title: Escalamiento de un trabajo en el enrutador de trabajo
titleSuffix: An Azure Communication Services how-to guide
description: Uso de SDK de Azure Communication Services para escalar un trabajo
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 3ed75c4e418e5e7494502e4f70d3c5419a5b162c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176042"
---
# <a name="escalate-a-job"></a>Escalamiento de un trabajo

En esta guía se muestra cómo escalar un trabajo en una cola mediante una directiva de excepciones.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Opcional: complete el inicio rápido para [empezar a trabajar con el enrutador de trabajos.](../../quickstarts/router/get-started-router.md)
- Opcional: revise la [guía paso a paso clasificación de trabajos](job-classification.md)

## <a name="escalation-overview"></a>Información general sobre la escalación

La escalación puede tomar la forma de varios comportamientos diferentes, como mover un trabajo a una cola distinta o especificar una prioridad más alta. Los trabajos con mayor prioridad se distribuirán a trabajadores antes que a trabajos con una prioridad más baja. Para esta guía paso a paso, usaremos una directiva de escalación y una directiva de clasificación para lograr este objetivo.

## <a name="exception-policy-configuration"></a>Configuración de directivas de excepción

Cree una directiva de excepción, que se asociará a la cola normal, que se desencadena por el tiempo y realiza la acción del trabajo que se está reclasificando.

```csharp
// create the exception policy
await client.SetExceptionPolicyAsync(
    id: "Escalate_XBOX_Policy",
    name: "Add escalated label and reclassify XBOX Job requests after 5 minutes",
    rules: new List<ExceptionRule>()
    {
        new (
            id: "Escalated_Rule",
            trigger: new WaitTimeExceptionTrigger(TimeSpan.FromMinutes(5)),
            actions: new List<ExceptionAction>
            {
                new ReclassifyExceptionAction("EscalateReclassifyExceptionAction")
                {
                    LabelsToUpsert = new LabelCollection(
                        new Dictionary<string, object>
                    {
                        ["Escalated"] = true,
                    })
                }
            }
        )
    });
```

## <a name="classification-policy-configuration"></a>Configuración de directivas de clasificación

Cree una directiva de clasificación para controlar la nueva etiqueta agregada al trabajo. Esta directiva evaluará la etiqueta `Escalated` y asignará el trabajo a cualquier cola. La directiva también usará [RulesEngine](../../concepts/router/router-rule-concepts.md) para aumentar la prioridad del trabajo de `1` a `10`.

```csharp
await client.SetClassificationPolicyAsync(
    id: "Classify_XBOX_Voice_Jobs",
    name: "Classify XBOX Voice Jobs",
    queueSelector: new QueueIdSelector(
        new ExpressionRule(
            "If(job.Escalated = true, \"XBOX_Queue\", \"XBOX_Escalation_Queue\")")),
    workerSelectors: null,
    prioritizationRule: new ExpressionRule("If(job.Escalated = true, 10, 1)"),
    fallbackQueueId: "Default");
```

## <a name="queue-configuration"></a>Configuración de la cola

Cree las colas necesarias para los trabajos normales y escalados y asigne la directiva de excepción a la cola normal.

> [!NOTE]
> En este paso se da por supuesto que ya ha creado una directiva de distribución con el nombre `Round_Robin_Policy`.

```csharp
// create a queue for regular Jobs and attach the exception policy
await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: "Round_Robin_Policy",
    exceptionPolicyId: "XBOX_Escalate_Policy"
);

// create a queue for escalated Jobs
await client.SetQueueAsync(
    id: "XBOX_Escalation_Queue",
    name: "XBOX Escalation Queue",
    distributionPolicyId: "Round_Robin_Policy"
);
```

## <a name="job-lifecycle"></a>Ciclo de vida de trabajo

Al enviar el trabajo, especifique el identificador de directiva de clasificación como se muestra a continuación. En este ejemplo concreto, el requisito sería buscar un trabajo con una etiqueta denominada `XBOX_Hardware`, que tiene un valor mayor o igual que el número `7`.

```csharp
await client.CreateJobWithClassificationPolicyAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    classificationPolicyId: "Classify_XBOX_Voice_Jobs",
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "XBOX_Hardware",
            @operator: LabelOperator.GreaterThanEqual,
            value: 7)
    }
);
```

Los siguientes pasos del ciclo de vida se realizarán una vez completada la configuración y el trabajo esté listo para enviarse:

1. El trabajo se envía al enrutador de trabajos y, puesto que se asocia una directiva de clasificación, evaluará y producirá tanto `RouterJobReceived` como `RouterJobClassified`.
2. A continuación, se inicia el temporizador de 5 minutos y, finalmente, se desencadenará si no se puede asignar ningún trabajo. Se supone que no hay ningún trabajo registrado, lo que da lugar a `RouterJobExceptionTriggered` y a otro `RouterJobClassified`.
3. En este momento, el trabajo estará en `XBOX_Escalation_Queue` y la prioridad se establecerá en `10`.