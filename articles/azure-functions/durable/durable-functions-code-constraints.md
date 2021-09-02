---
title: 'Restricciones de código de orquestador Durable: Azure Functions'
description: Reproducción de funciones de orquestación y restricciones de código para Azure Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: fade080e631385acec46fc59c41e6624280ae9e7
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070152"
---
# <a name="orchestrator-function-code-constraints"></a>Restricciones de código de las funciones de orquestador

Durable Functions es una extensión de [Azure Functions](../functions-overview.md) que le permite crear aplicaciones con estado. Puede usar una [función de orquestador](durable-functions-orchestrations.md) para organizar la ejecución de otras funciones de Durable Functions dentro de una aplicación de función. Las funciones de orquestador tienen estado y son confiables y potencialmente de larga duración.

## <a name="orchestrator-code-constraints"></a>Restricciones de código del orquestador

Las funciones de orquestador usan [el suministro de eventos](/azure/architecture/patterns/event-sourcing) para garantizar la ejecución confiable y mantener el estado de la variable local. El [comportamiento de reproducción](durable-functions-orchestrations.md#reliability) del código de orquestador crea restricciones sobre el tipo de código que puede escribir en una función de orquestador. Por ejemplo, las funciones de orquestador deben ser *deterministas*: una función de orquestador se reproducirá varias veces y debe producir el mismo resultado cada vez.

### <a name="using-deterministic-apis"></a>Uso de API deterministas

En esta sección se proporcionan algunas pautas simples que le permiten garantizar que su código sea determinista.

Las funciones de orquestador pueden llamar a cualquier API en sus lenguajes de destino. Sin embargo, es importante que las funciones de orquestador llamen solo API deterministas. Una *API determinista* es aquella que siempre devuelve el mismo valor con la misma entrada, con independencia de cuándo o con qué frecuencia se le llama.

En la siguiente tabla se muestran ejemplos de API que debe evitar porque *no* son deterministas. Estas restricciones solo se aplican a las funciones de orquestador. Otros tipos de funciones no tienen estas restricciones.

| Categoría de API | Motivo | Solución alternativa |
| ------------ | ------ | ---------- |
| Fechas y horas  | Las API que devuelven la fecha o la hora actual no son deterministas porque el valor devuelto es diferente en cada reproducción. | Use la propiedad[CurrentUtcDateTime](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.currentutcdatetime) en .NET, la API `currentUtcDateTime` en JavaScript, o la API `current_utc_datetime` en Python, que son seguras para la reproducción. De forma similar, evite los objetos de tipo "stopwatch" (como la [clase Stopwatch en .NET](/dotnet/api/system.diagnostics.stopwatch)). Si necesita medir el tiempo transcurrido, almacene el valor de `CurrentUtcDateTime` al principio de la ejecución y reste ese valor de `CurrentUtcDateTime` cuando concluya la ejecución. |
| GUID y UUID  | Las API que devuelven un valor de GUID o UUID aleatorio no son deterministas porque el valor generado es diferente en cada reproducción. | Use [NewGuid](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.newguid) en .NET, o bien `newGuid` en JavaScript y `new_guid` en Python, para generar valores de GUID aleatorios de forma segura. |
| Números aleatorios | Las API que devuelven números aleatorios no son deterministas porque el valor generado es diferente en cada repetición. | Use una función de actividad para devolver números aleatorios a una orquestación. Los valores devueltos de las funciones de actividad siempre son seguros para la reproducción. |
| Enlaces | Los enlaces de entrada y salida normalmente realizan operaciones de E/S y no son deterministas. Una función de orquestador no debe usar directamente los enlaces del [cliente de orquestación](durable-functions-bindings.md#orchestration-client) ni de [entidad de cliente](durable-functions-bindings.md#entity-client). | Use los enlaces de entrada y salida dentro de las funciones de cliente o actividad. |
| Red | Las llamadas de red implican sistemas externos y son no deterministas. | Use funciones de actividad para realizar llamadas de red. Si necesita realizar una llamada HTTP desde la función de orquestador, también tiene la opción de usar las [API HTTP duraderas](durable-functions-http-features.md#consuming-http-apis). |
| Bloqueo de API | Las API de bloqueo como `Thread.Sleep` de .NET y otras API similares pueden causar problemas de rendimiento y escalado en las funciones de orquestador, por lo que deben evitarse. En el plan de consumo de Azure Functions, incluso pueden provocar cargos innecesarios en el tiempo de ejecución. | Puede usar alternativas para bloquear las API cuando estén disponibles. Por ejemplo, use `CreateTimer` para introducir retrasos en la ejecución de la orquestación. Los retrasos en el [temporizador duradero](durable-functions-timers.md) no cuentan en el tiempo de ejecución de una función de orquestador. |
| API asincrónicas | El código de Orchestrator nunca debe iniciar ninguna operación asincrónica excepto mediante el uso de la API `IDurableOrchestrationContext`, la API `context.df` en JavaScript o la API `context` en Python. Por ejemplo, no puede usar `Task.Run`, `Task.Delay` y `HttpClient.SendAsync` en .NET o `setTimeout` y `setInterval` en JavaScript. La instancia de Durable Task Framework se encargará de ejecutar código de orquestador en un solo hilo. Esta no puede interactuar con ningún otro subproceso al que puedan llamar otras API asincrónicas. | Una función de orquestador solo debe realizar llamadas asincrónicas duraderas. Las funciones de actividad deben realizar cualquier otra llamada de API asincrónica. |
| Funciones asincrónicas de JavaScript | No puede declarar las funciones de orquestador de JavaScript como `async` porque el runtime de node.js no garantiza que esas funciones asincrónicas sean deterministas. | Declare las funciones de orquestador de JavaScript como funciones del generador sincrónico. |
| Corrutinas de Python | No pueden declarar las funciones de orquestador de Python como corrutinas; es decir, declararlas con la palabra clave `async`, ya que la semántica de la corrutina no se corresponde con el modelo de reproducción de Durable Functions. | Declare las funciones de orquestador de Python como generadores, lo que significa que debe esperar que la API `context` use `yield` en lugar de `await`.   |
| API de subprocesos | Durable Task Framework ejecuta código de orquestador en un solo subproceso y no puede interactuar con otros. La introducción de nuevos subprocesos en la ejecución de una orquestación puede dar lugar a interbloqueos o ejecuciones no deterministas. | Las funciones de orquestador casi nunca deben usar API de subprocesos. Por ejemplo, en .NET, evite utilizar `ConfigureAwait(continueOnCapturedContext: false)`; así se asegurará de que las continuaciones de tareas se ejecuten en el original de la función de orquestador `SynchronizationContext`. Si tales API son necesarias, limite su uso a funciones de actividad únicamente. |
| Variables estáticas | Evite el uso de variables estáticas no constantes en las funciones de orquestador, ya que sus valores pueden cambiar con el tiempo, lo que resultaría en un comportamiento de tiempo de ejecución no determinista. | Use constantes o limite el uso de variables estáticas a las funciones de actividad. |
| Variables de entorno | No use variables de entorno en las funciones de orquestador. Sus valores pueden cambiar con el tiempo, lo que resulta en un comportamiento de tiempo de ejecución no determinista. | Solo se debe hacer referencia a las variables de entorno desde las funciones de cliente o las funciones de actividad. |
| Bucles infinitos | Evite bucles infinitos en funciones de orquestador. Como Durable Task Framework guarda el historial de ejecución a medida que avanza la función de orquestación, un bucle infinito podría provocar que una instancia de orquestador se quedara sin memoria. | En cuanto a los escenarios de bucle infinito, use API como `ContinueAsNew` en .NET, `continueAsNew` en JavaScript o `continue_as_new` en Python para reiniciar la ejecución de la función y descartar el historial de ejecución anterior. |

Aunque la aplicación de estas restricciones puede parecer difícil al principio, en la práctica son fáciles de seguir.

Durable Task Framework intenta detectar infracciones de las reglas anteriores. Si encuentra una infracción, este marco genera una excepción **NonDeterministicOrchestrationException**. Sin embargo, este comportamiento de detección no detectará todas las infracciones y no debe depender del mismo.

## <a name="versioning"></a>Control de versiones

Una orquestación duradera puede ejecutarse continuamente durante días, meses, años o incluso [eternamente](durable-functions-eternal-orchestrations.md). Cualquier actualización de código realizada en aplicaciones de Durable Functions que afecte a las orquestaciones que no se hayan completado, puede interrumpir el comportamiento de reproducción de esas orquestaciones. Por lo tanto, es importante planificar cuidadosamente las actualizaciones que se harán en el código. Para obtener una descripción más detallada de cómo se debe hacer una versión del código, consulte el artículo [Control de versiones](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Tareas durables

> [!NOTE]
> En esta sección se describen los detalles internos de implementación de Durable Task Framework. Puede usar Durable Functions sin necesidad de conocer esta información. Su único fin es ayudarle a entender el comportamiento de reproducción.

Las tareas que pueden esperar de forma segura en las funciones de orquestador se conocen en ocasiones como *tareas durables*. Durable Task Framework crea y gestiona estas tareas. Algunos ejemplos son las tareas que devuelven **CallActivityAsync**, **WaitForExternalEvent** y **CreateTimer** en las funciones de .NET orchestrator.

Estas tareas duraderas se administran internamente mediante una lista de objetos `TaskCompletionSource` en .NET. Durante la reproducción, estas tareas se crean como parte de la ejecución del código del orquestador. Estas terminan cuando el distribuidor enumera los eventos de historial correspondientes.

La ejecución de las tareas se lleva a cabo sincrónicamente con un único subproceso hasta que se reproduce todo el historial. Las tareas duraderas, que no se completan al final de la reproducción del historial, llevan las acciones correspondientes asociadas. Por ejemplo, un mensaje puede ponerse en cola para llamar a una función de actividad.

La descripción de esta sección sobre el comportamiento del runtime debería ayudarle a comprender por qué una función de orquestador no puede usar las opciones `await` o `yield` en una tarea no duradera. Esto es debido a dos razones: el subproceso del distribuidor no puede esperar a que finalice la tarea, y cualquier devolución de llamada de esa tarea podría crear un error en el estado de seguimiento de la función de orquestador. Existen algunas comprobaciones de tiempo de ejecución que le permitirán detectar estas infracciones.

Para obtener más información acerca de cómo Durable Task Framework ejecuta las funciones de orquestador, consulte el [código fuente de Durable Task en GitHub](https://github.com/Azure/durabletask). En concreto, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) y [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Cómo invocar a suborquestaciones](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Aprenda a controlar las versiones](durable-functions-versioning.md)
