---
title: Sugerencias de rendimiento de Azure Cosmos DB para el SDK de .NET v2
description: Obtenga información sobre las opciones de configuración de cliente para mejorar el rendimiento del SDK de .NET v2 en Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/08/2021
ms.author: sngun
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: b120d955b6cfc86922a7ea57af755c528877538b
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113516640"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Sugerencias de rendimiento para Azure Cosmos DB y el SDK de .NET v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK de .NET v2](performance-tips.md)
> * [SDK de Java v4](performance-tips-java-sdk-v4-sql.md)
> * [Versión 2 del SDK de Java asincrónico](performance-tips-async-java.md)
> * [SDK de Java v2 sincrónico](performance-tips-java.md)

Azure Cosmos DB es una base de datos distribuida rápida y flexible que se escala sin problemas con una latencia y un rendimiento garantizados. No es necesario realizar cambios de arquitectura importantes ni escribir el código complejo para escalar la base de datos con Azure Cosmos DB. Escalar o reducir verticalmente es tan sencillo como realizar una única llamada API. Para más información, consulte [cómo aprovisionar el rendimiento del contenedor](how-to-provision-container-throughput.md) o [cómo aprovisionar el rendimiento de la base de datos](how-to-provision-database-throughput.md). Sin embargo, dado que se tiene acceso a Azure Cosmos DB a través de llamadas de red, existen optimizaciones del lado cliente que se pueden realizar para lograr el máximo rendimiento cuando se usa el [del SDK de .NET para SQL](sql-api-sdk-dotnet-standard.md).

Por lo tanto, si está intentando mejorar el rendimiento de la base de datos, tenga en cuenta estas opciones:

## <a name="upgrade-to-the-net-v3-sdk"></a>Actualización al SDK de .NET v3

Se publicó el [SDK de .NET v3](https://github.com/Azure/azure-cosmos-dotnet-v3). Si usa el SDK de .NET v3, consulte la información siguiente en la [guía de rendimiento de .NET v3](performance-tips-dotnet-sdk-v3-sql.md):

- Valores predeterminados en el modo TCP directo
- Compatibilidad con Stream API
- Compatibilidad con el serializador personalizado para permitir el uso de System.Text.JSON
- Compatibilidad integrada en bloque y en masa

## <a name="hosting-recommendations"></a><a id="hosting"></a> Recomendaciones sobre hospedaje

**En el caso de cargas de trabajo con un uso intensivo de consultas, use Windows de 64 bits en lugar del procesamiento de host de Windows de 32 bits y Linux**

Se recomienda el procesamiento de host de Windows de 64 bits para mejorar el rendimiento. El SDK de SQL incluye un archivo ServiceInterop.dll nativo para analizar y optimizar consultas localmente. ServiceInterop.dll solo se admite en la plataforma Windows x64. En el caso de Linux y otras plataformas no compatibles en las que ServiceInterop.dll no está disponible, se realiza una llamada de red adicional a la puerta de enlace para obtener la consulta optimizada. Los siguientes tipos de aplicaciones utilizan el procesamiento de host de 32 bits de forma predeterminada. Para cambiar el procesamiento de host al procesamiento de 64 bits, siga estos pasos según el tipo de la aplicación:

- En el caso de las aplicaciones ejecutables, puede cambiar el procesamiento de host estableciendo el [destino de la plataforma](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) en **x64** en la ventana **Propiedades del proyecto**, en la pestaña **Compilar**.

- Para proyectos de prueba basados en VSTest, puede cambiar el procesamiento del host seleccionando **Prueba** >  **Configuración de prueba** >  **Arquitectura de procesador predeterminada como X64** en el menú de **Prueba** de Visual Studio.

- En el caso de las aplicaciones Web de ASP.NET implementadas de forma local, puede cambiar el procesamiento de host seleccionando **Usar la versión de 64 bits de IIS Express para proyectos y sitios web** en **Herramientas** > **Opciones** > **Proyectos y Proyectos** > **de Soluciones Web**.

- En el caso de las aplicaciones Web de ASP.NET implementadas en Azure, puede cambiar el procesamiento de host seleccionando la plataforma de **64 bits** en **Configuración de la aplicación** en el Azure Portal.

> [!NOTE] 
> De forma predeterminada, los nuevos proyectos de Visual Studio se establecen en **cualquier CPU**. Se recomienda establecer el proyecto en **x64** para que no cambie a **x86**. Un proyecto establecido para **cualquier CPU** puede cambiar fácilmente a **x86** si se agrega una dependencia de solo x86.<br/>
> ServiceInterop.dll debe estar en la carpeta desde la que se ejecuta la DLL del SDK. Esto solo debe ser un problema si se copian manualmente los archivos DLL o los sistemas de compilación o implementación personalizados.
    
**Activación de la recolección de elementos no utilizados (GC) del lado servidor**

La reducción de la frecuencia de recolección de elementos no utilizados puede ayudar en algunos casos. En .NET, establezca [gcServer](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element) en `true`.

**Escalado horizontal de la carga de trabajo de cliente**

Si va a realizar pruebas en niveles de alto rendimiento (más de 50 000 RU/s), la aplicación del cliente podría convertirse en el cuello de botella debido a que el equipo se limita al uso de la CPU o la red. Si llega a este punto, puede seguir insertando la cuenta de Azure Cosmos DB mediante la escala horizontal de las aplicaciones cliente en varios servidores.

> [!NOTE] 
> Un uso elevado de CPU puede provocar el aumento de la latencia, así como excepciones de tiempo de espera de solicitud.

## <a name="networking"></a><a id="networking"></a> Redes

**Directiva de conexión: uso del modo de conexión directa**

El modo de conexión predeterminado del SDK de .NET V2 es la puerta de enlace. El modo de conexión se configura durante la construcción de la instancia de `DocumentClient` mediante el parámetro `ConnectionPolicy`. Si usa el modo directo, también debe establecer `Protocol` mediante el parámetro `ConnectionPolicy`. Para obtener más información sobre las distintas opciones de conectividad, vea el artículo [Modos de conectividad](sql-sdk-connection-modes.md).

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**Agotamiento de puertos efímeros**

Si en las instancias detecta un volumen de conexiones alto o un uso elevado de los puertos, compruebe primero que las instancias del cliente son singleton. Es decir, las instancias de cliente deben ser únicas para la duración de la aplicación.

Cuando se ejecuta en el protocolo TCP, el cliente se optimiza para la latencia mediante conexiones de larga duración en lugar de con el protocolo HTTPS, que finaliza las conexiones tras dos minutos de inactividad.

En escenarios en los que tiene acceso disperso y observa un recuento de conexiones superior en comparación con el acceso del modo de puerta de enlace, puede hacer lo siguiente:

* Configure la propiedad [ConnectionPolicy.PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) en `PrivatePortPool` (en vigor con la versión de Framework >= 4.6.1 y la versión de .NET Core >= 2.0): Esta propiedad permite al SDK usar un pequeño grupo de puertos efímeros para diferentes puntos de conexión de destino de Azure Cosmos DB.
* Configure la propiedad [ConnectionPolicy.IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) para que sea mayor o igual que 10 minutos. Los valores recomendados son entre 20 minutos y 24 horas.

**Llamada a OpenAsync para evitar la latencia de inicio en la primera solicitud**

De forma predeterminada, la primera solicitud tiene una latencia mayor porque debe capturar la tabla de enrutamiento de direcciones. Cuando use [SDK V2](sql-api-sdk-dotnet.md), llame a `OpenAsync()` una vez durante la inicialización para evitar esta latencia de inicio en la primera solicitud. La llamada tiene el aspecto siguiente: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` generará solicitudes para obtener la tabla de enrutamiento de direcciones para todos los contenedores de la cuenta. En el caso de las cuentas que tienen muchos contenedores pero cuya aplicación tiene acceso a un subconjunto de ellos, `OpenAsync` generaría una cantidad innecesaria de tráfico, lo que haría que la inicialización fuera lenta. Por lo tanto, el uso de `OpenAsync` podría no ser útil en este escenario porque ralentiza el inicio de la aplicación.

**Para el rendimiento, colocar a los clientes de la misma región de Azure**

Cuando sea posible, coloque las aplicaciones que llaman a Azure Cosmos DB en la misma región que la base de datos de Azure Cosmos DB. Esta es una comparación aproximada: las llamadas a Azure Cosmos DB en la misma región se completan entre 1 ms y 2 ms, pero la latencia entre la costa oeste y el este de EE. UU. es superior a 50 ms. Esta latencia puede variar de una solicitud a otra, en función de la ruta realizada por la solicitud a medida que pasa del cliente al límite del centro de recursos de Azure. Para obtener la menor latencia posible, asegúrese de que la aplicación que realiza la llamada se encuentra dentro de la misma región de Azure que el punto de conexión de Azure Cosmos DB aprovisionado. Para obtener una lista de las regiones disponibles, vea [Regiones de Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="La Directiva de conexión de Azure Cosmos DB" border="false":::

**Aumentar el número de subprocesos o tareas**
<a id="increase-threads"></a>

Dado que las llamadas a Azure Cosmos DB se realizan a través de la red, puede que tenga que cambiar el grado de paralelismo de las solicitudes para que la aplicación cliente dedique tiempo mínimo a esperar entre solicitudes. Por ejemplo, si usa la [biblioteca TPL](/dotnet/standard/parallel-programming/task-parallel-library-tpl) de .NET, cree en el orden de cientos de tareas que leen o escriben en Azure Cosmos DB.

**Habilitación de la redes aceleradas**
 
Para reducir la latencia y la vibración de la CPU, se recomienda habilitar redes aceleradas en las máquinas virtuales de cliente. Consulte [Creación de una máquina virtual Windows con redes aceleradas](../virtual-network/create-vm-accelerated-networking-powershell.md) o [Creación de una máquina virtual Linux con redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Uso del SDK

**Instalación del SDK más reciente**

Los SDK de Azure Cosmos DB se mejoran constantemente para proporcionar el mejor rendimiento. Consulte las páginas del [SDK de Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) para determinar las mejoras de los SDK y las revisiones más recientes.

**Uso de un cliente de Azure Cosmos DB singleton para aumentar la duración de la aplicación**

Cada instancia de `DocumentClient` es segura para subprocesos y realiza una administración eficaz de la conexión y el almacenamiento en caché de direcciones cuando funciona en modo directo. Para permitir una administración de conexión eficaz y un mejor rendimiento del cliente de SDK, se recomienda usar una sola instancia por `AppDomain` durante la vigencia de la aplicación.

**Evitar llamadas de bloqueo**

El SDK de Cosmos DB debe estar diseñado para procesar muchas solicitudes a la vez. Las API asincrónicas permiten que un pequeño grupo de subprocesos controle miles de solicitudes simultáneas sin esperar a las llamadas de bloqueo. En lugar de esperar a que se complete una tarea sincrónica de larga duración, el subproceso puede trabajar en otra solicitud.

Un problema de rendimiento común en las aplicaciones que usan el SDK de Cosmos DB es que las llamadas de bloqueo podrían ser asincrónicas. Muchas llamadas de bloqueo sincrónicas conducen a la [escasez del grupo de subprocesos](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) y a tiempos de respuesta degradados.

**No**:

* Bloquee la ejecución asincrónica mediante la llamada a [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) o [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Use [Task.Run](/dotnet/api/system.threading.tasks.task.run) para que una API sincrónica sea asincrónica.
* Adquiera bloqueos en rutas de acceso de código comunes. El SDK de Cosmos DB tiene el mejor rendimiento cuando se diseña para ejecutar código en paralelo.
* Llame a [Task.Run](/dotnet/api/system.threading.tasks.task.run) y espere inmediatamente. ASP.NET Core ya ejecuta código de aplicación en los subprocesos normales del grupo de subprocesos, por lo que la llamada a Task.Run solo da lugar a la programación adicional innecesaria del grupo de subprocesos. Aunque el código programado bloquee un subproceso, Task.Run no lo impide.
* No use ToList() en `DocumentClient.CreateDocumentQuery(...)` si se usan llamadas de bloqueo para purgar la consulta de manera sincrónica. Use [AsDocumentQuery()](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/a4348f8cc0750434376b02ae64ca24237da28cd7/samples/code-samples/Queries/Program.cs#L690) para purgar la consulta de manera asincrónica.

**Sí**:

* Llame a las API de Cosmos DB de manera asincrónica.
* Toda la pila de llamadas es asincrónica para beneficiarse de los patrones [async/await](/dotnet/csharp/programming-guide/concepts/async/).

Un generador de perfiles, como [PerfView](https://github.com/Microsoft/perfview), se puede usar para buscar subprocesos que se agregan con frecuencia al [grupo de subprocesos](/windows/desktop/procthread/thread-pools). El evento `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` indica que se agregó un subproceso al grupo de subprocesos.

**Aumentar System.Net MaxConnections por host al usar el modo de puerta de enlace**

Las solicitudes de Azure Cosmos DB se realizan a través de HTTPS o REST cuando se usa el modo de puerta de enlace. Están sujetos al límite de conexiones predeterminado por nombre de host o dirección IP. Es posible que tenga que establecer `MaxConnections` en un valor superior (de 100 a 1 000) para que la biblioteca de cliente pueda utilizar varias conexiones simultáneas para Azure Cosmos DB. En el SDK de .NET 1.8.0 y versiones posteriores, el valor predeterminado de [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) es 50. Para cambiar el valor, puede establecer [Documents.Client.ConnectionPolicy.MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) en un valor superior.

**Optimizar consultas paralelas para colecciones con particiones**

El SDK de .NET para SQL 1.9.0 y versiones posteriores admiten consultas paralelas, que permiten consultar una colección con particiones en paralelo. Para obtener más información, consulte [ejemplos de código](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs) relacionados para trabajar con los SDK. Las consultas paralelas están diseñadas para proporcionar una mejor latencia de consulta y rendimiento que su homólogo en serie. Las consultas paralelas proporcionan dos parámetros que se pueden adaptar para ajustarse a sus requisitos: 
- `MaxDegreeOfParallelism` controla el número máximo de particiones que se pueden consultar en paralelo. 
- `MaxBufferedItemCount` controla el número de resultados capturados previamente.

***Grado de optimización del paralelismo***

La consulta en paralelo funciona consultando varias particiones en paralelo. Sin embargo, los datos de una partición individual se capturan en serie con respecto a la consulta. Al establecer `MaxDegreeOfParallelism` en [SDK v2](sql-api-sdk-dotnet.md) en el número de particiones, tiene la mejor posibilidad de lograr la consulta de mayor rendimiento, siempre y cuando todas las demás condiciones del sistema sigan siendo las mismas. Si no conoce el número de particiones, puede establecer el grado de paralelismo en un número alto. El sistema elegirá el mínimo (número de particiones, entrada proporcionada por el usuario) como el grado de paralelismo.

Las consultas paralelas son las que mayor ventaja ofrecen si los datos se distribuyen de manera uniforme entre todas las particiones con respecto a la consulta. Si la colección con particiones tiene particiones para que todos o la mayoría de los datos devueltos por una consulta se concentren en algunas particiones (una partición es el peor caso), esas particiones producirán cuellos de botella en el rendimiento de la consulta.

***Optimizar MaxBufferedItemCount***
    
Las consultas en paralelo están diseñadas para capturar previamente los resultados mientras el cliente procesa el lote actual de resultados. Esta captura previa ayuda a mejorar la latencia general de una consulta. El parámetro `MaxBufferedItemCount` limita el número de resultados capturados previamente. Establezca `MaxBufferedItemCount` en el número esperado de resultados devueltos (o un número más alto) para permitir que la consulta reciba el máximo beneficio de la captura previa.

La captura previa funciona de la misma manera, independientemente del grado de paralelismo y hay un único búfer para los datos de todas las particiones.  

**Implementación del retroceso según intervalos RetryAfter**

Durante las pruebas de rendimiento, debe aumentar la carga hasta que se limite una pequeña tasa de solicitudes. Si se limitan las solicitudes, la aplicación del cliente debe volver al límite para el intervalo de reintentos especificado por el servidor. Respetar el retroceso garantiza una cantidad mínima de tiempo en espera entre reintentos. 

La compatibilidad con la directiva de reintentos se incluye en estos SDK:
- Versión 1.8.0 y versiones posteriores del [SDK de .NET para SQL](sql-api-sdk-dotnet.md) y el [SDK de Java para SQL](sql-api-sdk-java.md)
- Versión 1.9.0 y versiones posteriores de [SDK de Node.js para SQL](sql-api-sdk-node.md) y el [SDK de Python para SQL](sql-api-sdk-python.md)
- Todas las versiones compatibles de los SDK de [.NET Core](sql-api-sdk-dotnet-core.md) 

Para más información, consulte [RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter).
    
En la versión 1.19 y posteriores del SDK de .NET, hay un mecanismo para registrar información de diagnóstico adicional y problemas de latencia de solución de problemas, tal como se muestra en el ejemplo siguiente. Puede registrar la cadena de diagnóstico para las solicitudes que tienen una mayor latencia de lectura. La cadena de diagnóstico capturada le ayudará a comprender cuántas veces ha recibido 429 errores para una solicitud determinada.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Almacenamiento en caché de los identificadores URI de documentos para una latencia menor en las operaciones de lectura**

Siempre que sea posible, almacene en caché los identificadores URI para obtener el mejor rendimiento en las operaciones de lectura. Debe definir la lógica para almacenar en caché el identificador de recurso al crear un recurso. Las búsquedas basadas en identificadores de recursos son más rápidas que las búsquedas basadas en nombres, por lo que el almacenamiento en caché de estos valores mejora el rendimiento.

**Ajuste del tamaño de página en consultas y fuentes de lectura para aumentar el rendimiento**

Cuando se realiza una lectura masiva de documentos mediante la funcionalidad de fuente de lectura (por ejemplo, `ReadDocumentFeedAsync`) o cuando se emite una consulta SQL, los resultados se devuelven de forma segmentada si el conjunto de resultados es demasiado grande. De forma predeterminada, se devuelven resultados en fragmentos de 1 MB o de 100 artículos, el límite que se alcance primero.

Para reducir el número de recorridos de ida y vuelta de red necesarios para recuperar todos los resultados aplicables, puede aumentar el tamaño de página mediante [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para solicitar un máximo de 1 000 encabezados. Si necesita mostrar solo unos cuantos resultados, por ejemplo, si la interfaz de usuario o la API de aplicación solo devuelve 10 resultados a la vez, también puede reducir el tamaño de página a 10 para reducir el rendimiento consumido en las lecturas y consultas.

> [!NOTE] 
> La propiedad `maxItemCount` no se debe utilizar solo para la paginación. Su uso principal es mejorar el rendimiento de las consultas reduciendo el número máximo de elementos devueltos en una sola página.  

También puede establecer el tamaño de página mediante los SDK de Azure Cosmos DB disponibles. La propiedad [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount) de `FeedOptions` permite establecer el número máximo de elementos que se devolverán en la operación de enumeración. Cuando `maxItemCount` se establece en-1, el SDK busca automáticamente el valor óptimo, en función del tamaño del documento. Por ejemplo:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Cuando se ejecuta una consulta, se envían los datos resultantes en un paquete TCP. Si especifica un valor demasiado bajo para `maxItemCount`, el número de viajes necesarios para enviar los datos dentro del paquete TCP es alto, lo que afecta al rendimiento. Por lo tanto, si no está seguro del valor que se va a establecer para la propiedad `maxItemCount`, es mejor establecerlo en -1 y dejar que el SDK elija el valor predeterminado.

**Aumentar el número de subprocesos o tareas**

Consulte [Aumentar el número de subprocesos y tareas](#increase-threads) en la sección de redes de este artículo.

## <a name="indexing-policy"></a>Directiva de indexación
 
**Exclusión de rutas de acceso sin utilizar de la indexación para acelerar las escrituras**

La Directiva de indexación de Azure Cosmos DB también le permite especificar las rutas de acceso de documentos que se van a incluir o excluir de la indexación mediante rutas de acceso de indexación (IndexingPolicy.IncludedPaths and IndexingPolicy.ExcludedPaths). Las rutas de indexación pueden mejorar el rendimiento de escritura y reducir el almacenamiento de índices en los escenarios en los que los patrones de consulta se conocen con antelación. Esto se debe a que los costos de indización se relacionan directamente con el número de rutas de acceso únicas indizadas. Por ejemplo, este código muestra cómo excluir una sección completa de los documentos (un subárbol) de la indexación mediante el carácter comodín "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Para más información, consulte [Directivas de indexación de Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Rendimiento

**Medición y optimización del uso menor de unidades de solicitud por segundo**

Azure Cosmos DB ofrece un amplio conjunto de operaciones de base de datos. Estas operaciones incluyen consultas relacionales y jerárquicas con UDF, procedimientos almacenados y desencadenadores que funcionan en los documentos de una colección de base de datos. El costo asociado a cada una de estas operaciones varía en función de la CPU, la E/S y la memoria necesaria para completar la operación. En lugar de pensar y administrar los recursos de hardware, puede pensar en una unidad de solicitud (RU) como una única medida para los recursos necesarios para realizar varias operaciones de base de datos y dar servicio a una solicitud de aplicación.

El rendimiento se aprovisiona en función del número de [unidades de solicitud](request-units.md) establecidas para cada contenedor. El consumo de la unidad de solicitud se evalúa como una tarifa por segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para su contenedor se limitan hasta que la velocidad cae por debajo del nivel aprovisionado para el contenedor. Si su aplicación requiere un mayor nivel de rendimiento, puede aumentar el rendimiento mediante el aprovisionamiento de unidades de solicitud adicionales.

La complejidad de una consulta afecta a la cantidad de unidades de solicitud que se consumen para una operación. El número de predicados, la naturaleza de los predicados, el número de UDF y el tamaño del conjunto de código de origen influyen en el coste de las operaciones de consulta.

Para medir la sobrecarga de cualquier operación (crear, actualizar o eliminar), inspeccione el encabezado [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (o la propiedad equivalente a `RequestCharge` en `ResourceResponse\<T>` o `FeedResponse\<T>` en el SDK de .NET) para medir el número de unidades de solicitud consumidas por las operaciones:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

El cargo de solicitud devuelto en este encabezado es una fracción del rendimiento aprovisionado (es decir, 2 000 RUs por segundo). Por ejemplo, si la consulta anterior devuelve 1 000 documentos de 1 KB, el costo de la operación es 1 000. Por lo tanto, en un segundo, el servidor respeta solo dos solicitudes de este tipo antes de la limitación de velocidad de las solicitudes posteriores. Para más información, consulte [Unidades de solicitud](request-units.md) y la [Calculadora de unidades de solicitud ](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Administración de la limitación de velocidad y la tasa de solicitudes demasiado grande**

Cuando un cliente intenta superar la capacidad de proceso reservada para una cuenta, no hay degradación del rendimiento en el servidor y no se usa la capacidad de rendimiento más allá del nivel reservado. El servidor finalizará de forma preventiva la solicitud con RequestRateTooLarge (código de Estado HTTP 429). Devolverá un encabezado [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) que indica la cantidad de tiempo, en milisegundos, que el usuario debe esperar antes de volver a intentar la solicitud.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Los SDK capturan implícitamente esta respuesta, respetan el encabezado retry-after especificado por el servidor y reintentan la solicitud. A menos que varios clientes obtengan acceso a la cuenta al mismo tiempo, el siguiente reintento se realizará correctamente.

Si tiene más de un cliente que funciona acumulativamente de forma sistemática por encima de la tasa de solicitudes, es posible que el número de reintentos predeterminado establecido en 9 internamente por el cliente no sea suficiente. En este caso, el cliente inicia una DocumentClientException con el código de estado 429 en la aplicación. 

Puede cambiar el número de reintentos predeterminado estableciendo el `RetryOptions` en la instancia de `ConnectionPolicy`. De forma predeterminada, la excepción DocumentClientException con el código de estado 429 se devuelve tras un tiempo de espera acumulativo de 30 segundos si la solicitud sigue funcionando por encima de la tasa de solicitudes. Este error se devuelve aunque el número de reintentos actual sea inferior al número máximo de reintentos, tanto si el valor actual es el predeterminado de 9 como si es un valor definido por el usuario.

El comportamiento de reintento automatizado ayuda a mejorar la resistencia y la facilidad de uso de la mayoría de las aplicaciones. Pero es posible que no sea el mejor comportamiento al realizar pruebas comparativas de rendimiento, especialmente cuando se mide la latencia. La latencia observada del cliente aumentará si el experimento llega a la limitación del servidor y hace que el SDK del cliente realice reintentos de forma silenciosa. Para evitar aumentos de latencia durante los experimentos de rendimiento, mida el gasto devuelto por cada operación y asegúrese de que las solicitudes funcionan por debajo de la tasa de solicitudes observada. Para más información, consulte [Unidades de solicitud](request-units.md).

**Para un mayor rendimiento, diseñe para documentos más pequeños**

El cargo de la solicitud (es decir, el coste de procesamiento de solicitudes) de una operación determinada se correlaciona directamente con el tamaño del documento. Las operaciones en documentos grandes cuestan más que las operaciones en documentos pequeños.

## <a name="next-steps"></a>Pasos siguientes

Para obtener una aplicación de ejemplo que se usa para evaluar Azure Cosmos DB en escenarios de alto rendimiento en algunos equipos del cliente, consulte [Rendimiento y pruebas de escalado con Azure Cosmos DB](performance-testing.md).

Para más información sobre cómo diseñar la aplicación para escalarla y obtener un alto rendimiento, consulte [Partición y escalado en Azure Cosmos DB](partitioning-overview.md).