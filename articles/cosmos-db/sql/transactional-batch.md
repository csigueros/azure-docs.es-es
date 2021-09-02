---
title: Operaciones por lotes transaccionales en Azure Cosmos DB mediante el SDK para .NET
description: Descubra cómo usar TransactionalBatch en el SDK de Azure Cosmos DB para .NET para realizar un grupo de operaciones puntuales que finalicen bien correctamente, bien con errores.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f3bef945fe5e676b221650d9f43a9955e4483133
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114382"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Operaciones por lotes transaccionales en Azure Cosmos DB mediante el SDK para .NET
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El lote transaccional describe un grupo de operaciones puntuales que deben finalizar juntas, ya sea correctamente o con errores, con la misma clave de partición en un contenedor. En el SDK para .NET, se usa la clase `TransactionalBatch` para definir este lote de operaciones. Si todas las operaciones se realizan correctamente en el orden en que se describen en el lote transaccional, se confirmará la transacción. Sin embargo, si se produce un error en alguna de las operaciones, se revierte toda la transacción.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Concepto de transacción en Azure Cosmos DB

Una transacción en una base de datos típica se puede definir como una secuencia de operaciones realizadas como una única unidad lógica de trabajo. Cada transacción ofrece garantías de propiedad ACID (atomicidad, coherencia, aislamiento y durabilidad).

* La **atomicidad** garantiza que todas las operaciones realizadas dentro de una transacción se consideran como una única unidad y se confirman todas o ninguna.
* La **coherencia** garantiza que los datos se encuentran siempre en un estado válido entre las transacciones.
* El **aislamiento** garantiza que dos transacciones no pueden interferir entre ellas; muchos sistemas comerciales proporcionan varios niveles de aislamiento que se pueden utilizar según las necesidades de aplicación.
* La **durabilidad** garantiza que cualquier cambio que se confirme en una base de datos estará siempre presente.
Azure Cosmos DB es [totalmente compatible con transacciones ACID con aislamiento de instantáneas](database-transactions-optimistic-concurrency.md) para las operaciones que se encuentran en la misma [clave de partición lógica](../partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Operaciones por lotes transaccionales frente a procedimientos almacenados

Actualmente, Azure Cosmos DB admite procedimientos almacenados, que también proporcionan el ámbito transaccional en las operaciones. Sin embargo, las operaciones por lotes transaccionales ofrecen las siguientes ventajas:

* **Opción de lenguaje**: el lote transaccional es compatible con el SDK y el lenguaje con los que ya trabaja, mientras que los procedimientos almacenados deben escribirse en JavaScript.
* **Control de versiones de código**: controlar las versiones del código de la aplicación e incorporarlo en la canalización de CI/CD es mucho más natural que orquestar la actualización de un procedimiento almacenado y asegurar que la sustitución sucede en el momento adecuado. También se facilita la reversión de los cambios.
* **Rendimiento**: la latencia en operaciones equivalentes se reduce hasta un 30 % en comparación con la ejecución del procedimiento almacenado.
* **Serialización del contenido**: cada operación dentro de un lote transaccional puede aprovechar las opciones de serialización personalizadas para su carga.

## <a name="how-to-create-a-transactional-batch-operation"></a>Creación de una operación por lotes transaccional

Al crear una operación por lotes transaccional, debe comenzar a partir de una instancia de contenedor y llamar a `CreateTransactionalBatch`:

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

A continuación, deberá llamar a `ExecuteAsync` en el lote:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Una vez recibida la respuesta, examine si es correcta o no y extraiga los resultados:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Si se produce un error en la operación, esta tendrá un código de estado del error correspondiente. Todas las demás operaciones tendrán un código de estado 424 (dependencia con error). En el ejemplo siguiente, se produce un error en la operación porque intenta crear un elemento que ya existe (409 HttpStatusCode.Conflict). Los códigos de estado facilitan la identificación de la causa del error de la transacción.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Ejecución de las operaciones por lotes transaccionales

Cuando se llama al método `ExecuteAsync`, todas las operaciones del objeto `TransactionalBatch` se agrupan, se serializan en una sola carga y se envían como una única solicitud al servicio Azure Cosmos DB.

El servicio recibe la solicitud, ejecuta todas las operaciones de un ámbito transaccional y devuelve una respuesta con el mismo protocolo de serialización. Esta respuesta indica el fin correcto o un error, y proporciona todas las respuestas de las operaciones individuales.

El SDK le expone la respuesta para que compruebe el resultado y, opcionalmente, extraiga cada uno de los resultados de las operaciones internas.

## <a name="limitations"></a>Limitaciones

Actualmente, hay dos límites conocidos:

* El límite de tamaño de la solicitud de Azure Cosmos DB especifica que el tamaño de la carga de `TransactionalBatch` no puede superar los 2 MB y que el tiempo máximo de ejecución es de 5 segundos.
* Hay un límite actual de 100 operaciones por `TransactionalBatch`, para garantizar que el rendimiento es el esperado y se sitúa dentro de los contratos de nivel de servicio.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre [lo que puede hacer con TransactionalBatch](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch).

* Visite nuestros [ejemplos](sql-api-dotnet-v3sdk-samples.md) para conocer más formas de usar el SDK de Cosmos DB para .NET.
