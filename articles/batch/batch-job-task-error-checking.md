---
title: Comprobación de errores de trabajos y tareas
description: Obtenga información sobre los errores para buscarlo y cómo solucionar los problemas de trabajos y tareas.
author: mscurrell
ms.topic: how-to
ms.date: 09/08/2021
ms.author: markscu
ms.openlocfilehash: 31ca874ebb4e3d11d46ff47e775605ffdd015f63
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815372"
---
# <a name="job-and-task-error-checking"></a>Comprobación de errores de trabajos y tareas

Se pueden producir varios errores al agregar trabajos y tareas. La detección de errores para estas operaciones es directa porque la API, la CLI y la interfaz de usuario devuelven los errores inmediatamente. Sin embargo, también se pueden producir errores más adelante cuando se programan y se ejecutan los trabajos y las tareas.

En este artículo se tratan los errores que se pueden producir una vez enviados los trabajos y las tareas, así como la manera de buscarlos y solucionarlos.

## <a name="jobs"></a>Trabajos

Un trabajo es una agrupación de una o más tareas en el que las tareas especifican realmente las líneas de comandos que se van a ejecutar.

Al agregar un trabajo, se pueden especificar los parámetros siguientes, lo que puede influir en el modo en que se puede producir un error en el trabajo:

- [Restricciones del trabajo](/rest/api/batchservice/job/add#jobconstraints)
  - Opcionalmente, se puede especificar la propiedad `maxWallClockTime` para establecer la cantidad máxima de tiempo que un trabajo puede estar activo o en ejecución. Si se supera, el trabajo finalizará con la propiedad `terminateReason` establecida en el elemento [executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation) del trabajo.
- [Tarea de preparación del trabajo](/rest/api/batchservice/job/add#jobpreparationtask)
  - Si se especifica, se ejecuta una tarea de preparación del trabajo la primera vez que se ejecuta una tarea de un trabajo en un nodo. Se puede producir un error en la tarea de preparación del trabajo, lo que hará que la tarea no se ejecute y el trabajo no se complete.
- [Tarea de liberación del trabajo](/rest/api/batchservice/job/add#jobreleasetask)
  - Solo se puede especificar una tarea de liberación del trabajo si se configura una tarea de preparación del trabajo. Cuando se termina un trabajo, la tarea de liberación del trabajo se ejecuta en cada uno de los nodos del grupo en los que se ejecutó una tarea de preparación del trabajo. Se puede producir un error en una tarea de liberación del trabajo, pero el trabajo se moverá a un estado de `completed`.

### <a name="job-properties"></a>Propiedades del trabajo

Se deben comprobar los errores de las siguientes propiedades del trabajo:

- "[executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation):
  - La propiedad `terminateReason` puede tener valores para indicar que se superó el elemento `maxWallClockTime`, especificado en las restricciones del trabajo y, por tanto, el trabajo se finalizó. También se puede establecer para indicar que se produjo un error en una tarea si la propiedad `onTaskFailure` del trabajo se estableció de forma adecuada.
  - La propiedad [schedulingError](/rest/api/batchservice/job/get#jobschedulingerror) se establece si se ha producido un error de programación.

### <a name="job-preparation-tasks"></a>Tareas de preparación del trabajo

Si se especifica una [tarea de preparación del trabajo](batch-job-prep-release.md#job-preparation-task) para un trabajo, se ejecutará una instancia de esa tarea la primera vez que se ejecute una tarea del trabajo en un nodo. La tarea de preparación del trabajo configurada en el trabajo se puede considerar como una plantilla de tareas, con varias instancias de la tarea de preparación del trabajo en ejecución, hasta el número de nodos de un grupo.

Las instancias de la tarea de preparación del trabajo se deben comprobar para determinar si se produjeron errores:

- Cuando se ejecuta una tarea de preparación del trabajo, la tarea que desencadenó la tarea de preparación del trabajo pasará a un [estado](/rest/api/batchservice/task/get#taskstate) de `preparing`; si se produce un error en la tarea de preparación del trabajo, la tarea desencadenadora volverá un estado de `active` y no se ejecutará.
- Todas las instancias de la tarea de preparación del trabajo que se han ejecutado se pueden obtener del trabajo mediante la API [List Preparation and Release Task Status](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) (Enumerar estado de las tareas de preparación y liberación). Como con cualquier tarea, hay [información de ejecución](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponible con propiedades como `failureInfo`, `exitCode` y `result`.
- Si se produce un error en las tareas de preparación del trabajo, las tareas del trabajo desencadenador no se ejecutarán, el trabajo no se completará y se bloqueará. El grupo puede quedar sin uso si no hay otros trabajos con tareas que se puedan programar.

### <a name="job-release-tasks"></a>Tareas de liberación del trabajo

Si se especifica una [tarea de liberación del trabajo](batch-job-prep-release.md#job-release-task) para un trabajo, cuando se termina un trabajo se ejecuta una instancia de la tarea de liberación del trabajo en cada nodo del grupo en el que se ejecutó una tarea de preparación del trabajo. Las instancias de la tarea de liberación del trabajo se deben comprobar para determinar si se produjeron errores:

- Todas las instancias de la tarea de liberación del trabajo que se han ejecutado se pueden obtener del trabajo mediante la API [List Preparation and Release Task Status](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) (Enumerar estado de las tareas de preparación y liberación). Como con cualquier tarea, hay [información de ejecución](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponible con propiedades como `failureInfo`, `exitCode` y `result`.
- Si se produce un error en una o varias tareas de liberación del trabajo, el trabajo se finaliza y pasará a un estado de `completed`.

## <a name="tasks"></a>Tareas

Se pueden producir errores en las tareas del trabajo por varias razones:

- La línea de comandos de la tarea produce un error y devuelve un código de salida distinto de cero.
- Se especificaron elementos `resourceFiles` para una tarea, pero se produjo un error que significaba que uno o varios archivos no se descargaron.
- Se especificaron elementos `outputFiles` para una tarea, pero se produjo un error que significaba que uno o varios archivos no se descargaron.
- Se superó el tiempo transcurrido para la tarea, especificado por la propiedad `maxWallClockTime` en las [restricciones](/rest/api/batchservice/task/add#taskconstraints) de la tarea.

En todos los casos, se debe comprobar si hay errores en las siguientes propiedades y obtener información sobre los errores:

- La propiedad [executionInfo](/rest/api/batchservice/task/get#taskexecutioninformation) de las tareas contiene varias propiedades que proporcionan información sobre un error. [result](/rest/api/batchservice/task/get#taskexecutionresult) indica si se ha producido un error en la tarea por cualquier motivo, con `exitCode` y `failureInfo` que proporcionan más información sobre el error.
- La tarea siempre se moverá a un  [estado](/rest/api/batchservice/task/get#taskstate) de `completed`, independientemente de si se ha realizado correctamente o no.

Se deben tener en cuenta el impacto de los errores de las tareas en el trabajo y las dependencias de las tareas. Se puede especificar la propiedad [exitConditions](/rest/api/batchservice/task/add#exitconditions) de una tarea para configurar una acción para las dependencias y para el trabajo.

- En el caso de las dependencias, [DependencyAction](/rest/api/batchservice/task/add#dependencyaction) controla si las tareas que dependen de la tarea con errores se bloquean o se ejecutan.
- En el caso del trabajo, [JobAction](/rest/api/batchservice/task/add#jobaction) controla si la tarea con errores hace que el trabajo se deshabilite, finalice o quede sin cambios.

### <a name="task-command-line-failures"></a>Errores de la línea de comandos de la tarea

Cuando se ejecuta la línea de comandos de la tarea, la salida se escribe en `stderr.txt` y `stdout.txt`. Además, la aplicación puede escribir en archivos de registro específicos de la aplicación.

Si el nodo del grupo en el que se ha ejecutado una tarea sigue existiendo, se pueden obtener y ver los archivos de registro. Por ejemplo, en Azure Portal se muestra y se pueden ver los archivos de registro de una tarea o un nodo del grupo. Varias API también permiten enumerar y obtener los archivos de tareas, como [Get From Task](/rest/api/batchservice/file/getfromtask).

Ya que los grupos y los nodos del grupo suelen ser efímeros, con nodos que se agregan y eliminan continuamente, se recomienda guardar los archivos de registro. [Los archivos de salida de la tarea](./batch-task-output-files.md) son una manera cómoda de guardar los archivos de registro en Azure Storage.

Las líneas de comandos que ejecutan las tareas en los nodos de proceso no se ejecutan en un shell, por lo que no pueden beneficiarse de manera nativa de las características del shell, como la expansión de variables de entorno. Para beneficiarse de estas características, debe [invocar el shell en la línea de comandos](batch-compute-node-environment-variables.md#command-line-expansion-of-environment-variables).

### <a name="output-file-failures"></a>Errores del archivo de salida

En cada carga de archivos, Batch escribe dos archivos de registro en el nodo de proceso, `fileuploadout.txt` y `fileuploaderr.txt`. Puede examinar estos archivos de registro para obtener más información sobre un error específico. En aquellos casos en los que la carga de archivos nunca se intentó, por ejemplo, porque no se pudo ejecutar la tarea propiamente dicha, estos archivos de registro no existen.  

## <a name="next-steps"></a>Pasos siguientes

- Compruebe que la aplicación implementa una comprobación de errores completa; puede ser fundamental detectar y diagnosticar los problemas rápidamente.
- Más información acerca de [trabajos y tareas](jobs-and-tasks.md) y [tareas de preparación y liberación de trabajos](batch-job-prep-release.md).