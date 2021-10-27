---
title: Clasificación de un trabajo
titleSuffix: An Azure Communication Services how-to guide
description: Uso de los SDK de Azure Communication Services para cambiar las propiedades de un trabajo
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 0648334ef4baef6c753afa222a5532356ad67590
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074958"
---
# <a name="classifying-a-job"></a>Clasificación de un trabajo

Aprenda a usar una directiva de clasificación en el enrutador de trabajos para resolver dinámicamente la cola y la prioridad, a la vez que asocia selectores de trabajo a un trabajo.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Opcional: completar el inicio rápido para [empezar a trabajar con el enrutador de trabajos](../../quickstarts/router/get-started-router.md)

## <a name="static-classification"></a>Clasificación estática

Al crear un trabajo con el SDK, especifique solo la cola, la prioridad y los selectores de trabajo; este método se conoce como **clasificación estática**. Con el ejemplo siguiente se incluiría un trabajo en `XBOX_DEFAULT_QUEUE` con una prioridad de `1` y requeriría que los procesos de trabajo tuvieran una capacidad `XBOX_Hardware` mayor o igual que `5`.

> [!NOTE]
> Un trabajo se puede [volver a clasificar después del envío](#reclassify-a-job-after-submission), incluso si se creó inicialmente sin una directiva de clasificación. En este caso, el enrutador de trabajo evaluará el comportamiento de la directiva respecto a las **etiquetas** y realizará los ajustes necesarios en la cola, la prioridad y los selectores de trabajo.

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "Location",
            @operator: LabelOperator.Equal,
            value: "Edmonton")
    });

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="dynamic-classification"></a>Clasificación dinámica

Como se ha descrito anteriormente, una manera sencilla de enviar un trabajo es especificar la prioridad, la cola y los selectores de trabajo durante el envío. Para hacerlo, el remitente debe tener conocimientos sobre estas características. Para evitar que el remitente necesite tener conocimientos explícitos sobre el funcionamiento interno del comportamiento del enrutador de trabajos, puede especificar una **directiva de clasificación** junto con una colección de **etiquetas** genéricas para invocar el comportamiento dinámico.

### <a name="create-a-classification-policy"></a>Creación de una directiva de clasificación

La siguiente directiva de clasificación usará el lenguaje con poco código [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) para seleccionar la cola y la prioridad. La expresión intentará hallar un resultado para la etiqueta del trabajo denominada `Region` igual a `NA`, lo que dará lugar a que el trabajo se incluya en `XBOX_NA_QUEUE` si se encuentra; de lo contrario, se incluirá en `XBOX_DEFAULT_QUEUE`.  Si tampoco se encuentra `XBOX_DEFAULT_QUEUE`, el trabajo se enviará automáticamente a la cola de reserva `DEFAULT_QUEUE`, tal y como se define en `fallbackQueueId`.  Además, la prioridad será `10` si se ha hallado una coincidencia para la etiqueta `Hardware_VIP`; de lo contrario, será `1`.

```csharp
var policy = await client.SetClassificationPolicyAsync(
    id: "XBOX_NA_QUEUE_Priority_1_10",
    name: "Select XBOX Queue and set priority to 1 or 10",
    queueSelector: new QueueIdSelector(
        new ExpressionRule("If(job.Region = \"NA\", \"XBOX_NA_QUEUE\", \"XBOX_DEFAULT_QUEUE\")")
    ),
    workerSelectors: new List<LabelSelectorAttachment>
    {
        new StaticLabelSelector(
            new LabelSelector(
                key: "Language",
                @operator: LabelOperator.Equal,
                value: "English")
        )
    },
    prioritizationRule: new ExpressionRule("If(job.Hardware_VIP = true, 10, 1)"),
    fallbackQueueId: "DEFAULT_QUEUE"
);
```

### <a name="submit-the-job"></a>Enviar el archivo

En el ejemplo siguiente, la directiva de clasificación evaluará las etiquetas del trabajo. El resultado colocará el trabajo en la cola llamada `XBOX_NA_QUEUE` y establecerá la prioridad en `1`.

```csharp
var dynamicJob = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "my_custom_reference_number",
    classificationPolicyId: "XBOX_NA_QUEUE_Priority_1_10",
    labels: new LabelCollection()
    {
        { "Region", "NA" },
        { "Caller_Id", "tel:7805551212" },
        { "Caller_NPA_NXX", "780555" },
        { "XBOX_Hardware", 7 }
    }
);

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="reclassify-a-job-after-submission"></a>Nueva clasificación de un trabajo después del envío

Una vez que el enrutador de trabajos ha recibido y clasificado un trabajo mediante una directiva, se puede volver a clasificar mediante el SDK. En el ejemplo siguiente se muestra una manera de aumentar la prioridad del trabajo a `10`, simplemente especificando el **identificador de trabajo**, llamando al método `ReclassifyJobAsync` e incluyendo la etiqueta `Hardware_VIP`.

```csharp
var reclassifiedJob = await client.ReclassifyJobAsync(
    jobId: "4ad7f4b9-a0ff-458d-b3ec-9f84be26012b",
    classificationPolicyId: null,
    labelsToUpdate: new LabelCollection()
    {
        { "Hardware_VIP", true }
    }
);
```
