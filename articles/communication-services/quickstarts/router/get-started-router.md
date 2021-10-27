---
title: 'Inicio rápido: Envío de trabajos para su puesta en cola y enrutamiento'
titleSuffix: An Azure Communication Services quickstart
description: En este inicio rápido, aprenderá a crear un cliente del enrutador de trabajos, una directiva de distribución, una cola y un trabajo en el recurso Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/18/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ffad9a33c932300dbf52989536663215f8abb8f9
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164764"
---
# <a name="quickstart-submit-a-job-for-queuing-and-routing"></a>Inicio rápido: Envío de trabajos para su puesta en cola y enrutamiento

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Para empezar a trabajar con el enrutador de trabajos de Azure Communication Services, configure el cliente. Luego, puede configurar funcionalidades básicas, como colas, directivas, roles de trabajo y trabajos. Para más información sobre los conceptos del enrutador de trabajos, consulte la [documentación conceptual del enrutador de trabajos](../../concepts/router/concepts.md).

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un recurso activo de Communication Services y una cadena de conexión. [Cree un recurso de Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Instalación

### <a name="create-a-new-c-application"></a>Creación de una aplicación de C#

En una ventana de consola (por ejemplo, cmd, PowerShell o Bash), use el comando `dotnet new` para crear una nueva aplicación de consola con el nombre `JobRouterQuickstart`. Este comando crea un sencillo proyecto "Hola mundo" de C# con un solo archivo de origen: **Program.cs**.

```console
dotnet new console -o JobRouterQuickstart
```

Cambie el directorio a la carpeta de la aplicación recién creada y use el comando `dotnet build` para compilar la aplicación.

```console
cd JobRouterQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalar el paquete

Durante la versión preliminar privada, descargue el SDK de [GitHub\](https://github.com/Azure/communication-preview/releases/tag/communication-job-router-net-v1.0.0-alpha.20211012.1).

> [!NOTE]
> Debe ser miembro del grupo de versión preliminar privado para descargar los SDK.

Agregue las directivas `using` siguientes al principio de **Program.cs** para incluir los espacios de nombres JobRouter.

```csharp
using Azure.Communication.JobRouter;
```

Actualice la signatura de función `Main` para que sea `async` y devuelva un objeto `Task`.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-job-router-client"></a>Autenticación del cliente del enrutador de trabajos

Los clientes del enrutador de trabajos se pueden autenticar mediante la cadena de conexión adquirida de un recurso Azure Communication Services en Azure Portal.

```csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "your_connection_string";
var client = new RouterClient(connectionString);
```

## <a name="create-a-distribution-policy"></a>Creación de una directiva de distribución

El enrutador de trabajos usa una directiva de distribución para decidir cómo se notificará a los roles de trabajo los trabajos disponibles y el período de vida de las notificaciones, lo que se conoce como **ofertas**. Para crear la directiva, especifique el **identificador**, un **nombre**, un valor de **offerTTL** y un **modo** de distribución.

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);
```

## <a name="create-a-queue"></a>Creación de una cola

Los trabajos se organizan en una cola lógica. Para crear la cola, especifique un **identificador** y un **nombre**, y proporcione el identificador de objeto de la **directiva de distribución** que creó anteriormente.

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: distributionPolicy.Value.Id
);
```

## <a name="submit-a-job"></a>Enviar un trabajo
La manera más rápida de empezar es especificar el identificador de la cola, la prioridad y los requisitos del rol de trabajo al enviar un trabajo. En el ejemplo siguiente, el trabajo se envía directamente a la **cola de XBOX**, donde los roles de trabajo de esa cola requieren una etiqueta `Location` que coincida con el nombre `Edmonton`.

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsChatChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelector: new List<LabelSelector>
    {
        new (
            key: "Location", 
            @operator: LabelOperator.Equal, 
            value: "Edmonton")
    });
```

## <a name="register-a-worker"></a>Registro de un rol de trabajo
Para registrar un rol de trabajo, haga referencia al identificador de cola creado anteriormente junto con un valor de **capacidad**, las **etiquetas** y la **configuración del canal** para asegurarse de que `EdmontonWorker` está asignado a "XBOX_Queue".

```csharp
var edmontonWorker = await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new []{ queue.Value.Id },
    totalCapacity: 100,
    labels: new LabelCollection()
    {
        {"Location", "Edmonton"}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100)
    }
);
```

## <a name="query-the-worker-to-observe-the-job-offer"></a>Consulta del rol de trabajo para observar la oferta de trabajo
Use la conexión de cliente del enrutador de trabajos para consultar el rol de trabajo y comparar el identificador del trabajo con el identificador. 

```csharp
    // wait 500ms for the Job Router to offer the Job to the Worker
    Task.Delay(500).Wait();

    var result = await client.GetWorkerAsync(edmontonWorker.Value.Id);

    Console.WriteLine(
        $"Job ID: {job.Value.Id} offered to {edmontonWorker.Value.Id} " +
        $"should match Job ID attached to worker: {result.}");
```

Ejecute la aplicación con `dotnet run` y observe los resultados.

```console
dotnet run

Job 6b83c5ad-5a92-4aa8-b986-3989c791be91 offered to EdmontonWorker should match Job ID from offer attached to worker: 6b83c5ad-5a92-4aa8-b986-3989c791be91
```

> [!NOTE]
> La ejecución de la aplicación más de una vez hará que se coloque un nuevo trabajo en la cola cada vez. Como consecuencia, al rol de trabajo se le puede ofrecer un trabajo distinto al que se creó al ejecutar el código anterior. Dado que esto puede sesgar la solicitud, considere la posibilidad de quitar trabajos de la cola cada vez. Consulte la documentación del SDK para administrar colas o trabajos.