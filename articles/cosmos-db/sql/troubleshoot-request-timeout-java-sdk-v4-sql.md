---
title: Solución de los problemas de error HTTP 408 o de tiempo de espera de la solicitud en Azure Cosmos DB con el SDK para Java v4
description: Aprenda a diagnosticar y corregir excepciones de tiempo de espera de la solicitud del SDK de Java con el SDK para Java v4.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 718a0aa96579b3234bfa242408565246602762a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113707"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Diagnóstico y solución de problemas de tiempo de espera de la solicitud del SDK de Azure Cosmos DB para Java v4
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El error HTTP 408 se produce si el SDK no puede completar la solicitud antes de que se agote el límite de tiempo de espera.

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas
La lista siguiente contiene las causas y las soluciones conocidas para las excepciones de tiempo de espera de solicitud.

### <a name="existing-issues"></a>Problemas existentes
Si ve que las solicitudes se bloquean durante más tiempo o el tiempo de espera se agota con más frecuencia, actualice el SDK de Java v4 a la versión más reciente. NOTA: Se recomienda usar la versión 4.7.0 y versiones posteriores. Para más detalles, consulte las [notas de la versión del SDK de Java v4](sql-api-sdk-java-v4.md).

### <a name="high-cpu-utilization"></a>Uso elevado de CPU
El uso elevado de la CPU es el caso más común. Para lograr una latencia óptima, el uso de la CPU debe ser de aproximadamente el 40 por ciento. Use 10 segundos como intervalo para supervisar el uso máximo de la CPU (no el promedio). Los picos de CPU son más habituales con consultas entre particiones en las que se pueden realizar varias conexiones para una sola consulta.

#### <a name="solution"></a>Solución:
La aplicación cliente que usa el SDK se debe escalar vertical u horizontalmente.

### <a name="connection-throttling"></a>Limitación de la conexión
La limitación de la conexión puede deberse a un Límite de conexiones en una máquina host o al agotamiento de puertos SNAT (PAT) de Azure.

### <a name="connection-limit-on-a-host-machine"></a>Límite de conexiones en una máquina host
Algunos sistemas Linux, como Red Hat, tienen un límite máximo para el número total de archivos abiertos. En Linux, los sockets se implementan como archivos, por lo que este número también limita el número total de conexiones. Ejecute el siguiente comando:

```bash
ulimit -a
```

#### <a name="solution"></a>Solución:
El número máximo permitido de archivos abiertos, que se identifican como "nofile", debe ser al menos de 10 000. Para más información, consulte [Sugerencias de rendimiento](performance-tips-java-sdk-v4-sql.md) del SDK de Azure Cosmos DB para Java v4.

### <a name="socket-or-port-availability-might-be-low"></a>La disponibilidad de puertos o de sockets puede ser reducida
Cuando se ejecutan en Azure, los clientes que usan el SDK para Java pueden alcanzar el agotamiento de puertos SNAT (PAT) de Azure.

#### <a name="solution-1"></a>Solución 1:
Si utiliza máquinas virtuales de Azure, siga la [guía de agotamiento de los puertos SNAT](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>Solución 2:
Si utiliza Azure App Service, siga la [guía de solución de problemas de errores de conexión](../../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) y [use el diagnóstico de App Service](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Solución 3:
Si usa Azure Functions, compruebe que está siguiendo la [recomendación para Azure Functions](../../azure-functions/manage-connections.md#static-clients) del mantenimiento de los clientes Singleton o estáticos para todos los servicios implicados (incluido Azure Cosmos DB). Compruebe los [límites de servicio](../../azure-functions/functions-scale.md#service-limits) según el tipo y el tamaño del hospedaje de la aplicación de funciones.

#### <a name="solution-4"></a>Solución 4:
Si usa un Proxy HTTP, asegúrese de que pueda admitir el número de conexiones configuradas en el SDK de `GatewayConnectionConfig`. En caso contrario, se encontrará con problemas de conexión.

### <a name="create-multiple-client-instances"></a>Creación de varias instancias de cliente
La creación de varias instancias de cliente podría provocar problemas de tiempo de espera y la contención de la conexión.

#### <a name="solution-1"></a>Solución 1:
Siga los [consejos de rendimiento](performance-tips-java-sdk-v4-sql.md#sdk-usage) y use una sola instancia de CosmosClient en toda una aplicación.

#### <a name="solution-2"></a>Solución 2:
Si no es posible tener una sola instancia de CosmosClient en una aplicación, se recomienda usar las conexiones compartidas entre varios clientes de Cosmos a través de la API `connectionSharingAcrossClientsEnabled(true)` en CosmosClient. Cuando hay varias instancias del cliente de Cosmos en la misma máquina virtual Java que interactúan con varias cuentas de Cosmos, esta solución permite compartir las conexiones en modo directo, si es posible entre instancias del cliente de Cosmos. Tenga en cuenta que, al establecer esta opción, se usará la configuración de conexión (por ejemplo, configuración de tiempo de espera de socket, configuración de tiempo de espera de inactividad) de la primera instancia del cliente en todas las demás instancias.

### <a name="hot-partition-key"></a>Clave de partición activa
Azure Cosmos DB distribuye el rendimiento general aprovisionado de forma uniforme entre las particiones físicas. Cuando hay una partición activa, una o varias claves de partición lógica en una partición física están consumiendo todas las unidades de solicitud de la partición física por segundo (RU/s). Al mismo tiempo, las RU/s de otras particiones físicas no se usan. Como síntoma, el total de RU/s consumido será inferior al total de RU/s aprovisionadas en la base de datos o el contenedor, pero seguirá viendo la limitación (429s) en las solicitudes de la clave de la partición lógica activa. Use la [métrica de consumo normalizado de RU](../monitor-normalized-request-units.md) para ver si la carga de trabajo está detectando una partición activa. 

#### <a name="solution"></a>Solución:
Elija una buena clave de partición que distribuya uniformemente el volumen y el almacenamiento de solicitudes. Obtenga más información acerca de [cómo cambiar la clave de partición](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Alto grado de simultaneidad
La aplicación tiene un alto nivel de simultaneidad, lo que puede provocar contención en el canal.

#### <a name="solution"></a>Solución:
La aplicación cliente que usa el SDK se debe escalar vertical u horizontalmente.

### <a name="large-requests-or-responses"></a>Solicitudes o respuestas de gran tamaño
Un gran número de solicitudes o respuestas puede provocar un bloqueo de encabezado de línea en el canal y agravar la contención, incluso con un nivel relativamente bajo de simultaneidad.

#### <a name="solution"></a>Solución:
La aplicación cliente que usa el SDK se debe escalar vertical u horizontalmente.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>La tasa de errores está dentro del Acuerdo de Nivel de Servicio de Azure Cosmos DB
La aplicación debe ser capaz de controlar los errores transitorios y volver a intentarlo cuando sea necesario. Las excepciones 408 no se reintentan porque, en las rutas de acceso de creación, no es posible saber si el servicio creó el elemento. Si se vuelve a enviar el mismo elemento para crearlo, se producirá una excepción de conflicto. La lógica de negocios de las aplicaciones de usuario podría personalizarse para administrar los conflictos, lo que produciría errores por la ambigüedad de un elemento existente frente al conflicto de un reintento de creación.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>La tasa de errores infringe el Acuerdo de Nivel de Servicio de Azure Cosmos DB
Póngase en contacto con el [soporte técnico de Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Pasos siguientes
* [Diagnóstico y solución de problemas](troubleshoot-java-sdk-v4-sql.md) al utilizar el SDK de Azure Cosmos DB para Java v4.
* Obtenga información sobre las directrices de rendimiento para [Java v4](performance-tips-java-sdk-v4-sql.md).
