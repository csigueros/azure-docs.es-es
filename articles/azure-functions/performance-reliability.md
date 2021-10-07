---
title: Mejora del rendimiento y la confiabilidad de Azure Functions
description: Aprenda a mejorar el rendimiento y la confiabilidad de la ejecución de funciones en Azure.
ms.topic: conceptual
ms.date: 08/25/2021
ms.openlocfilehash: a554407b3d3b222507245823c1962071bbebe929
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699784"
---
# <a name="improve-the-performance-and-reliability-of-azure-functions"></a>Mejora del rendimiento y confiabilidad de Azure Functions

En este artículo se proporcionan instrucciones para mejorar el rendimiento y la confiabilidad de sus aplicaciones de función [sin servidor](https://azure.microsoft.com/solutions/serverless/). Para obtener un conjunto más general de procedimientos recomendados de Azure Functions, consulte [Procedimientos recomendados de Azure Functions](functions-best-practices.md).

Éstos son los procedimientos recomendados para crear y diseñar las soluciones sin servidor mediante Azure Functions.

## <a name="avoid-long-running-functions"></a>Evitar funciones de ejecución prolongada

Las funciones grandes de ejecución prolongada pueden causar problemas de tiempo de espera inesperados. Para más información sobre los tiempos de expiración de un plan de hospedaje determinado, consulte [Duración del tiempo de tiempo de expiración de una aplicación de funciones](functions-scale.md#timeout).

Una función puede llegar a ser grande debido a sus numerosas dependencias de Node.js. La importación de las dependencias también puede provocar mayores tiempos de carga que dan lugar a tiempos de expiración inesperados. Las dependencias se cargan explícita e implícitamente. Un módulo único cargado por el código puede cargar sus propios módulos adicionales.

Siempre que sea posible, refactorice funciones grandes en conjuntos más pequeños de funciones que trabajen juntos y devuelvan respuestas rápidas. Por ejemplo, un webhook o una función de desencadenador HTTP podría requerir una respuesta de confirmación en un determinado período de tiempo. Es habitual que los webhooks requieran una respuesta inmediata. Puede pasar la carga útil de desencadenador HTTP a una cola para ser procesada por una función de desencadenador de cola. Este enfoque permite aplazar el trabajo real y devolver una respuesta inmediata.

## <a name="cross-function-communication"></a>Comunicación entre funciones

[Durable Functions](durable/durable-functions-overview.md) y [Azure Logic Apps](../logic-apps/logic-apps-overview.md) se han creado para administrar las transiciones de estado y las comunicaciones entre varias funciones.

Si no usa Durable Functions ni Logic Apps para integrar varias funciones, es mejor usar colas de almacenamiento para la comunicación entre funciones. La razón principal es que las colas de almacenamiento son más baratas y mucho más fáciles de aprovisionar que otras opciones de almacenamiento.

Los mensajes individuales de una cola de almacenamiento tienen un límite de tamaño de 64 KB. Si tiene que pasar mensajes más grandes entre funciones, se podría usar una cola de Azure Service Bus para admitir tamaños de mensaje de hasta 256 KB en el nivel Estándar y hasta 1 MB en el nivel Premium.

Temas de Service Bus son útiles si necesita filtrado de mensajes antes del procesamiento.

Los concentradores de eventos son útiles para admitir comunicaciones de gran volumen.

## <a name="write-functions-to-be-stateless"></a>Escritura de funciones para que no tengan estado

Si es posible, las funciones no deben tener estado y ser idempotentes. Asociar cualquier información de estado necesaria con los datos. Por ejemplo, un pedido para procesar probablemente tendría un miembro `state` asociado. Una función podría procesar un pedido en función de ese estado mientras que la propia función permanece sin estado.

Las funciones idempotentes se recomiendan especialmente con desencadenadores de temporizador. Por ejemplo, si tiene algo que debe ejecutarse una vez al día obligatoriamente, escríbalo para poder ejecutarse en cualquier momento durante el día con los mismos resultados. La función puede salir cuando no haya ningún trabajo para un día determinado. Asimismo, si una ejecución anterior no se pudo completar, la siguiente ejecución debe continuar donde se quedó.

## <a name="write-defensive-functions"></a>Escritura de funciones defensivas

Suponga que la función podría encontrarse con una excepción en cualquier momento. Diseñe las funciones con la capacidad de continuar a partir de un punto de error anterior durante la siguiente ejecución. Considere un escenario que requiere las siguientes acciones:

1. Consulta de 10 000 filas en una base de datos.
2. Cree un mensaje de cola para cada una de esas filas para procesar más abajo la línea.

Dependiendo de lo complejo que sea el sistema, es posible que haya servicios de bajada implicados con un comportamiento incorrecto, interrupciones de red, límites de cuota alcanzados, etc. Todo esto puede afectar a su función en cualquier momento. Debe diseñar las funciones para que estén preparadas para ello.

¿Cómo reacciona el código si se produce un error después de insertar 5000 de esos elementos en una cola para su procesamiento? Realice un seguimiento de elementos de un conjunto que ha completado. En caso contrario, podría insertarlos la próxima vez. Esta doble inserción puede afectar seriamente al flujo de trabajo, por lo que debe [hacer que las funciones sean idempotentes](functions-idempotent.md). 

Si ya se ha procesado un elemento de la cola, permita que la función sea no operativa.

Aproveche las medidas defensivas ya proporcionadas para los componentes que se usa en la plataforma de Azure Functions. Por ejemplo, vea la información sobre el **tratamiento de mensajes dudosos en la cola** en la documentación de [desencadenadores y enlaces de cola de Azure Storage](functions-bindings-storage-queue-trigger.md#poison-messages).

## <a name="function-organization-best-practices"></a>Procedimientos recomendados de la organización de funciones

Como parte de la solución, puede desarrollar y publicar varias funciones. Estas funciones suelen combinarse en una única aplicación de funciones, pero también se pueden ejecutar en aplicaciones de funciones independientes. En los planes de hospedaje Premium y dedicado (App Service), varias aplicaciones de funciones también pueden compartir los mismos recursos al ejecutarse en el mismo plan. La forma de agrupar las funciones y las aplicaciones de funciones puede afectar al rendimiento, el escalado, la configuración, la implementación y la seguridad de la solución global. No hay reglas que se apliquen a todos los escenarios, por lo que debe tener en cuenta la información de esta sección al planear y desarrollar las funciones.

### <a name="organize-functions-for-performance-and-scaling"></a>Organización de funciones para rendimiento y escalado

Cada función que se crea tiene una superficie de memoria. Aunque esta superficie suele ser pequeña, tener demasiadas funciones en una aplicación de funciones puede dar lugar a un inicio más lento de la aplicación en nuevas instancias. También significa que el uso de memoria general de la aplicación de funciones puede ser mayor. Es difícil decir cuántas funciones deben estar en una única aplicación, lo que depende de la carga de trabajo concreta. Sin embargo, si la función almacena una gran cantidad de datos en la memoria, considere la posibilidad de tener menos funciones en una única aplicación.

Si ejecuta varias aplicaciones de funciones en un plan Premium único o en un plan dedicado (App Service), todas estas aplicaciones comparten los mismos recursos asignados al plan. Si tiene una aplicación de funciones que tiene un requisito de memoria mucho mayor que las demás, usa una cantidad desproporcionada de recursos de memoria en cada instancia en la que se implementa la aplicación. Dado que esto podría dejar menos memoria disponible para las demás aplicaciones en cada instancia, es posible que quiera ejecutar una aplicación de funciones que use mucha memoria, como esta, en su propio plan de hospedaje independiente.

> [!NOTE]
> Al usar el [Plan de consumo](./functions-scale.md), se recomienda colocar siempre cada aplicación en su propio plan, ya que las aplicaciones se escalan de forma independiente de todos modos.

Considere si quiere agrupar funciones con distintos perfiles de carga. Por ejemplo, si tiene una función que procesa muchos miles de mensajes de cola, y otra a la que solo se llama ocasionalmente, pero tiene requisitos de memoria elevados, es posible que quiera implementarlas en aplicaciones de funciones independientes para que obtengan sus propios conjuntos de recursos y se escalen de forma independiente entre sí.

### <a name="organize-functions-for-configuration-and-deployment"></a>Organización de funciones para configuración e implementación

Las aplicaciones de funciones tienen un archivo `host.json`, que se usa para configurar el comportamiento avanzado de los desencadenadores de funciones y Azure Functions Runtime. Los cambios en el archivo `host.json` se aplican a todas las funciones de la aplicación. Si tiene algunas funciones que necesitan configuraciones personalizadas, considere la posibilidad de moverlas a su propia aplicación de funciones.

Todas las funciones de un proyecto local se implementan juntas como conjunto de archivos en la aplicación de funciones en Azure. Es posible que tenga que implementar funciones individuales por separado o usar características como [ranuras de implementación](./functions-deployment-slots.md) para algunas funciones y no para otras. En tales casos, debe implementar estas funciones (en proyectos de código independientes) en diferentes aplicaciones de funciones.

### <a name="organize-functions-by-privilege"></a>Organización de funciones por privilegio

Las cadenas de conexión y otras credenciales almacenadas en la configuración de la aplicación proporcionan a todas las funciones de la aplicación de funciones el mismo conjunto de permisos en el recurso asociado. Considere la posibilidad de minimizar el número de funciones con acceso a credenciales específicas moviendo las funciones que no las utilizan a una aplicación de funciones independiente. Siempre puede usar técnicas como el [encadenamiento de funciones](/learn/modules/chain-azure-functions-data-using-bindings/) para pasar datos entre funciones de diferentes aplicaciones de funciones.  

## <a name="scalability-best-practices"></a>Procedimientos recomendados de escalabilidad

Hay una serie de factores que afectan a cómo se escalan las instancias de la aplicación de función. Se proporcionan más detalles en la documentación sobre [escalado de funciones](functions-scale.md).  A continuación se indican algunos procedimientos recomendados para garantizar una escalabilidad óptima de una aplicación de función.

### <a name="share-and-manage-connections"></a>Compartir y administrar conexiones

Vuelva a usar las conexiones con los recursos externos, siempre que le sea posible. Consulte [Administración de conexiones en Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Evitar compartir cuentas de almacenamiento

Al crear una aplicación de función, debe asociarla a una cuenta de almacenamiento. La conexión de la cuenta de almacenamiento se mantiene en [el ajuste de la aplicación AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage).

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>No mezclar código de prueba y producción en la misma aplicación de función

Las funciones dentro de una aplicación de función compartan recursos. Por ejemplo, la memoria se comparte. Si usa una aplicación de función en producción, no agregue recursos y funciones relacionados con pruebas a ella. Se podría producir una sobrecarga inesperada durante la ejecución de código de producción.

Asegúrese de cargar en las aplicaciones de función de producción. La memoria se promedia entre cada función de la aplicación.

Si tiene un ensamblado compartido al que se hace referencia en varias funciones. NET, colóquelo en una carpeta compartida común. En caso contrario, podría implementar accidentalmente varias versiones del mismo binario que se comporten de manera diferente entre funciones.

No use el registro detallado en el código de producción, ya que afecta negativamente al rendimiento.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Uso del código asincrónico pero evitar las llamadas de bloqueo

La programación asincrónica es un procedimiento recomendado, especialmente cuando implica operaciones de bloqueo de E/S.

En C#, evite siempre las referencias a la propiedad `Result` o las llamadas al método `Wait` en una instancia `Task`. Este enfoque puede provocar el agotamiento de subprocesos.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Uso de varios procesos de trabajo

De forma predeterminada, cualquier instancia de host de Functions utiliza un único proceso de trabajo. Para mejorar el rendimiento, especialmente con los tiempos de ejecución de un solo subproceso, como Python, use [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) para aumentar el número de procesos de trabajo por host (hasta 10). Al hacerlo, Azure Functions intenta distribuir uniformemente las invocaciones de función simultáneas en estos trabajos.

FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que Functions crea al escalar horizontalmente la aplicación para satisfacer la demanda.

### <a name="receive-messages-in-batch-whenever-possible"></a>Recepción de mensajes en lotes siempre que sea posible

Algunos desencadenadores como Event Hubs permiten la recepción de un lote de mensajes en una única invocación.  El procesamiento de mensajes por lotes tiene un rendimiento mucho mejor.  Puede configurar el tamaño de lote máximo en el archivo `host.json` tal como se detalla en la [documentación de referencia sobre host.json](functions-host-json.md)

Para las funciones de C#, puede cambiar el tipo a una matriz fuertemente tipada.  Por ejemplo, en lugar de `EventData sensorEvent` la signatura del método podría ser `EventData[] sensorEvent`.  Para otros idiomas debe establecer explícitamente la propiedad de cardinalidad de `function.json` en `many` para habilitar el procesamiento por lotes [tal y como se muestra aquí](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configuración de los comportamientos de host para controlar mejor la simultaneidad

El archivo `host.json` de la aplicación de función permite la configuración de comportamientos del sistema de tiempo de ejecución y de desencadenadores del host.  Además de los comportamientos del procesamiento por lotes, puede administrar la simultaneidad para varios desencadenadores. Frecuentemente, el ajustar los valores de estas opciones puede hacer que cada instancia se escale adecuadamente para satisfacer la demanda de las funciones que se invocan.

La configuración del archivo host.json se aplica a todas las funciones de la aplicación, dentro de una *única instancia* de la función. Por ejemplo, si tuviera una aplicación de funciones con dos funciones HTTP y solicitudes [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) establecidas en 25, una solicitud a cualquiera de los desencadenadores HTTP contaría las 25 solicitudes simultáneas compartidas.  Cuando esa aplicación de funciones se escala a 10 instancias, las diez funciones permiten eficazmente 250 solicitudes simultáneas (10 instancias * 25 solicitudes simultáneas por cada instancia). 

En el [artículo de configuración de host.json](functions-host-json.md) hay otras opciones de configuración de host.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Administración de conexiones en Azure Functions](manage-connections.md)
* [Procedimientos recomendados de Azure App Service](../app-service/app-service-best-practices.md)
