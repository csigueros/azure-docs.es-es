---
title: 'Entidades duraderas: Azure Functions'
description: Más información sobre qué son las entidades duraderas y cómo usarlas en la extensión de Durable Functions para Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: bdc5ba0c21bc55c04c8407e218fd09eecd726be2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603607"
---
# <a name="entity-functions"></a>Funciones de entidad

Las funciones de entidad definen las operaciones de lectura y actualización de pequeños fragmentos de estado, denominados *entidades duraderas*. Al igual que las funciones de orquestador, las funciones de entidad son funciones con un tipo de desencadenador especial, el *desencadenador de entidad*. A diferencia de las funciones de orquestador, las de entidad administran el estado de una entidad de forma explícita, en lugar de representarlo de forma implícita a través del flujo de control.
Las entidades proporcionan un medio para escalar horizontalmente las aplicaciones mediante la distribución del trabajo entre muchas entidades, cada una con un estado de tamaño modesto.

> [!NOTE]
> Las funciones de entidad y la funcionalidad relacionada solo están disponibles en [Durable Functions 2.0](durable-functions-versions.md#migrate-from-1x-to-2x) y versiones posteriores. Actualmente se admiten en .NET, JavaScript y Python.

## <a name="general-concepts"></a>Conceptos generales

Las entidades se comportan de forma algo parecida a pequeños servicios que se comunican mediante mensajes. Cada entidad tiene una identidad única y un estado interno (si existe). Al igual que los servicios u objetos, las entidades realizan operaciones cuando se les pide que lo hagan. Cuando se ejecuta una operación, es posible que actualice el estado interno de la entidad. También puede llamar a servicios externos y esperar una respuesta. Las entidades se comunican con otras entidades, orquestaciones y clientes mediante el uso de mensajes que se envían implícitamente a través de colas de confianza. 

Para evitar conflictos, se garantiza la ejecución en serie de todas las operaciones de una sola entidad, es decir, una después de otra.

> [!NOTE]
> Cuando se invoca una entidad, se procesa su carga hasta su finalización y, después, se programa una nueva ejecución para que se active cuando lleguen entradas futuras. Como resultado, los registros de ejecución de la entidad pueden mostrar una ejecución adicional después de cada invocación de una entidad. Esto es normal.

### <a name="entity-id"></a>El identificador de entidad
A las entidades se accede a través de un identificador único, el *identificador de entidad*. Un identificador de entidad es simplemente un par de cadenas que identifica de forma exclusiva una instancia de entidad. Consta de un:

* **Nombre de entidad**, que es un nombre que identifica el tipo de la entidad. Un ejemplo es "Contador." Este nombre debe coincidir con el nombre de la función que implementa la entidad correspondiente. No distingue entre mayúsculas y minúsculas.
* **Clave de entidad**, que es una cadena que identifica de forma única la entidad entre las demás entidades del mismo nombre. Un ejemplo es un GUID.

Por ejemplo, una `Counter`función de entidad podría usarse para mantener la puntuación en un juego en línea. Cada instancia del juego tiene un identificador de entidad único, como `@Counter@Game1` y `@Counter@Game2`. Todas las operaciones que tienen como destino una entidad determinada requieren la especificación de un identificador de entidad como parámetro.

### <a name="entity-operations"></a>Operaciones de entidad ###

Para invocar una operación en una entidad, especifique:

* **Identificador de entidad** de la entidad de destino.
* **Nombre de la operación**, que es una cadena que especifica la operación que se va a realizar. Por ejemplo, la entidad `Counter` podría admitir las operaciones `add`, `get`o `reset`.
* **Entrada de operación**, que es un parámetro de entrada opcional para la operación. Por ejemplo, la operación de agregar puede tomar una cantidad de entero como entrada.
* **Tiempo programado**, que es un parámetro opcional para especificar el tiempo de entrega de la operación. Por ejemplo, una operación se puede programar de forma confiable para que se ejecute varios días después.

Las operaciones pueden devolver un valor de resultado o un resultado de error, como un error de JavaScript o una excepción de .NET. Las orquestaciones que llamaron a la operación pueden observar este resultado o error.

Una operación de entidad también puede crear, leer, actualizar y eliminar el estado de la entidad. El estado de la entidad se conserva siempre de forma duradera en el almacenamiento.

## <a name="define-entities"></a>Definir entidades

Actualmente, las dos API distintas para definir entidades son:

**Sintaxis basada en funciones**, donde las entidades se representan como funciones y operaciones se envían explícitamente por la aplicación. Esta sintaxis funciona bien para las entidades con un estado simple, pocas operaciones o un conjunto dinámico de operaciones como en marcos de aplicaciones. Esta sintaxis puede ser tediosa de mantener porque no detecta errores de tipo en tiempo de compilación.

**Sintaxis basada en clases (solo .NET)** , donde las entidades y las operaciones se representan mediante clases y métodos. Esta sintaxis genera código más fácilmente legible y permite invocar operaciones con seguridad de tipos. La sintaxis basada en clases es una capa fina en la parte superior de la sintaxis basada en funciones, por lo que ambas variantes se pueden usar indistintamente en la misma aplicación.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Ejemplo: Sintaxis basada en funciones: C#

El código siguiente es un ejemplo de una entidad `Counter` simple implementada como una función duradera. Esta función define tres operaciones, `add`, `reset`y `get`, cada una de las cuales funciona en un estado entero.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Para más información sobre la sintaxis basada en funciones y cómo utilizarla, consulte [sintaxis basada en funciones](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Ejemplo: Sintaxis basada en clases: C#

El ejemplo siguiente es una implementación equivalente de la entidad `Counter` que usa clases y métodos.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

El estado de esta entidad es un objeto de tipo `Counter`, que contiene un campo que almacena el valor actual del contador. Para conservar este objeto en el almacenamiento, se serializa y deserializa en la biblioteca de [Json.NET](https://www.newtonsoft.com/json). 

Para más información sobre la sintaxis basada en clases y cómo utilizarla, consulte [Definición de las clases de entidad](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Ejemplo: Entidad de JavaScript

Las entidades durables están disponibles en JavaScript a partir de la versión **1.3.0** del paquete de npm `durable-functions`. El código siguiente es la entidad `Counter` implementada como una función duradera escrita en JavaScript.

**Counter/function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Counter/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>Ejemplo: Entidad de Python

El código siguiente es la entidad `Counter` implementada como una función duradera escrita en Python.

**Counter/function.json**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Counter/__init__.py**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>Acceso a entidades

Se puede acceder a las entidades mediante una comunicación unidireccional o bidireccional. La terminología siguiente distingue las dos formas de comunicación: 

* **La llamada** a una entidad utiliza la comunicación bidireccional (ida y vuelta). Envía un mensaje de operación a la entidad y, después, espera el mensaje de respuesta antes de continuar. El mensaje de respuesta puede proporcionar un valor de resultado o un resultado de error, como un error de JavaScript o una excepción de .NET. El autor de la llamada observa este resultado o error.
* **La señalización** de una entidad utiliza una comunicación unidireccional (desencadenar y olvidar). Envía un mensaje de operación pero no espera una respuesta. Aunque se garantiza la entrega del mensaje, el remitente no sabe cuándo y no puede observar ningún valor de resultado o error.

Se puede acceder a las entidades desde las funciones de cliente, desde las de orquestador o desde las de entidad. No todos los contextos admiten todas las formas de comunicación:

* En los clientes, puede indicar entidades y puede leer el estado de la entidad.
* Desde las orquestaciones, puede indicar entidades y puede llamar a las entidades.
* Desde dentro de las entidades, puede señalizar entidades.

En los siguientes ejemplos se muestran estas diversas formas de obtener acceso a las entidades.

### <a name="example-client-signals-an-entity"></a>Ejemplo: El cliente señala una entidad

Para acceder a las entidades desde una función de Azure normal, que también se conoce como función cliente, use el [enlace de cliente de entidad](durable-functions-bindings.md#entity-client). En el ejemplo siguiente se muestra una función desencadenada por la cola que señala una entidad mediante este enlace.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Para simplificar, en los siguientes ejemplos se muestra la sintaxis de tipo flexible para tener acceso a las entidades. En general, se recomienda tener [acceso a las entidades a través de interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) porque proporciona más comprobación de tipos.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

El término *señal* significa que la invocación de la API de entidad es unidireccional y asincrónica. No es posible que una función de cliente sepa si la entidad ha procesado la operación. La función de cliente tampoco puede observar valores de resultado ni excepciones. 

### <a name="example-client-reads-an-entity-state"></a>Ejemplo: El cliente lee el estado de una entidad

Las funciones de cliente también pueden consultar el estado de una entidad, tal y como se muestra en el ejemplo siguiente:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

```python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func

async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    entity_state_result = await client.read_entity_state(entityId)
    entity_state = "No state found"
    if entity_state_result.entity_exists:
      entity_state = str(entity_state_result.entity_state)
    return func.HttpResponse(entity_state)
```

---

Las consultas de estado de la entidad se envían al almacén de seguimiento duradero y devuelven el estado guardado más recientemente de la entidad. Este estado siempre es un estado "confirmado", es decir, nunca se presupone un estado intermedio temporal en medio de la ejecución de una operación. No obstante, es posible que este estado esté obsoleto en comparación con el estado en memoria de la entidad. Solo las orquestaciones pueden leer el estado en memoria de una entidad, tal y como se describe en la sección siguiente.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Ejemplo: La orquestación señala y llama a una entidad

Las funciones de orquestador pueden acceder a las entidades mediante el uso de API en el [enlace de desencadenador de orquestación](durable-functions-bindings.md#orchestration-trigger). En el ejemplo de código siguiente se muestra una función de orquestador que llama y señala una `Counter`entidad.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript no admite actualmente la señalización de una entidad desde un orquestador. En su lugar, use `callEntity`.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

Solo las orquestaciones son capaces de llamar a las entidades y obtener una respuesta que puede ser un valor devuelto o una excepción. Las funciones de cliente que usan el [enlace de cliente](durable-functions-bindings.md#entity-client) solo pueden indicar entidades.

> [!NOTE]
> Llamar a una entidad desde una función de orquestador es similar a llamar a una [función de actividad](durable-functions-types-features-overview.md#activity-functions) desde una función de orquestador. La principal diferencia es que las funciones de entidad son objetos duraderos con una dirección, que es el identificador de entidad. Las funciones de entidad admiten la especificación de un nombre de operación. Por otro lado, las funciones de actividad no tienen estado y no tienen el concepto de operaciones.

### <a name="example-entity-signals-an-entity"></a>Ejemplo: La entidad señaliza una entidad

Una función de entidad puede enviar señales a otras entidades, o incluso a sí misma, mientras ejecuta una operación.
Por ejemplo, podemos modificar el `Counter`ejemplo de la entidad anterior para que envíe una señal "alcanzada por un hito" a alguna entidad del monitor cuando el contador alcance el valor 100.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python todavía no admite señales de entidad a entidad. En su lugar, use un orquestador para señalizar entidades.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Coordinación de entidades (actualmente solo .NET)

Puede haber ocasiones en las que necesite coordinar las operaciones entre varias entidades. Por ejemplo, en una aplicación de banca, es posible que tenga entidades que representen cuentas bancarias individuales. Cuando se transfieren fondos de una cuenta a otra, debe asegurarse de que la cuenta de origen tiene fondos suficientes. También debe asegurarse de que las actualizaciones de las cuentas de origen y de destino se realicen de forma transaccional.

### <a name="example-transfer-funds-c"></a>Ejemplo: Transferencia de fondos (C#)

En el siguiente código de ejemplo se transfieren fondos entre dos entidades de cuenta mediante una función de orquestador. La coordinación de las actualizaciones de entidad requiere el uso del `LockAsync` método para crear una _sección crítica_ en la orquestación.

> [!NOTE]
> Para simplificar, en este ejemplo se reutiliza la entidad `Counter` definida previamente. En una aplicación real, sería mejor definir una entidad `BankAccount` más detallada.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

En .NET, `LockAsync` devuelve `IDisposable`, que finaliza la sección crítica cuando se elimina. Este resultado `IDisposable` se puede usar junto con un bloque `using` para obtener una representación sintáctica de la sección crítica.

En el ejemplo anterior, una función de orquestador transfirió fondos de una entidad de origen a una entidad de destino. El método `LockAsync` bloqueó las entidades de la cuenta de origen y de destino. Este bloqueo garantiza que ningún otro cliente podría consultar o modificar el estado de ninguna de las cuentas hasta que la lógica de la orquestación salga de la sección crítica al final de la instrucción `using`. Este comportamiento evita la posibilidad de que se produzca un borrado de la cuenta de origen.

> [!NOTE] 
> Cuando una orquestación finaliza, ya sea normalmente o con un error, todas las secciones críticas en curso se terminan implícitamente y se liberan todos los bloqueos.

### <a name="critical-section-behavior"></a>Comportamiento de la sección crítica

El método `LockAsync` crea una sección crítica en una orquestación. Estas secciones críticas impiden que otras orquestaciones realicen cambios superpuestos en un conjunto especificado de entidades. Internamente, la API `LockAsync` envía operaciones de "bloqueo" a las entidades y vuelve cuando recibe un mensaje de respuesta "bloqueo adquirido" de cada una de estas mismas entidades. Bloquear y desbloquear son operaciones integradas admitidas por todas las entidades.

No se permiten operaciones de otros clientes en una entidad mientras está en estado bloqueado. Este comportamiento garantiza que solo una instancia de orquestación pueda bloquear una entidad a la vez. Si un llamador intenta invocar una operación en una entidad mientras está bloqueada por una orquestación, esa operación se coloca en una cola de operaciones pendiente. No se procesa ninguna operación pendiente hasta que la orquestación que la contiene libera el bloqueo.

> [!NOTE] 
> Este comportamiento es ligeramente diferente de los primitivos de sincronización que se usan en la mayoría de los lenguajes de programación, como la `lock` instrucción en C#. Por ejemplo, en C#, la `lock` instrucción debe ser utilizada por todos los subprocesos para garantizar la sincronización correcta entre varios subprocesos. Sin embargo, las entidades no requieren que todos los llamadores bloqueen explícitamente una entidad. Si algún llamador bloquea una entidad, todas las demás operaciones de esa entidad se bloquean y se ponen en cola detrás de ese bloqueo.

Los bloqueos en entidades son duraderos, por lo que se conservan incluso si se recicla el proceso de ejecución. Los bloqueos se conservan internamente como parte del estado duradero de una entidad.

A diferencia de las transacciones, las secciones críticas no revierten automáticamente los cambios en caso de que se produzcan errores. En su lugar, cualquier control de errores, como reversión o reintento, se debe codificar explícitamente, por ejemplo, mediante la detección de errores o excepciones. Esta opción de diseño es intencionada. La reversión automática de todos los efectos de una orquestación es difícil o imposible en general, ya que las orquestaciones pueden ejecutar actividades y realizar llamadas a servicios externos que no se pueden revertir. Además, los intentos de revertir podrían fallar y requerir un mayor control de errores.

### <a name="critical-section-rules"></a>Reglas de secciones críticas

A diferencia de las primitivas de bloqueo de bajo nivel de la mayoría de los lenguajes de programación, se *garantiza que las secciones críticas no tendrán interbloqueos*. Para evitar los interbloqueos, se aplican las siguientes restricciones: 

* Las secciones críticas no se pueden anidar.
* Las secciones críticas no pueden crear suborquestaciones.
* Las secciones críticas solo pueden llamar a las entidades que hayan bloqueado.
* Las secciones críticas no pueden llamar a la misma entidad mediante varias llamadas paralelas.
* Las secciones críticas solo pueden indicar entidades que no se han bloqueado.

Cualquier infracción de estas reglas produce un error en tiempo de ejecución, como `LockingRulesViolationException` en .NET, que incluye un mensaje que explica qué regla se ha interrumpido.

## <a name="comparison-with-virtual-actors"></a>Comparación con actores virtuales

Muchas de las características de las entidades duraderas están inspiradas en el [modelo de actor](https://en.wikipedia.org/wiki/Actor_model). Si ya está familiarizado con los actores, puede que reconozca muchos de los conceptos que se describen en este artículo. Las entidades duraderas son especialmente similares a [actores virtuales](https://research.microsoft.com/projects/orleans/)o granos, tal y como se ha popularizado en el proyecto de [Orleans](http://dotnet.github.io/orleans/). Por ejemplo:

* Las entidades duraderas son direccionables a través de un identificador de entidad.
* Las operaciones de las entidades duraderas se ejecutan en serie, una cada vez, para evitar condiciones de carrera.
* Las entidades duraderas se crean implícitamente cuando se llaman o señalan.
* Cuando no ejecutan operaciones, las entidades duraderas se descargan en modo silencioso de la memoria.

Hay algunas diferencias importantes que merece la pena mencionar:

* Las entidades duraderas priorizan la durabilidad de la latencia y, por lo tanto, es posible que no sean adecuadas para aplicaciones con requisitos de latencia estrictos.
* Las entidades duraderas no tienen tiempos de espera integrados para los mensajes. En Orleans, se agota el tiempo de espera de todos los mensajes después de un tiempo configurable. El valor predeterminado es 30 segundos.
* Los mensajes enviados entre las entidades se entregan en orden y de forma confiable. En Orleans, se admite la entrega confiable o ordenada para el contenido enviado a través de secuencias, pero no se garantiza para todos los mensajes entre granos.
* Los patrones de solicitud-respuesta en entidades se limitan a las orquestaciones. Desde dentro de las entidades, solo se permite la mensajería unidireccional (también conocida como señalización), como en el modelo de actor original, y a diferencia de los granos de Orleans. 
* Las entidades duraderas no interbloquean. En Orleans, pueden producirse interbloqueos que no se resuelven hasta que los mensajes agotan el tiempo de espera.
* Las entidades duraderas se pueden usar junto con las orquestaciones duraderas y admiten mecanismos de bloqueo distribuido. 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Lea la Guía del desarrollador de entidades duraderas en .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Más información acerca de las centrales de tareas](durable-functions-task-hubs.md)
