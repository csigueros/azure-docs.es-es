---
title: Modelo de extracción de fuente de cambios
description: Obtenga información sobre cómo usar el modelo de extracción de la fuente de cambios de Azure Cosmos DB para leer la fuente de cambios. Además, conozca las diferencias entre el modelo de extracción y el procesador de fuente de cambios
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/02/2021
ms.reviewer: sngun
ms.openlocfilehash: 06345674b17b790cadf69a2b10383015fdaaa134
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113681"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Modelo de extracción de la fuente de cambios de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Con el modelo de extracción de fuente de cambios, puede consumir la fuente de cambios de Azure Cosmos DB a su propio ritmo. Al igual que con el [procesador de fuente de cambios](change-feed-processor.md), puede usar el modelo de extracción de la fuente de cambios para ejecutar en paralelo el procesamiento de los cambios en varios consumidores de fuentes de cambios.

## <a name="comparing-with-change-feed-processor"></a>Comparación con el procesador de fuente de cambios

Muchos escenarios pueden procesar la fuente de cambios con el [procesador de fuente de cambios](change-feed-processor.md) o el modelo de extracción. Tanto los tokens de continuación del modelo de extracción como los contenedores de concesión del procesador de la fuente de cambios son "marcadores" del último elemento (o lote de elementos) que se procesó en la fuente de cambios.

Sin embargo, no puede convertir los tokens de continuación en un contenedor de concesión (o viceversa).

> [!NOTE]
> En la mayoría de los casos, cuando necesite leer de la fuente de cambios, la opción más sencilla será usar el [procesador de la fuente de cambios](change-feed-processor.md).

Plantéese el uso del modelo de extracción en los siguientes escenarios:

- Leer cambios de una clave de partición determinada.
- Controlar el ritmo con el que el cliente recibe los cambios para procesarlos.
- Realizar una lectura única de los datos existentes en la fuente de cambios (por ejemplo, para realizar una migración de datos).

Estas son algunas diferencias clave entre el procesador de fuente de cambios y el modelo de extracción:

|Característica  | Procesador de fuente de cambios| Modelo de extracción |
| --- | --- | --- |
| Realizar un seguimiento del punto actual en el procesamiento de la fuente de cambios. | Concesión (almacenada en un contenedor de Azure Cosmos DB). | Token de continuación (almacenado en la memoria o guardado manualmente). |
| Funcionalidad para reproducir los cambios anteriores. | Sí, con el modelo de inserción. | Sí, con el modelo de extracción.|
| Sondeo para cambios futuros. | Comprueba automáticamente si hay cambios según el `WithPollInterval` especificado por el usuario. | Manual |
| Comportamiento en el que no hay cambios nuevos | Esperar automáticamente `WithPollInterval` y volver a comprobar | Debe comprobar el estado y volver a comprobar manualmente |
| Procesar cambios de todo un contenedor. | Sí, y se ejecuta en paralelo automáticamente en varios subprocesos o máquinas que consumen el mismo contenedor.| Sí, y se ejecuta en paralelo de forma manual mediante FeedRange. |
| Procesar los cambios de una sola clave de partición. | No compatible | Sí|

> [!NOTE]
> A diferencia de cuando se lee mediante el procesador de fuente de cambios, debe controlar explícitamente los casos en los que no haya cambios nuevos. 

## <a name="consuming-an-entire-containers-changes"></a>Consumo de los cambios de todo un contenedor

Puede crear un `FeedIterator` para procesar la fuente de cambios mediante el modelo de extracción. Al crear por primera vez un `FeedIterator`, debe especificar un valor `ChangeFeedStartFrom` obligatorio que consta de la posición de inicio para leer los cambios y del `FeedRange` deseado. `FeedRange` es un intervalo de valores de claves de partición y especifica los elementos que se leerán de la fuente de cambios con ese `FeedIterator` específico.

También puede especificar `ChangeFeedRequestOptions` para establecer un objeto `PageSizeHint`. Al establecerse, esta propiedad establece la cantidad máxima de elementos recibidos por página. Si las operaciones de la colección supervisada se realizan a través de procedimientos almacenados, el ámbito de transacción se conserva al leer los elementos de la fuente de cambios. Como resultado, es posible que el número de elementos recibidos sea mayor que el valor especificado, de modo que los elementos que cambian en la misma transacción se devuelvan como parte de un lote atómico.

`FeedIterator` se ofrece en dos variedades. Además de los ejemplos siguientes que devuelven objetos entidad, también puede obtener la respuesta mediante la compatibilidad con `Stream`. Las secuencias le permiten leer datos sin tener que deserializarlos primero, de modo que se ahorran recursos del cliente.

Este es un ejemplo para obtener un `FeedIterator` que devuelve objetos entidad; en este caso, un objeto `User`:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Este es un ejemplo para obtener un `FeedIterator` que devuelve una `Stream`:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Si no suministra un `FeedRange` a un objeto `FeedIterator`, puede procesar la fuente de cambios de un contenedor completo a su propio ritmo. A continuación se muestra un ejemplo que empieza a leer todos los cambios a partir de la hora actual:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now(), ChangeFeedMode.Incremental);

while (iteratorForTheEntireContainer.HasMoreResults)
{
    FeedResponse<User> response = await iteratorForTheEntireContainer.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else 
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

Dado que la fuente de cambios es realmente una lista infinita de elementos que abarcan todas las escrituras y actualizaciones futuras, el valor de `HasMoreResults` siempre es true. Al intentar leer la fuente de cambios y no haber nuevos cambios disponibles, recibirá una respuesta con estado `NotModified`. En el ejemplo anterior, se controla al esperar 5 segundos antes de volver a comprobar los cambios.

## <a name="consuming-a-partition-keys-changes"></a>Consumo de los cambios de una clave de partición

En algunos casos, es posible que solo quiera procesar los cambios de una clave de partición específica. Puede obtener un `FeedIterator` para una clave de partición específica y procesar los cambios de la misma manera en que lo haría para todo un contenedor.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue")), ChangeFeedMode.Incremental));

while (iteratorForThePartitionKey.HasMoreResults)
{
    FeedResponse<User> response = await iteratorForThePartitionKey.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Uso de FeedRange para la paralelización

En el [procesador de fuente de cambios](change-feed-processor.md), el trabajo se distribuye de manera automática entre varios consumidores. En el modelo de extracción de la fuente de cambios, puede usar `FeedRange` para ejecutar en paralelo el procesamiento de la fuente de cambios. `FeedRange` representa un intervalo de valores de clave de partición.

Este es un ejemplo que muestra cómo obtener una lista de intervalos para su contenedor:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Al obtener la lista de FeedRanges del contenedor, obtendrá un `FeedRange` por cada [partición física](../partitioning-overview.md#physical-partitions).

Con un `FeedRange`, puede crear un `FeedIterator` para ejecutar en paralelo el procesamiento de la fuente de cambios en varios equipos o subprocesos. A diferencia del ejemplo anterior, en el que se mostró cómo obtener un `FeedIterator` para todo el contenedor o una sola clave de partición, puede usar FeedRanges para obtener varios FeedIterators que puedan procesar la fuente de cambios en paralelo.

En caso de que quiera usar FeedRanges, debe tener un proceso de orquestador que obtenga FeedRanges y los distribuya en esas máquinas. Esta distribución puede ser de las siguientes formas:

* Use `FeedRange.ToJsonString` y distribuya este valor de cadena. Los consumidores pueden usar este valor con `FeedRange.FromJsonString`
* Si la distribución está en proceso, pase la referencia de objeto `FeedRange`.

Este es un ejemplo que muestra cómo leer la fuente de cambios de un contenedor desde el principio mediante dos máquinas independientes hipotéticas que realizan la lectura en paralelo:

Máquina 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]), ChangeFeedMode.Incremental);
while (iteratorA.HasMoreResults)
{
    FeedResponse<User> response = await iteratorA.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

Máquina 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]), ChangeFeedMode.Incremental);
while (iteratorB.HasMoreResults)
{
    FeedResponse<User> response = await iteratorA.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

## <a name="saving-continuation-tokens"></a>Guardado de tokens de continuación

Puede obtener el token de continuación para guardar la posición de `FeedIterator`. Un token de continuación es un valor de cadena que realiza un seguimiento de los últimos cambios que procesó FeedIterator y permite que `FeedIterator` se reanude más tarde desde este punto. El código siguiente leerá la fuente de cambios a partir de la creación del contenedor. Cuando ya no haya más cambios disponibles, se conservará un token de continuación para que el consumo de la fuente de cambios se pueda reanudar más tarde.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);

string continuation = null;

while (iterator.HasMoreResults)
{
    FeedResponse<User> response = await iterator.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        continuation = response.ContinuationToken;
        // Stop the consumption since there are no new changes
        break;
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}

// Some time later when I want to check changes again
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation), ChangeFeedMode.Incremental);
```

En tanto exista el contenedor de Cosmos, no expirará el token de continuación de FeedIterator.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la fuente de cambios](../change-feed.md)
* [Uso del procesador de fuente de cambios](change-feed-processor.md)
* [Desencadenar Azure Functions](change-feed-functions.md)
