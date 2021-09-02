---
title: Programación de los trabajos
description: Utilice la programación de trabajos para administrar las tareas.
ms.topic: how-to
ms.date: 07/16/2021
ms.custom: seodec18
ms.openlocfilehash: a247be1826cd68ef2ac1302c1910eae74b2d65b6
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114405768"
---
# <a name="schedule-jobs-for-efficiency"></a>Programación de trabajos para mejorar la eficacia

La programación de trabajos de Batch le permite priorizar los trabajos que desea ejecutar en primer lugar y tener en cuenta las [tareas que tienen dependencias en otras tareas](batch-task-dependencies.md). Mediante la programación de los trabajos, puede asegurarse de que se usa la menor cantidad de recursos. Los nodos se pueden retirar cuando no son necesarios y las tareas que dependen de otras tareas se inician Just-in-time para optimizar los flujos de trabajo. También puede establecer los trabajos en autocompletar, ya que solo se ejecuta un trabajo a la vez y no se iniciará un nuevo trabajo hasta que se complete el anterior.

Las tareas que programe mediante la tarea del administrador de trabajos se asociarán con un trabajo. La tarea del administrador de trabajos creará las tareas para el trabajo. Para ello, la tarea del administrador de trabajos debe autenticarse con la cuenta de Batch. Use el token de acceso AZ_BATCH_AUTHENTICATION_TOKEN. El token le permitirá el acceso al resto del trabajo.

Para administrar un trabajo mediante la CLI de Azure, consulte [az batch job-schedule](/cli/azure/batch/job-schedule). También puede crear programaciones de trabajo en Azure Portal.

## <a name="schedule-a-job-in-the-azure-portal"></a>Programación de un trabajo en Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Seleccione la cuenta de Batch en la que desea programar los trabajos.
1. En el panel de navegación izquierdo, seleccione **Programas de trabajos**.
1. Seleccione **Agregar** para crear una programación de trabajo.
1. En **Formulario básico**, especifique la siguiente información:
   - **Id. del programa de trabajos**: un identificador único de esta programación de trabajos.
   - **Nombre para mostrar**: este nombre es opcional y no tiene que ser único. El nombre tiene una longitud máxima de 1024 caracteres.
   - **No ejecutar hasta**: especifica la hora más temprana a la que se ejecutará el trabajo. Si no establece esta configuración, la programación está lista para ejecutar trabajos inmediatamente.
   - **No ejecutar después de**: no se ejecutará ningún trabajo después de la hora que especifique aquí. Si no se especifica una hora, se crea una programación de trabajo periódica que permanece activa hasta que se finalice explícitamente.
   - **Intervalo de periodicidad**: seleccione **Habilitado** si desea especificar la cantidad de tiempo entre trabajos. Solo puede tener un trabajo programado a la vez, por lo que si es el momento de crear un nuevo trabajo en una programación de trabajo, pero el trabajo anterior todavía está en ejecución, el servicio Batch no creará el nuevo trabajo hasta que finalice el trabajo anterior.
   - **Ventana de inicio**: seleccione **Personalizado** si desea especificar el intervalo de tiempo en el que se debe crear un trabajo. Si no se crea un trabajo en esta ventana, no se creará ningún nuevo trabajo hasta la siguiente repetición de la programación.

     :::image type="content" source="media/batch-job-schedule/add-job-schedule-02.png" alt-text="Captura de pantalla de las opciones de Agregar programa de trabajos en Azure Portal.":::  

1. En la parte inferior del formulario básico, especifique el grupo en el que desea que se ejecute el trabajo. Para elegir entre una lista de grupos de la cuenta de Batch, seleccione **Actualizar**.
1. Junto con el **Id. del grupo**, escriba la siguiente información:
   - **Tarea de configuración del trabajo**: seleccione **Actualizar** para asignar un nombre y configurar la tarea del administrador de trabajos, así como a las tareas de preparación y publicación de trabajos, si las va a usar.
   - **Nombre para mostrar**: este nombre es opcional y no tiene que ser único. El nombre tiene una longitud máxima de 1024 caracteres.
   - **Prioridad**: use el control deslizante para establecer una prioridad para el trabajo o escriba un valor en el cuadro.
   - **Tiempo de reloj máximo**: seleccione **Personalizado** si desea establecer un límite máximo de tiempo para que el trabajo se ejecute. Si lo hace, Batch finalizará el trabajo si no se completa dentro de ese período de tiempo.
   - **Número máximo de reintentos de tareas**: seleccione **Personalizado** si desea especificar el número de veces que se puede reintentar una tarea o **Ilimitado** si desea que la tarea se pruebe tantas veces como sea necesario. Esto no es lo mismo que el número de reintentos que puede tener una llamada de API.
   - **Cuando se completan todas las tareas**: el valor predeterminado es NoAction, pero puede seleccionar **TerminateJob** si prefiere finalizar el trabajo cuando se hayan completado todas las tareas (o si no hay ninguna tarea en el trabajo).
   - **Cuando se produce un error en una tarea**: se produce un error en una tarea si se agota el número de reintentos o se produce un error al iniciar la tarea. El valor predeterminado es NoAction, pero puede seleccionar **PerformExitOptionsJobAction** si prefiere realizar la acción asociada a la condición de salida de la tarea si se produce un error.

     :::image type="content" source="media/batch-job-schedule/add-job-schedule-03.png" alt-text="Captura de pantalla de las opciones de especificación de trabajos para una nueva programación de trabajo en Azure Portal.":::

1. Seleccione **Guardar** para crear la programación del trabajo.

Para realizar un seguimiento de la ejecución del trabajo, vuelva a **Programas de trabajos** y seleccione la programación del trabajo. Expanda **Información de ejecución** para ver los detalles. También puede finalizar, eliminar o deshabilitar la programación de trabajos desde esta pantalla.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [trabajos y tareas](jobs-and-tasks.md).
- [Creación de dependencias de tareas](batch-task-dependencies.md) para ejecutar las tareas que dependan de otras tareas.
