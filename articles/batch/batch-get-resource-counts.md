---
title: Estados de recuento de tareas y nodos
description: Realice un recuento del estado de los nodos de ejecución y las tareas de Azure Batch para ayudar a administrar y supervisar las soluciones de Batch.
ms.date: 09/10/2021
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 4f17bb97aefef5db2cbfe9d4e344d26cff3f0296
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806673"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Supervisión de las soluciones de Batch realizando un recuento de las tareas y los nodos por estado

Para supervisar y administrar soluciones de Azure Batch a gran escala, es posible que deba determinar los recuentos de los recursos de varios estados. Azure Batch proporciona operaciones eficaces para obtener estos recuentos de las tareas y los nodos de ejecución de Batch. Puede usar estas operaciones en lugar de consultas de lista que es posible que tarden mucho para devolver información detallada sobre grandes colecciones de tareas o nodos.

- [Get Task Counts](/rest/api/batchservice/job/gettaskcounts) obtiene un recuento agregado de tareas activas, en ejecución y completadas, así como de tareas que se realizaron correctamente o no. Si cuenta las tareas en cada estado, podrá mostrar más fácilmente el progreso del trabajo a un usuario o detectar retrasos o errores inesperados que pueden afectar a la tarea.

- [List Pool Node Counts](/rest/api/batchservice/account/listpoolnodecounts) obtiene el número de nodos de ejecución dedicados y de prioridad baja en cada grupo que se encuentran en varios estados: creating, idle, offline, preempted, rebooting, reimaging, starting, etc. Al contar los nodos de cada estado, podrá determinar cuándo tiene los recursos de proceso adecuados para ejecutar sus tareas e identificar posibles problemas con los grupos.

Tenga en cuenta que, en ocasiones, es posible que los números devueltos por estas operaciones no estén actualizados. Si necesita asegurarse de que un recuento es preciso, use una consulta de lista para contar estos recursos. Las consultas de lista también permiten obtener información sobre otros recursos de Batch, tales como aplicaciones. Para más información sobre cómo aplicar filtros a las consultas de lista, consulte [Creación de consultas para enumerar los recursos de Batch con eficacia](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Recuentos de estados de tareas

La operación Get Task Counts hace un recuento de las tareas por los estados siguientes:

- **Active:** tarea que está en cola y se puede ejecutar, pero no está asignada actualmente a ningún nodo de ejecución. Una tarea también es `active` si [depende de una tarea primaria](batch-task-dependencies.md) que no se ha completado.
- **Running:** tarea que se ha asignado a un nodo de ejecución, pero no se ha completado. Una tarea se cuenta como `running` cuando su estado es `preparing` o `running`, tal como indica la operación [Obtener información sobre una tarea](/rest/api/batchservice/task/get).
- **Completed:** : tarea que ya no cumple los requisitos para ejecutarse, ya sea porque se ha completado correctamente o porque no lo ha hecho y además ha agotado el límite de reintentos.
- **Succeeded:** tarea cuya ejecución da el resultado `success`. Batch determina si una tarea se ha realizado correctamente o no comprobando la propiedad `TaskExecutionResult` de la propiedad [executionInfo](/rest/api/batchservice/task/get).
- **Failed:** tarea cuya ejecución da el resultado `failure`.

En el siguiente ejemplo de código de .NET se muestra cómo recuperar recuentos de tareas por estado:

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Puede usar un patrón similar para REST y otros lenguajes admitidos para obtener los recuentos de tareas de un trabajo.

## <a name="node-state-counts"></a>Recuentos de estados de nodos

La operación List Pool Node Counts hace un recuento de los nodos de ejecución por los estados siguientes en cada grupo. Se proporcionan recuentos agregados independientes para los nodos dedicados y de prioridad baja en cada grupo.

- **Creating:** : máquina virtual asignada por Azure que aún no ha empezado a unirse a ningún grupo.
- **Idle:** nodo de ejecución disponible que actualmente no ejecuta ninguna tarea.
- **LeavingPool:** nodo que va a dejar el grupo, ya sea porque el usuario lo ha eliminado expresamente, o bien porque el grupo va a cambiar de tamaño o a reducirse verticalmente de manera automática.
- **Offline:** : nodo que Batch no puede usar para programar nuevas tareas.
- **Preempted:** nodo de prioridad baja que se ha eliminado del grupo porque Azure ha reclamado la máquina virtual. Un nodo `preempted` se puede reinicializar cuando la capacidad de la máquina virtual de prioridad baja de reemplazo está disponible.
- **Rebooting:** nodo que va a reiniciarse.
- **Reimaging:** nodo en el que va a instalarse de nuevo el sistema operativo.
- **Running:** nodo que ejecuta una o varias tareas (distintas de la de inicio).
- **Starting:** nodo en el que va a iniciarse el servicio de Batch.
- **StartTaskFailed:** nodo en el que la [tarea de inicio](/rest/api/batchservice/pool/add#starttask) no se ha realizado correctamente y ha agotado todos los reintentos, y en el que `waitForSuccess` se ha establecido en la tarea de inicio. El nodo no se puede usar para las tareas en ejecución.
- **Unknown:** nodo que ha perdido el contacto con el servicio de Batch y cuyo estado no se conoce.
- **Unusable:** nodo que no se puede usar para la ejecución de tareas debido a errores.
- **WaitingForStartTask:** nodo en el que la tarea de inicio ha empezado a ejecutarse, pero en el que se ha establecido `waitForSuccess` y dicha tarea no se ha completado.

El siguiente fragmento de código de C# muestra cómo enumerar los recuentos de nodos de todos los grupos en la cuenta actual:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

El siguiente fragmento de código de C# muestra cómo enumerar los recuentos de nodos de un grupo determinado en la cuenta actual.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

Puede usar un patrón similar para REST y otros lenguajes admitidos para obtener los recuentos de nodos de los grupos.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [flujo de trabajo y los recursos principales del servicio Batch](batch-service-workflow-features.md), como grupos, nodos, trabajos y tareas.
- Para obtener información sobre cómo aplicar filtros a las consultas que enumeran los recursos de Batch, consulte [Creación de consultas para enumerar los recursos de Batch con eficacia](batch-efficient-list-queries.md).
