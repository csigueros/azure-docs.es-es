---
title: Solución de problemas de solicitudes lentas de Azure Cosmos DB con el SDK de .NET
description: Aprenda a diagnosticar y corregir solicitudes lentas al usar el SDK de .NET de Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 06/15/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: abee066553c4ee338000efd39ecd3b0bfd11d171
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745450"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-slow-requests"></a>Diagnóstico y solución de problemas de solicitudes lentas del SDK de .NET de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Las solicitudes lentas de Azure Cosmos DB pueden producirse por varias razones, como la limitación de solicitudes o la forma en que se diseña la aplicación. En este artículo se explican las distintas causas principales de esta incidencia. 

## <a name="request-rate-too-large-429-throttles"></a>Tasa de solicitudes demasiado grande (429 limitaciones)

La limitación de solicitudes es la razón más común para las solicitudes lentas. Azure Cosmos DB limitará las solicitudes si superan las RU asignadas para la base de datos o el contenedor. El SDK tiene lógica integrada para reintentar estas solicitudes. En el artículo sobre solución de problemas de [tasa de solicitudes demasiado grande](troubleshoot-request-rate-too-large.md#how-to-investigate) se explica cómo comprobar si se están limitando las solicitudes y cómo escalar la cuenta para evitar estas incidencias en un futuro.

## <a name="application-design"></a>Diseño de aplicación

Si la aplicación no sigue los procedimientos recomendados del SDK, puede dar lugar a diferentes incidencias que provocarán solicitudes lentas o con errores. Siga los [procedimientos recomendados del SDK de .NET](performance-tips-dotnet-sdk-v3-sql.md) para obtener el mejor rendimiento.

Tenga en cuenta lo siguiente al desarrollar la aplicación:
* La aplicación debe estar en la misma región que la cuenta de Azure Cosmos DB.
* Instancia singleton de la instancia del SDK. El SDK tiene varias memorias caché que deben inicializarse, lo que puede ralentizar las primeras solicitudes. 
* Use el modo de conectividad Directo + TCP.
* Evite el uso elevado de CPU. Asegúrese de ver el número máximo de CPU y no el promedio, que es el valor predeterminado para la mayoría de los sistemas de registro. Cualquier valor superior al 40 % aproximadamente puede aumentar la latencia.


## <a name="capture-the-diagnostics"></a><a name="capture-diagnostics"></a>Captura de los diagnósticos

Todas las respuestas del SDK, incluidas `CosmosException`, tienen una propiedad Diagnostics. Esta propiedad registra toda la información relacionada con la solicitud única, incluidos los reintentos o errores transitorios. 

Los diagnósticos se devuelven como una cadena. La cadena cambia con cada versión a medida que se mejora para solucionar mejor los distintos escenarios. Con cada versión del SDK, la cadena tendrá cambios importantes en el formato. No analice la cadena para evitar cambios importantes. En el ejemplo de código siguiente se muestra cómo leer los registros de diagnóstico mediante el SDK de .NET:

```c#
try
{
    ItemResponse<Book> response = await this.Container.CreateItemAsync<Book>(item: testItem);
    if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan)
    {
        // Log the diagnostics and add any additional info necessary to correlate to other logs 
        Console.Write(response.Diagnostics.ToString());
    }
}catch(CosmosException cosmosException){
    // Log the full exception including the stack trace 
    Console.Write(cosmosException.ToString());
    // The Diagnostics can be logged separately if required.
    Console.Write(cosmosException.Diagnostics.ToString());
}

ResponseMessage response = await this.Container.CreateItemStreamAsync(partitionKey, stream);
if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan || IsFailureStatusCode(response.StatusCode))
{
    // Log the diagnostics and add any additional info necessary to correlate to other logs 
    Console.Write(response.Diagnostics.ToString());
}
```


## <a name="diagnostics-in-version-319-and-higher"></a>Diagnósticos en la versión 3.19 y posteriores
La estructura JSON tiene cambios importantes con cada versión del SDK. Esto hace que no sea seguro analizarlo. JSON representa una estructura de árbol de la solicitud que pasa por el SDK. Esto abarca algunos aspectos clave que hay que tener en cuenta:

### <a name="cpu-history"></a><a name="cpu-history"></a>Historial de CPU
El uso elevado de la CPU es la causa más común de las solicitudes lentas. Para lograr una latencia óptima, el uso de la CPU debe ser de aproximadamente el 40 por ciento. Use 10 segundos como intervalo para supervisar el uso máximo de la CPU (no el promedio). Los picos de CPU son más habituales con consultas entre particiones en las que se pueden realizar varias conexiones para una sola consulta.

Si el error contiene información de `TransportException`, también podría contener la información de `CPU History`:

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* Si el uso de la CPU supera el 70 %, es probable que el problema de tiempo de estera se deba al agotamiento de la CPU. En este caso, la solución es investigar el origen del uso intensivo de la CPU y reducirlo, o bien escalar el equipo a un tamaño de recurso mayor.
* Si las medidas de la CPU no se producen cada 10 segundos, los intervalos o tiempos de medición indican tiempos mayores entre las medidas. En tal caso, la causa es la escasez de subprocesos. La solución es investigar el origen u orígenes del colapso de subprocesos (subprocesos potencialmente bloqueados) o escalar los equipos a un tamaño de recurso mayor.

#### <a name="solution"></a>Solución:
La aplicación cliente que usa el SDK se debe escalar vertical u horizontalmente.


### <a name="httpresponsestats"></a><a name="httpResponseStats"></a>HttpResponseStats
HttpResponseStats son solicitudes que van a la [puerta de enlace](sql-sdk-connection-modes.md). Incluso en el modo Directo, el SDK obtiene toda la información de metadatos de la puerta de enlace.

Si la solicitud es lenta, compruebe primero que todas las sugerencias anteriores no producen resultados.

Si sigue siendo lento, los distintos patrones apuntan a incidencias diferentes:

Resultado de un solo almacén para una única solicitud

| Número de solicitudes | Escenario | Descripción | 
|----------|-------------|-------------|
| Una a todas | Tiempo de espera de solicitud o HttpRequestExceptions | Apunta al [agotamiento del puerto SNAT](troubleshoot-dot-net-sdk.md#snat) o a la falta de recursos en la máquina para procesar la solicitud a tiempo. |
| Porcentaje único o pequeño (no se infringe el Acuerdo de Nivel de Servicio) | All | Un porcentaje único o pequeño de solicitudes lentas puede deberse a varias incidencias transitorias diferentes y debe esperarse. | 
| All | All | Apunta a una incidencia con la infraestructura o las redes. |
| Acuerdo de Nivel de Servicio infringido | No se ha eliminado ningún cambio en la aplicación y el Acuerdo de Nivel de Servicio | Apunta a una incidencia con el servicio Azure Cosmos DB. |

```json
"HttpResponseStats": [
    {
        "StartTimeUTC": "2021-06-15T13:53:09.7961124Z",
        "EndTimeUTC": "2021-06-15T13:53:09.7961127Z",
        "RequestUri": "https://127.0.0.1:8081/dbs/347a8e44-a550-493e-88ee-29a19c070ecc/colls/4f72e752-fa91-455a-82c1-bf253a5a3c4e",
        "ResourceType": "Collection",
        "HttpMethod": "GET",
        "ActivityId": "e16e98ec-f2e3-430c-b9e9-7d99e58a4f72",
        "StatusCode": "OK"
    }
]
```

### <a name="storeresult"></a><a name="storeResult"></a>StoreResult
StoreResult representa una única solicitud a Azure Cosmos DB mediante el modo Directo con el protocolo TCP.

Si sigue siendo lento, los distintos patrones apuntan a incidencias diferentes:

Resultado de un solo almacén para una única solicitud

| Número de solicitudes | Escenario | Descripción | 
|----------|-------------|-------------|
| Una a todas | StoreResult contiene TransportException | Apunta al [agotamiento del puerto SNAT](troubleshoot-dot-net-sdk.md#snat) o a la falta de recursos en la máquina para procesar la solicitud a tiempo. |
| Porcentaje único o pequeño (no se infringe el Acuerdo de Nivel de Servicio) | All | Un porcentaje único o pequeño de solicitudes lentas puede deberse a varias incidencias transitorias diferentes y debe esperarse. | 
| All | All | Una incidencia con la infraestructura o las redes. |
| Acuerdo de Nivel de Servicio infringido | Las solicitudes contienen varios códigos de error como 410 e IsValid es true | Apunta a una incidencia con el servicio Azure Cosmos DB |
| Acuerdo de Nivel de Servicio infringido | Las solicitudes contienen varios códigos de error como 410 e IsValid es false | Apunta a una incidencia con la máquina |
| Acuerdo de Nivel de Servicio infringido | StorePhysicalAddress es el mismo sin código de estado de error | Es probable que haya una incidencia con el servicio Cosmos DB |
| Acuerdo de Nivel de Servicio infringido | StorePhysicalAddress tiene el mismo identificador de partición, pero identificadores de réplica diferentes sin código de estado de error | Es probable que haya una incidencia con el servicio Cosmos DB |
| Acuerdo de Nivel de Servicio infringido | StorePhysicalAddress son aleatorios sin código de estado de error | Apunta a una incidencia con la máquina |

Varios StoreResults para una única solicitud:

* La coherencia fuerte y de obsolescencia limitada siempre tendrá al menos dos resultados de almacén
* Compruebe el código de estado de cada StoreResult. El SDK vuelve a intentarlo automáticamente en varios errores [transitorios diferentes](troubleshoot-dot-net-sdk-request-timeout.md). El SDK se está mejorando constantemente para abarcar más escenarios. 

### <a name="rntbdrequeststats"></a><a name="rntbdRequestStats"></a>RntbdRequestStats 
Muestra la hora de las distintas fases de envío y recepción de una solicitud en la capa de transporte.

* ChannelAcquisitionStarted: la hora para obtener o crear una nueva conexión. Se pueden crear nuevas conexiones para numerosas regiones diferentes. Por ejemplo, una conexión se cerró inesperadamente o se enviaron demasiadas solicitudes a través de las conexiones existentes, por lo que se crea una nueva conexión. 
* El tiempo canalizado es un punto grande para posiblemente una solicitud grande.
* El tiempo de tránsito es grande, lo que da lugar a una incidencia de red. Compare este número con `BELatencyInMs`. Si BELatencyInMs es pequeño, el tiempo se dedicó a la red y no al servicio Azure Cosmos DB.
* El tiempo recibido es grande, lo que apunta a una incidencia de escasez de subprocesos. Este es el tiempo entre que se tiene la respuesta y se devuelve el resultado.

```json
"StoreResult": {
    "ActivityId": "a3d325c1-f4e9-405b-820c-bab4d329ee4c",
    "StatusCode": "Created",
    "SubStatusCode": "Unknown",
    "LSN": 1766,
    "PartitionKeyRangeId": "0",
    "GlobalCommittedLSN": -1,
    "ItemLSN": -1,
    "UsingLocalLSN": false,
    "QuorumAckedLSN": 1765,
    "SessionToken": "-1#1766",
    "CurrentWriteQuorum": 1,
    "CurrentReplicaSetSize": 1,
    "NumberOfReadRegions": 0,
    "IsClientCpuOverloaded": false,
    "IsValid": true,
    "StorePhysicalAddress": "rntbd://127.0.0.1:10253/apps/DocDbApp/services/DocDbServer92/partitions/a4cb49a8-38c8-11e6-8106-8cdcd42c33be/replicas/1p/",
    "RequestCharge": 11.05,
    "BELatencyInMs": "7.954",
    "RntbdRequestStats": [
        {
            "EventName": "Created",
            "StartTime": "2021-06-15T13:53:10.1302477Z",
            "DurationInMicroSec": "6383"
        },
        {
            "EventName": "ChannelAcquisitionStarted",
            "StartTime": "2021-06-15T13:53:10.1366314Z",
            "DurationInMicroSec": "96511"
        },
        {
            "EventName": "Pipelined",
            "StartTime": "2021-06-15T13:53:10.2331431Z",
            "DurationInMicroSec": "50834"
        },
        {
            "EventName": "Transit Time",
            "StartTime": "2021-06-15T13:53:10.2839774Z",
            "DurationInMicroSec": "17677"
        },
        {
            "EventName": "Received",
            "StartTime": "2021-06-15T13:53:10.3016546Z",
            "DurationInMicroSec": "7079"
        },
        {
            "EventName": "Completed",
            "StartTime": "2021-06-15T13:53:10.3087338Z",
            "DurationInMicroSec": "0"
        }
    ],
    "TransportException": null
}
```

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>La tasa de errores infringe el Acuerdo de Nivel de Servicio de Azure Cosmos DB
Póngase en contacto con el [soporte técnico de Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Pasos siguientes
* [Diagnóstico y solución de problemas](troubleshoot-dot-net-sdk.md) al utilizar el SDK de Azure Cosmos DB para .NET.
* Más información sobre las directrices de rendimiento de [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) y [.NET v2](performance-tips.md).
