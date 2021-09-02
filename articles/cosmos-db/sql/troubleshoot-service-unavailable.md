---
title: Solución de problemas de excepciones de servicio no disponible de Azure Cosmos DB
description: Descubra cómo diagnosticar y corregir las excepciones de servicio no disponible de Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5b734c9d1ab4a64a15e8b73d81d02a6066beb35e
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114508"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnóstico y solución de problemas de excepciones de servicio no disponible de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El SDK no pudo conectarse a Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas
La lista siguiente contiene las causas conocidas y las soluciones para las excepciones de servicio no disponible.

### <a name="the-required-ports-are-being-blocked"></a>Los puertos necesarios se están bloqueando
Compruebe que todos los [puertos necesarios](sql-sdk-connection-modes.md#service-port-ranges) estén habilitados.

### <a name="client-side-transient-connectivity-issues"></a>Problemas de conectividad transitorios en el lado cliente
Las excepciones de servicio no disponible pueden aparecer cuando se producen problemas de conectividad transitorios que provocan tiempos de espera. Normalmente, el seguimiento de la pila relacionado con este escenario contendrá un error `TransportException`. Por ejemplo:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Para resolverlo, siga los [pasos de solución de problemas relativos al tiempo de espera de solicitud](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps).

### <a name="service-outage"></a>Interrupción del servicio
Compruebe el [estado de Azure](https://status.azure.com/status) para ver si hay un problema continuado.


## <a name="next-steps"></a>Pasos siguientes
* [Diagnóstico y solución de problemas](troubleshoot-dot-net-sdk.md) al utilizar el SDK de Azure Cosmos DB para .NET.
* Más información sobre las directrices de rendimiento de [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) y [.NET v2](performance-tips.md).