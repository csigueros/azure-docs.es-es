---
title: Administración de una cola en el enrutador de trabajos
titleSuffix: An Azure Communication Services how-to guide
description: Uso de los SDK de Azure Communication Services para administrar el comportamiento de una cola
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f7aaf45cea9ced8721d9b13da4d8ab34b249f2
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074957"
---
# <a name="manage-a-queue"></a>Administración de una cola

En esta guía se describen los pasos para crear y administrar una cola de enrutador de trabajos.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Opcional: complete el inicio rápido para [empezar a trabajar con el enrutador de trabajos](../../quickstarts/router/get-started-router.md)

## <a name="create-a-queue"></a>Creación de una cola

Para crear una cola sencilla en el enrutador de trabajos, use el SDK para especificar el **identificador de cola**,el **nombre** y un **identificador de directiva de distribución**. La directiva de distribución debe crearse de antemano, ya que el enrutador de trabajos validará su existencia tras la creación de la cola. En el ejemplo siguiente, se crea una directiva de distribución para controlar cómo se generan las ofertas de trabajo para los roles de trabajo.

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);

var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```
## <a name="update-a-queue"></a>Actualización de una cola

El SDK del enrutador de trabajo creará una nueva cola o actualizará una cola existente cuando se llame al método `SetQueue` o `SetQueueAsync`.

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```

## <a name="delete-a-queue"></a>Eliminación de una cola

Para eliminar una cola mediante el SDK del enrutador de trabajos, llame al método `DeleteQueue` o `DeleteQueueAsync` pasando el **identificador de cola**.

```csharp
var result = await client.DeleteQueueAsync("XBOX_DEFAULT_QUEUE");
```

> [!NOTE]
> Para eliminar una cola, debe asegurarse de que no hay ningún trabajo activo asignado a esta. Además, asegúrese de que no haya referencias a la cola en ninguna regla o directivas de clasificación que usen una expresión para seleccionar la cola por identificador mediante un valor de cadena.