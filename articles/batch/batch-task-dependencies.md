---
title: Creación de dependencias de tareas para ejecutar tareas
description: Cree tareas que dependan de la finalización de otras para procesar grandes cargas de trabajo de macrodatos similares y de estilo MapReduce en Azure Batch.
ms.topic: how-to
ms.date: 06/29/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 0cd5e0bc97d37e4daf76bee66c8b4de4698b7aca
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126525"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Creación de dependencias de tareas para ejecutar las tareas que dependan de otras tareas

Con dependencias de la tarea de Batch, puede crear tareas que estén programadas para su ejecución en los nodos de proceso después de la finalización de una o varias tareas principales. Por ejemplo, puede crear un trabajo que represente cada fotograma de una película 3D con una tarea independiente y paralela. La última tarea combina los fotogramas representados en la película completa solo después de que todos se hayan representado correctamente. Es decir, la tarea final depende de las tareas primarias anteriores.

A continuación se indican algunos escenarios donde las dependencias de tareas son útiles:

- Cargas de trabajo del estilo MapReduce en la nube.
- Trabajos cuyas tareas de procesamiento de datos se pueden expresar como un gráfico acíclico dirigido (DAG).
- Procesos anteriores y posteriores a la representación, donde cada tarea se debe completar para que pueda comenzar la siguiente tarea.
- Cualquier otro trabajo en el que las tareas que siguen en la cadena dependen de las tareas que preceden en la cadena.

De forma predeterminada, las tareas dependientes están programadas para ejecutarse solo después de que la tarea principal se haya completado correctamente. Opcionalmente puede especificar una [acción de dependencia](#dependency-actions) para invalidar el comportamiento predeterminado y ejecutar la tarea dependiente incluso si se produce un error en la tarea principal.

En este artículo se describe cómo configurar las dependencias de tareas mediante la biblioteca de [.NET de Batch](/dotnet/api/microsoft.azure.batch). Primero le mostraremos cómo [habilitar la dependencia de tareas](#enable-task-dependencies) en sus trabajos y, después, le demostraremos cómo [configurar una tarea con dependencias](#create-dependent-tasks). También se describe cómo especificar una acción de dependencia para ejecutar tareas dependientes, si se produce un error en la principal. Por último, se tratarán los [escenarios de dependencia](#dependency-scenarios) compatibles con Batch.

## <a name="enable-task-dependencies"></a>Habilitación de dependencias de tareas

Para utilizar la dependencia de tareas en la aplicación Batch, antes es preciso configurar el trabajo para usar dependencias de tareas. En Batch para .NET, habilítelo en [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) estableciendo su propiedad [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) en `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

En el fragmento de código anterior, "batchClient" es una instancia de la clase [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="create-dependent-tasks"></a>Creación de tareas dependientes

Para crear una tarea que dependa de la finalización de una o varias tareas, puede especificar que la tarea "dependa" de las otras tareas. En .NET para Batch, configure la propiedad [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) con una instancia de la clase [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies):

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este fragmento de código crea una tarea dependiente con el identificador de tarea "Flowers". La tarea "Flowers" depende de las tareas "Rain" y "Sun". La tarea "Flowers" se programará para que se ejecute en un nodo de proceso solo después de las tareas "Rain" y "Sun" se hayan completado correctamente.

> [!NOTE]
> De forma predeterminada, se considera que una tarea se ha completado correctamente cuando se encuentra en estado de completada y su código de salida es `0`. En .NET de Batch, esto significa que el valor de la propiedad [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state) es `Completed` y el valor de la propiedad [TaskExecutionInformation.ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) de CloudTask es `0`. Para aprender a cambiar esto, consulte la sección [Acciones de dependencia](#dependency-actions).

## <a name="dependency-scenarios"></a>escenarios de dependencia

Hay tres escenarios de dependencia de tareas básicos que puede usar en Azure Batch: uno a uno, uno a varios y la dependencia de intervalo de identificadores de tarea. Estos tres escenarios se pueden combinar para proporcionar un cuarto escenario: varios a varios.

| Escenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Ejemplo | Ilustración |
|:---:| --- | --- |
|  [Uno a uno](#one-to-one) |*taskB* depende de *taskA* <p/> Se programará que *taskB* no se ejecute hasta que *taskA* se haya completado correctamente |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Diagrama que muestra el escenario de dependencia de tareas uno a uno."::: |
|  [Uno a varios](#one-to-many) |*taskC* depende de *taskA* y *taskB*. <p/> Se programará que *taskC* no se ejecute hasta que *taskA* y *taskB* se hayan completado correctamente |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Diagrama que muestra el escenario de dependencia de tareas uno a varios."::: |
|  [Intervalo de id. de tarea](#task-id-range) |*taskD* depende de una serie de tareas <p/> Se programará que *taskD* no se ejecute hasta que las tareas con los identificadores del *1* al *10* se hayan completado correctamente. |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="Diagrama que muestra el escenario de dependencia de tareas de intervalo de id. de tarea."::: |

> [!TIP]
> Puede crear relaciones de **varios a varios**, por ejemplo, donde las tareas C, D, E y F dependan de las tareas A y B. Esto es útil, por ejemplo, en escenarios de preprocesamiento en paralelo donde las tareas que siguen en la cadena dependen de la salida de varias tareas que preceden en la cadena.
> 
> En los ejemplos de esta sección, una tarea dependiente solo se ejecuta después de que las tareas principales se completen correctamente. Este comportamiento es el comportamiento predeterminado para una tarea dependiente. Puede ejecutar una tarea dependiente después de que se produzca un error en una tarea principal si especifica una [acción de dependencia](#dependency-actions) para invalidar el comportamiento predeterminado.

### <a name="one-to-one"></a>Uno a uno

En una relación uno a uno, una tarea depende de la finalización correcta de una tarea principal. Para crear la dependencia, proporcione un identificador de tarea único al método estático [TaskDependencies.OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) cuando rellene la propiedad [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson).

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Uno a varios

En una relación uno a muchos, una tarea depende de la finalización correcta de varias tareas principales. Para crear la dependencia, proporcione una colección de identificadores de tarea concretos al método estático [TaskDependencies.OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) cuando rellene la propiedad [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson).

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

> [!IMPORTANT]
> Se producirá un error en la creación de la tarea dependiente si la longitud combinada de los id. de tarea primarios es superior a 64 000 caracteres. Para especificar un gran número de tareas primarias, considere la posibilidad de usar en su lugar un intervalo de identificadores de tarea.

### <a name="task-id-range"></a>Intervalo de id. de tarea

En un dependencia de un intervalo de tareas principales, una tarea depende de la finalización de las tareas cuyos identificadores se encuentran dentro de un intervalo especificado.

Para crear la dependencia, proporcione el primer y el último identificador de tarea del intervalo al método estático [TaskDependencies.OnIdRange](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) cuando rellene la propiedad [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson).

> [!IMPORTANT]
> Si usa intervalos de identificadores de tareas para las dependencias, el intervalo solo seleccionará aquellas tareas con identificadores que representan valores enteros. Por ejemplo, el intervalo `1..10` seleccionará las tareas `3` y `7`, pero no la tarea `5flamingoes`.
>
> Los ceros iniciales no son significativos al evaluar las dependencias de intervalo, por lo que las tareas con los identificadores de cadena `4`, `04` y `004` estarán todas *dentro* del intervalo. Dado que todas se tratarán como la tarea `4`, la primera en completarse cumplirá la dependencia.
>
> Para que se ejecute la tarea dependiente, todas las tareas del intervalo deben satisfacer la dependencia, ya sea cuando se completan correctamente como cuando lo hacen con un error que se asigna a una [acción de dependencia](#dependency-actions) establecida en **Satisfacer**.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Acciones de dependencia

De forma predeterminada, una tarea dependiente o un conjunto de tareas se ejecuta solo después de que una tarea principal se haya completado correctamente. En algunos escenarios, puede que desee ejecutar tareas dependientes incluso si se produce un error en la tarea principal. Puede invalidar el comportamiento predeterminado si especifica una *acción de dependencia* que indique si una tarea dependiente es apta para ejecutarse.

Por ejemplo, suponga que una tarea dependiente está esperando los datos de la finalización de la tarea de nivel superior. Si se produce un error en la tarea de nivel superior, la tarea dependiente aún puede ejecutarse con datos más antiguos. En este caso, una acción de dependencia puede especificar que la tarea dependiente es apta para ejecutarse a pesar del error de la tarea principal.

Una acción de dependencia se basa en una condición de salida para la tarea principal. Puede especificar una acción de dependencia para cualquiera de las siguientes condiciones de salida:

- Cuando se produce un error de procesamiento previo.
- Cuando se produce un error de carga de archivo. Si la tarea finaliza con un código de salida que se especificó mediante **exitCodes** o **exitCodeRanges** y, a continuación, encuentra un error de carga de archivo, la acción especificada por el código de salida tiene prioridad.
- Cuando la tarea finaliza con un código de salida definido por la propiedad **ExitCodes**.
- Cuando la tarea finaliza con un código de salida con error dentro de un intervalo especificado por la propiedad **ExitCodeRanges**.
- El caso predeterminado, si la tarea finaliza con un código de salida no definido en **ExitCodes** o **ExitCodeRanges**, o si la tarea finaliza con un error de procesamiento previo y no se ha establecido la propiedad **PreProcessingError**, o si se produce un error de carga de archivo en la tarea y no se ha establecido la propiedad **FileUploadError**. 

Para .NET, estas condiciones se definen como propiedades de la clase [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions).

Para especificar una acción de dependencia, establezca la propiedad [ExitOptions.DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) para la condición de salida en uno de los valores siguientes:

- **Satisfy**: indica que las tareas dependientes son aptas para ejecutarse si la tarea principal se cierra con un error especificado.
- **Bloquear**: indica que las tareas dependientes no son aptas para ejecutarse.

La configuración predeterminada para la propiedad **DependencyAction** es **Satisfacer** para el código de salida 0, y **Bloquear** para todas las demás condiciones de salida.

El fragmento de código siguiente establece la propiedad **DependencyAction** para una tarea principal. Si la tarea principal finaliza con un error de procesamiento previo o con los códigos de error especificados, la tarea dependiente se bloquea. Si la tarea principal sale con cualquier otro error distinto de cero, la tarea dependiente es apta para ejecutarse.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Código de ejemplo

En el proyecto de ejemplo [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) de GitHub se muestra lo siguiente:

- Cómo habilitar la dependencia de tareas en un trabajo.
- Cómo crear tareas que dependen de otras tareas.
- Cómo ejecutar las tareas en un grupo de nodos de proceso.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre la característica [paquetes de aplicación](batch-application-packages.md) de Batch, que proporciona una manera sencilla de implementar y de cambiar la versión de las aplicaciones que las tareas ejecutan en los nodos de proceso.
- Obtenga información sobre la [comprobación de errores de trabajos y tareas](batch-job-task-error-checking.md).
