---
title: Encabezados de respuesta de Gremlin en Azure Cosmos DB
description: Documentación de referencia de los metadatos de respuesta del servidor que permite solucionar otros problemas
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: c5af5675142ced8ca881abba83534f8ede0ece60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780322"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Encabezados de respuesta del servidor Gremlin en Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

En este artículo, se describen los encabezados que el servidor Gremlin de Cosmos DB devuelve al autor de la llamada al ejecutar una solicitud. Estos encabezados son útiles para solucionar problemas de rendimiento de las solicitudes, crear aplicaciones que se integren de forma nativa con el servicio Cosmos DB y simplificar el servicio de soporte al cliente.

Tenga en cuenta que, si depende de estos encabezados, estará limitando la portabilidad de la aplicación a otras implementaciones de Gremlin. A cambio, disfrutará de una integración más estrecha de Gremlin en Cosmos DB. Estos encabezados no son un estándar de TinkerPop.

## <a name="headers"></a>encabezados

| Encabezado | Tipo | Valor de ejemplo | Cuándo se incluye | Explicación |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Aciertos y errores | Cantidad de rendimiento de las bases de datos o colecciones consumido en [unidades de solicitud (RU/s o RU)](../request-units.md) en un mensaje de respuesta parcial. Este encabezado está presente en las continuaciones de las solicitudes que tienen varios fragmentos. Refleja la carga de un determinado fragmento de la respuesta. En aquellas solicitudes que se componen de un solo fragmento de respuesta, este encabezado coincide con el costo total del recorrido. Sin embargo, en la mayoría de los recorridos complejos, este valor representa un costo parcial. |
| **x-ms-total-request-charge** | double | 423,987 | Aciertos y errores | Cantidad de rendimiento de las bases de datos o colecciones consumido en [unidades de solicitud (RU/s o RU)](../request-units.md) en una solicitud completa. Este encabezado está presente en las continuaciones de las solicitudes que tienen varios fragmentos. Indica el cargo acumulado desde el inicio de la solicitud. El valor de este encabezado en el último fragmento indica la carga completa de la solicitud. |
| **x-ms-server-time-ms** | double | 13,75 | Aciertos y errores | Este encabezado se incluye para tratar de solucionar problemas de latencia. Indica la cantidad de tiempo, en milisegundos, que el servidor Gremlin de Cosmos DB tardó en ejecutarse y en producir un mensaje de respuesta parcial. Si se utiliza el valor de este encabezado y se compara con la latencia general de las solicitudes, las aplicaciones pueden calcular la sobrecarga de la latencia de red. |
| **x-ms-total-server-time-ms** | double | 130,512 | Aciertos y errores | Tiempo total, en milisegundos, que el servidor Gremlin de Cosmos DB tardó en ejecutar todo el recorrido. Este encabezado se incluye en todas las respuestas parciales. Representa el tiempo de ejecución acumulado desde el inicio de la solicitud. La última respuesta indica el tiempo total de ejecución. Este encabezado resulta útil para diferenciar entre el cliente y el servidor como origen de latencia. Puede comparar el tiempo de ejecución del recorrido del cliente con el valor de este encabezado. |
| **x-ms-status-code** | long | 200 | Aciertos y errores | El encabezado indica el motivo interno por el que ha finalizado o terminado una solicitud. Es recomendable que la aplicación examine el valor de este encabezado y tome medidas correctivas. |
| **x-ms-substatus-code** | long | 1003 | Solo error | Cosmos DB es una base de datos de varios modelos que se crea sobre el nivel de almacenamiento unificado. Si se produce un error en las capas inferiores de la pila de alta disponibilidad, este encabezado contiene información adicional sobre el motivo. Es conveniente que la aplicación guarde este encabezado para poder utilizarlo al contactar con el servicio de soporte al cliente de Cosmos DB. El valor de este encabezado le será útil al ingeniero de Cosmos DB para resolver problemas con mayor rapidez. |
| **x-ms-retry-after-ms** | cadena (TimeSpan) | "00:00:03.9500000" | Solo error | Este encabezado es una representación de cadena de un tipo [TimeSpan](/dotnet/api/system.timespan) de .NET. Este valor solo se incluirá en las solicitudes que registran errores por agotar el rendimiento aprovisionado. La aplicación debe ejecutar de nuevo el recorrido una vez transcurrido el período de tiempo indicado. |
| **x-ms-activity-id** | cadena (GUID) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Aciertos y errores | El encabezado contiene el identificador único del lado servidor de una solicitud. A cada solicitud se le asigna un identificador único para hacer un seguimiento. Las aplicaciones deben registrar los identificadores de actividad devueltos por el servidor en aquellas solicitudes en las que es posible que los clientes quieran ponerse en contacto con el servicio de soporte al cliente. El personal de soporte de Cosmos DB puede buscar solicitudes específicas utilizando estos identificadores en los recursos de telemetría del servicio Cosmos DB. |

## <a name="status-codes"></a>Códigos de estado

A continuación se indican los códigos más comunes devueltos por el servidor para el atributo de estado `x-ms-status-code`.

| Status | Explicación |
| --- | --- |
| **401** | El mensaje de error `"Unauthorized: Invalid credentials provided"` aparece cuando la contraseña de autenticación no coincide con la clave de cuenta de Cosmos DB. Vaya a la cuenta de Gremlin de Cosmos DB en Azure Portal y confirme que la clave es correcta.|
| **404** | Hay operaciones simultáneas que intentan eliminar y actualizar el mismo vértice o borde a la vez. El mensaje de error `"Owner resource does not exist"` indica que la base de datos o la colección especificadas son incorrectas en los parámetros de conexión en formato `/dbs/<database name>/colls/<collection or graph name>`.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Esto suele ocurrir cuando ya existe un vértice o un borde con un identificador en el grafo.| 
| **412** | El código de estado va acompañado de un mensaje de error `"PreconditionFailedException": One of the specified pre-condition is not met`. Esto error indica una infracción del control de simultaneidad optimista entre la lectura de un borde o vértice y su escritura en el almacén después de realizar una modificación. Por lo general, esto suele producirse cuando se modifica una propiedad; por ejemplo, `g.V('identifier').property('name','value')`. El motor de Gremlin leerá el vértice, lo modificará y lo escribirá de nuevo. Si hay otro recorrido ejecutándose en paralelo y se intenta escribir el mismo vértice o borde, uno de ellos registrará este error. La aplicación deberá ejecutar de nuevo el recorrido en el servidor.| 
| **429** | La solicitud se limitó y se debe reintentar después del valor especificado en **x-ms-retry-after-ms**.| 
| **500** | Un mensaje de error que contiene `"NotFoundException: Entity with the specified id does not exist in the system."` indica que se ha vuelto a crear una base de datos o una colección con el mismo nombre. Este error desaparecerá en 5 minutos a medida que el cambio se propague e invalide las memorias caché en los diferentes componentes de Cosmos DB. Para evitar este problema, use nombres de base de datos y de colección únicos cada vez.| 
| **1000** | Este código de estado se devuelve cuando el servidor analiza correctamente un mensaje pero no puede ejecutarlo. Normalmente, suelen tratarse de problemas con las consultas.| 
| **1001** | Este código se devuelve cuando el servidor completa la ejecución transversal pero no puede serializar la respuesta de vuelta al cliente. Este error puede producirse cuando el recorrido genera un resultado complejo que es demasiado grande o no se ajusta a la especificación del protocolo TinkerPop. Si se encuentra con este error, la aplicación debería simplificar el recorrido. | 
| **1003** | El valor `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` se devuelve cuando el recorrido supera el límite de memoria permitido. El límite de memoria es de **2 GB** por recorrido.| 
| **1004** | Este código de estado indica que una solicitud de grafo no tiene el formato correcto. La solicitud puede tener un formato incorrecto porque se han producido errores en la deserialización, se está deserializando un tipo que no tiene valor o la operación de Gremlin solicitada no es compatible. La aplicación no debe volver a intentar la solicitud, ya que no se realizará correctamente. | 
| **1007** | Normalmente, este código de estado se devuelve junto con un mensaje de error `"Could not process request. Underlying connection has been closed."`. Esta situación puede producirse si el controlador del cliente intenta usar una conexión que el servidor está cerrando. La aplicación debería volver a intentar el recorrido con una conexión diferente.
| **1008** | El servidor Gremlin de Cosmos DB puede terminar las conexiones para reequilibrar el tráfico del clúster. Los controladores del cliente deben ocuparse de esta situación y usar solo conexiones dinámicas para enviar solicitudes al servidor. En ocasiones, es posible que los controladores del cliente no detecten que la conexión está cerrada. Cuando la aplicación se encuentra con un error, `"Connection is too busy. Please retry after sometime or open more connections."` debe reintentar el recorrido con una conexión diferente.
| **1009** | La operación no se completó en el tiempo asignado, y el servidor la canceló. Optimice los recorridos para que se ejecuten rápidamente filtrando vértices o bordes en cada salto del recorrido para restringir el ámbito de búsqueda. El tiempo de espera predeterminado de las solicitudes es de **60 segundos**. |

## <a name="samples"></a>Ejemplos

Aplicación cliente de ejemplo basada en Gremlin.Net que lee un atributo de estado:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Ejemplo en el que se muestra cómo se lee un atributo de estado desde el cliente de Java de Gremlin:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Pasos siguientes
* [Códigos de Estado HTTP en Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Encabezados de respuesta comunes de REST en Azure Cosmos DB](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Requisitos del proveedor del controladores de grafos de TinkerPop]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
