---
title: Solución de los problemas de error HTTP 408 o de tiempo de espera de la solicitud en Azure Cosmos DB con el SDK de .NET
description: Aprenda a diagnosticar y corregir las excepciones de tiempo de espera de solicitud del SDK de .NET.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/05/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 3d731685825b09cdee52d9aae525d169d1d85ace
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114380"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnóstico y solución de problemas de tiempo de espera de la solicitud del SDK de .NET en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El error HTTP 408 se produce si el SDK no puede completar la solicitud antes de que se agote el límite de tiempo de espera.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Personalización del tiempo de espera en el SDK de.NET para Azure Cosmos DB

El SDK tiene dos alternativas distintas a los tiempos de espera de control, cada una con un ámbito diferente.

### <a name="requesttimeout"></a>RequestTimeout

La configuración `CosmosClientOptions.RequestTimeout` (o `ConnectionPolicy.RequestTimeout` para SDK v2) le permite establecer un tiempo de espera que afecte a cada solicitud de red individual. Una operación iniciada por un usuario puede abarcar varias solicitudes de red (por ejemplo, podría haber límite de ancho de banda). Esta configuración se aplicaría para cada solicitud de red al reintentar. Este no es un tiempo de espera de la solicitud de operación de un extremo a otro.

### <a name="cancellationtoken"></a>CancellationToken

Todas las operaciones asincrónicas del SDK tienen un parámetro CancellationToken opcional. Este parámetro [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) se utiliza en toda la operación y en todas las solicitudes de red. Entre las solicitudes de red, se puede comprobar el parámetro de cancelación. Una operación se cancela si el token relacionado ha expirado. El token de cancelación se debe usar para definir un tiempo de espera esperado aproximado en el ámbito de la operación.

> [!NOTE]
> El parámetro `CancellationToken` es un mecanismo que permite a la biblioteca comprobar la cancelación [cuando no cause un estado no válido](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Es posible que la operación no se cancele exactamente cuando el tiempo definido en la cancelación esté activo. En su lugar, una vez que se agote el tiempo, se cancelará cuando sea seguro.

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas
La lista siguiente contiene las causas y las soluciones conocidas para las excepciones de tiempo de espera de solicitud.

### <a name="high-cpu-utilization"></a>Uso elevado de CPU
El uso elevado de la CPU es el caso más común. Para lograr una latencia óptima, el uso de la CPU debe ser de aproximadamente el 40 por ciento. Use 10 segundos como intervalo para supervisar el uso máximo de la CPU (no el promedio). Los picos de CPU son más habituales con consultas entre particiones en las que se pueden realizar varias conexiones para una sola consulta.

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

* Si las medidas de la CPU superan el 70 %, es probable que el problema de tiempo de estera se deba al agotamiento de la CPU. En este caso, la solución es investigar el origen del uso intensivo de la CPU y reducirlo, o bien escalar el equipo a un tamaño de recurso mayor.
* Si las medidas de la CPU no se producen cada 10 segundos (por ejemplo, los intervalos o tiempos de medición indican tiempos mayores entre las medidas), la causa es el colapso de subprocesos. En este caso, la solución es investigar el origen u orígenes del colapso de subprocesos (subprocesos potencialmente bloqueados) o escalar los equipos a un tamaño de recurso mayor.

#### <a name="solution"></a>Solución:
La aplicación cliente que usa el SDK se debe escalar vertical u horizontalmente.

### <a name="socket-or-port-availability-might-be-low"></a>La disponibilidad de puertos o de sockets puede ser reducida
Cuando se ejecutan en Azure, los clientes que usan el SDK de .NET pueden alcanzar el agotamiento de puertos SNAT (PAT) de Azure.

#### <a name="solution-1"></a>Solución 1:
Si utiliza máquinas virtuales de Azure, siga la [guía de agotamiento de los puertos SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Solución 2:
Si utiliza Azure App Service, siga la [guía de solución de problemas de errores de conexión](../../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) y [use el diagnóstico de App Service](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Solución 3:
Si usa Azure Functions, compruebe que está siguiendo la [recomendación para Azure Functions](../../azure-functions/manage-connections.md#static-clients) del mantenimiento de los clientes Singleton o estáticos para todos los servicios implicados (incluido Azure Cosmos DB). Compruebe los [límites de servicio](../../azure-functions/functions-scale.md#service-limits) según el tipo y el tamaño del hospedaje de la aplicación de funciones.

#### <a name="solution-4"></a>Solución 4:
Si usa un Proxy HTTP, asegúrese de que pueda admitir el número de conexiones configuradas en el SDK de `ConnectionPolicy`. En caso contrario, se encontrará con problemas de conexión.

### <a name="create-multiple-client-instances"></a>Creación de varias instancias de cliente
La creación de varias instancias de cliente podría provocar problemas de tiempo de espera y la contención de la conexión.

#### <a name="solution"></a>Solución:
Siga los [consejos de rendimiento](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage) y use una sola instancia de CosmosClient en un proceso completo.

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
* [Diagnóstico y solución de problemas](troubleshoot-dot-net-sdk.md) al utilizar el SDK de Azure Cosmos DB para .NET.
* Más información sobre las directrices de rendimiento de [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) y [.NET v2](performance-tips.md).