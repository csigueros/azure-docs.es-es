---
title: Solución de problemas de excepciones de solicitudes prohibidas de Azure Cosmos DB
description: Aprenda a diagnosticar y corregir excepciones de solicitudes prohibidas.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: dcef0eccc6ca314c51b436025fb10c25dbbe04fa
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658241"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnóstico y solución de problemas de excepciones de solicitudes prohibidas de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El código de estado HTTP 403 significa que se prohíbe que se complete la solicitud.

## <a name="firewall-blocking-requests"></a>Solicitudes de bloqueo de firewall
En este escenario, es habitual ver errores como los siguientes:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Solución
Compruebe que la [configuración actual del firewall](../how-to-configure-firewall.md) sea correcta e incluya las direcciones IP o redes a las que está intentando conectarse.
Si las ha actualizado recientemente, tenga en cuenta que los cambios pueden tardar **hasta 15 minutos en aplicarse**.

## <a name="partition-key-exceeding-storage"></a>Clave de partición que supera el almacenamiento
En este escenario, es habitual ver errores como los siguientes:

```
Response status code does not indicate success: Forbidden (403); Substatus: 1014
```

```
Partition key reached maximum size of {...} GB
```

### <a name="solution"></a>Solución
Este error significa que la carga de trabajo y el [diseño de creación de particiones](../partitioning-overview.md#logical-partitions) actuales están intentando almacenar más de la cantidad de datos permitida para un valor de clave de partición determinado. No hay ningún límite en el número de particiones lógicas del contenedor, pero el tamaño de los datos que puede almacenar cada partición lógica es limitado. Puede comunicarse con el soporte técnico para que le den explicaciones.

## <a name="non-data-operations-are-not-allowed"></a>No se permiten operaciones que no son de datos
Este escenario se produce cuando [no se pueden realizar en la cuenta operaciones](../how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations) que no son de datos. En este escenario, es habitual ver errores como los siguientes:

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>Solución
Realice la operación mediante Azure Resource Manager, Azure Portal, la CLI de Azure o Azure PowerShell. O bien, vuelva a permitir la ejecución de operaciones que no son de datos.

## <a name="next-steps"></a>Pasos siguientes
* Configuración de [firewall de IP](../how-to-configure-firewall.md).
* Configuración del acceso desde [redes virtuales](../how-to-configure-vnet-service-endpoint.md).
* Configuración del acceso desde [puntos de conexión privados](../how-to-configure-private-endpoints.md).
