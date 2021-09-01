---
title: Diagnóstico y solución de problemas de disponibilidad de los SDK de Azure Cosmos en entornos de varias regiones
description: Obtenga información sobre el comportamiento de la disponibilidad del SDK de Azure Cosmos cuando se trabaja en entornos de varias regiones.
author: ealsur
ms.service: cosmos-db
ms.date: 02/18/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 55912ee592ea04ec8771cf833dd92874d6d2c505
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114375"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>Diagnóstico y solución de problemas de disponibilidad de los SDK de Azure Cosmos en entornos de varias regiones
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo se describe el comportamiento de la versión más reciente de los SDK de Azure Cosmos cuando se tiene un problema de conectividad con una región determinada o cuando se produce una conmutación por error de una región.

Todos los SDK de Azure Cosmos ofrecen la opción de personalizar las preferencias regionales. Las siguientes propiedades se utilizan en diferentes SDK:

* La propiedad [ConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) en el SDK de .NET V2.
* Las propiedades [CosmosClientOptions.ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) o [CosmosClientOptions.ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) en el SDK de .NET V3.
* El método [CosmosClientBuilder.preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) en el SDK de Java V4.
* El [parámetro CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) en el SDK de Python.
* El parámetro [CosmosClientOptions.ConnectionPolicy.preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) en el SDK de JS.

Cuando establezca las preferencias regionales, el cliente se conectará a una región como se menciona en la tabla siguiente:

|Tipo de cuenta |Lecturas |Escrituras |
|------------------------|--|--|
| Una región de escritura | Región preferida | Región primaria  |
| Varias regiones de escritura | Región preferida | Región preferida  |

Si **no establece una región preferida**, el cliente SDK utilizará la región primaria como el valor predeterminado:

|Tipo de cuenta |Lecturas |Escrituras |
|------------------------|--|--|
| Una región de escritura | Región primaria | Región primaria |
| Varias regiones de escritura | Región primaria  | Región primaria  |

> [!NOTE]
> La región primaria hace referencia a la primera región de la [lista de regiones de la cuenta de Azure Cosmos](../distribute-data-globally.md).
> Si los valores especificados como preferencia regional no coinciden con ninguna región de Azure existente, se omitirán. Si coinciden con una región existente pero la cuenta no se replica en ella, el cliente se conectará a la siguiente región preferida que coincida o a la región primaria.

> [!WARNING]
> La lógica de conmutación por error y de disponibilidad que se describe en este documento puede deshabilitarse en la configuración del cliente, lo que no se recomienda a menos que la aplicación del usuario controle los errores de disponibilidad por sí misma. Para lograr esto:
>
> * Establezca la propiedad [ConnectionPolicy.EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) del SDK de .NET V2 en false.
> * Establezca la propiedad [CosmosClientOptions.LimitToEndpoint](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.limittoendpoint) del SDK de .NET V3 en true.
> * Establezca el método [CosmosClientBuilder.endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) del SDK de Java V4 en false.
> * Establezca el parámetro [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) del SDK de Python en false.
> * Establezca el parámetro [CosmosClientOptions.ConnectionPolicy.enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) del SDK de JS en false.

En circunstancias normales, el cliente SDK se conectará a la región preferida (si se ha establecido una preferencia regional) o a la región primaria (si no se ha establecido una preferencia); y las operaciones se limitarán a esa región, a menos que se produzca alguno de estos escenarios.

En estos casos, el cliente que usa el SDK de Azure Cosmos expone los registros e incluye la información de reintentos como parte de la **información de diagnóstico de operaciones**:

* La propiedad *RequestDiagnosticsString* en las respuestas del SDK de .NET v2.
* La propiedad *Diagnostics* en las respuestas y excepciones en el SDK de .NET v3.
* El método *getDiagnostics()* en las respuestas y excepciones en el SDK de Java v4.

Al determinar la siguiente región en orden de preferencia, el cliente del SDK usará la lista de regiones de la cuenta, dando prioridad a las regiones preferidas (si las hay).

Para obtener información detallada sobre las garantías del Acuerdo de Nivel de Servicio durante estos eventos, vea los [Acuerdos de Nivel de Servicio para la disponibilidad](../high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region">Eliminación de una región de la cuenta</a>

Al quitar una región de una cuenta de Azure Cosmos, cualquier cliente SDK que use activamente la cuenta detectará la eliminación de la región a través de un código de respuesta de back-end. Después, el cliente marca el punto de conexión regional como no disponible. El cliente reintentará la operación actual y todas las operaciones futuras se enrutarán permanentemente a la siguiente región en orden de preferencia. En caso de que la lista de preferencias tenga solo una entrada (o estaba vacía) pero la cuenta tenga otras regiones disponibles, se enrutará a la siguiente región de la lista de cuentas.

## <a name="adding-a-region-to-an-account"></a>Adición de una región a una cuenta

Cada 5 minutos, el cliente SDK de Azure Cosmos lee la configuración de la cuenta y actualiza las regiones que conoce.

Cuando se quita una región y más adelante se vuelve a agregar a la cuenta, si la región agregada tiene un orden de preferencia regional más alto en la configuración del SDK en comparación con la región conectada actualmente, el SDK volverá a usar esta región de forma permanente. Una vez detectada la región agregada, todas las solicitudes futuras se dirigirán a ella.

Si configura el cliente para que se conecte preferiblemente a una región que no tiene la cuenta de Azure Cosmos, se omitirá la región preferida. Si agrega esa región más adelante, el cliente la detectará y cambiará de forma permanente a esa región.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Conmutación por error de la región de escritura en una cuenta con una sola región de escritura

Si inicia una conmutación por error de la región de escritura actual, la siguiente solicitud de escritura producirá un error con una respuesta de back-end conocida. Cuando se detecta esta respuesta, el cliente consultará la cuenta para obtener información sobre la nueva región de escritura y volverá a intentar la operación actual y enrutará permanentemente todas las operaciones de escritura futuras a la nueva región.

## <a name="regional-outage"></a>Interrupción regional

Si la cuenta es de una sola región de escritura y la interrupción regional se produce durante una operación de escritura, el comportamiento es similar a una [conmutación por error manual](#manual-failover-single-region). En el caso de las solicitudes de lectura o de cuentas de varias regiones de escritura, el comportamiento es similar a [quitar una región](#remove-region).

## <a name="session-consistency-guarantees"></a>Garantías de coherencia de la sesión

Al utilizar la [coherencia de la sesión](../consistency-levels.md#guarantees-associated-with-consistency-levels), el cliente debe garantizar que puede leer sus propias escrituras. En las cuentas de una sola región de escritura en las que la preferencia de región de lectura es diferente de la región de escritura, podría haber casos en los que el usuario emita una escritura y al realizar una lectura desde una región local, esta no haya recibido todavía la replicación de datos (restricción de velocidad de la luz). En tales casos, el SDK detecta el error específico en la operación de lectura y reintenta la lectura en la región primaria para garantizar la coherencia de la sesión.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Problemas de conectividad transitorios en el protocolo TCP

En los escenarios en los que el cliente SDK de Azure Cosmos está configurado para usar el protocolo TCP, pueden darse situaciones para una solicitud determinada en las que las condiciones de red afecten temporalmente a la comunicación con un punto de conexión determinado. Estas condiciones de red temporales se pueden exponer como tiempos de espera de TCP y errores de servicio no disponible (HTTP 503). El cliente reintentará la solicitud localmente en el mismo punto de conexión durante algunos segundos antes de exponer el error.

Si el usuario ha configurado una lista de regiones preferidas con más de una región y la cuenta de Azure Cosmos es de varias regiones de escritura o de una sola región de escritura y la operación es una solicitud de lectura, el cliente detectará el error local y volverá a intentar esa única operación en la siguiente región de la lista de preferencias.

## <a name="next-steps"></a>Pasos siguientes

* Revise los [Acuerdos de Nivel de Servicio para la disponibilidad](../high-availability.md#slas-for-availability).
* Utilización del [SDK de .NET](sql-api-sdk-dotnet-standard.md) más reciente
* Utilización del [SDK de Java](sql-api-sdk-java-v4.md) más reciente
* Utilización del [SDK de Python](sql-api-sdk-python.md) más reciente
* Utilización del [SDK de Node](sql-api-sdk-node.md) más reciente
