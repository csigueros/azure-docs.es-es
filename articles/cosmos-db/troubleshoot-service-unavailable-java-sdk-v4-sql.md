---
title: Solución de problemas de excepciones de servicio no disponible de Azure Cosmos DB con el SDK para Java v4
description: Descubra cómo diagnosticar y corregir las excepciones de servicio no disponible de Azure Cosmos DB con el SDK para Java v4.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 162b311879a5096966f1bd7f88313b93da809462
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123036251"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnóstico y solución de problemas de excepciones de servicio no disponible de Azure Cosmos DB con el SDK para Java v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
El SDK para Java v4 no pudo conectarse a Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas
La lista siguiente contiene las causas conocidas y las soluciones para las excepciones de servicio no disponible.

### <a name="the-required-ports-are-being-blocked"></a>Los puertos necesarios se están bloqueando
Compruebe que todos los [puertos necesarios](sql-sdk-connection-modes.md#service-port-ranges) estén habilitados.

### <a name="client-initialization-failure"></a>Error de inicialización de cliente
La siguiente excepción se produce si el SDK no puede comunicarse con la instancia de Cosmos DB. Normalmente, esto apunta a que algún protocolo de seguridad, como una regla de firewall, está bloqueando las solicitudes.

```java
 java.lang.RuntimeException: Client initialization failed. Check if the endpoint is reachable and if your auth token is valid
```

Para comprobar que el SDK puede comunicarse con la cuenta de Cosmos DB, ejecute el siguiente comando desde la ubicación en que se hospeda la aplicación. Si se produce un error, esto apunta a que una regla de firewall u otra característica de seguridad está bloqueando la solicitud. Si no hay error, el SDK debería poder comunicarse con la cuenta de Cosmos DB.
```
telnet myCosmosDbAccountName.documents.azure.com 443
```

### <a name="client-side-transient-connectivity-issues"></a>Problemas de conectividad transitorios en el lado cliente
Las excepciones de servicio no disponible pueden aparecer cuando se producen problemas de conectividad transitorios que provocan tiempos de espera. Normalmente, el seguimiento de la pila relacionado con este escenario contendrá un error `ServiceUnavailableException` con detalles de diagnóstico. Por ejemplo:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Para resolverlo, siga los [pasos de solución de problemas relativos al tiempo de espera de solicitud](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps).

### <a name="service-outage"></a>Interrupción del servicio
Compruebe el [estado de Azure](https://status.azure.com/status) para ver si hay un problema continuado.


## <a name="next-steps"></a>Pasos siguientes
* [Diagnóstico y solución de problemas](troubleshoot-java-sdk-v4-sql.md) al utilizar el SDK de Azure Cosmos DB para Java v4.
* Obtenga información sobre las instrucciones de rendimiento del [SDK para Java v4](performance-tips-java-sdk-v4-sql.md).