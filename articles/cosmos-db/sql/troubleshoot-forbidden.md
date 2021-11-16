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
ms.openlocfilehash: 9a5f1590e73388429f613a1c6b078f5c315397d6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238951"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnóstico y solución de problemas de excepciones de solicitudes prohibidas de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El código de estado HTTP 403 significa que se prohíbe que se complete la solicitud.

## <a name="firewall-blocking-requests"></a>Solicitudes de bloqueo de firewall

Las solicitudes del plano de datos pueden llegar a Cosmos DB a través de las tres rutas de acceso siguientes.

- Red pública de Internet (IPv4)
- Punto de conexión de servicio
- Punto de conexión privado

Cuando una solicitud del plano de datos se bloquea con el error 403 Prohibido, el mensaje de error especificará a través de cuál de las tres rutas de acceso anteriores llegó la solicitud a Cosmos DB.

- `Request originated from client IP {...} through public internet.`
- `Request originated from client VNET through service endpoint.`
- `Request originated from client VNET through private endpoint.`

### <a name="solution"></a>Solución

Determine a través de qué ruta de acceso **se espera** que llegue la solicitud a Cosmos DB.
   - Si el mensaje de error muestra que la solicitud no llegó a Cosmos DB a través de la ruta de acceso esperada, es probable que el problema se deba a la configuración del cliente. Compruebe la configuración del lado cliente en función de las documentaciones siguientes.
      - Red pública de Internet: [Configuración del firewall de IP en Azure Cosmos DB](../how-to-configure-firewall.md).
      - Punto de conexión de servicio: [Configuración del acceso a Azure Cosmos DB desde redes virtuales (VNet)](../how-to-configure-vnet-service-endpoint.md). Por ejemplo, si espera usar el punto de conexión de servicio pero la solicitud llegó a Cosmos DB a través de la red pública de Internet, es posible que la subred en que se ejecutaba el cliente no tuviera habilitado el punto de conexión de servicio a Cosmos DB.
      - Punto de conexión privado: [Configuración de Azure Private Link para una cuenta de Azure Cosmos](../how-to-configure-private-endpoints.md). Por ejemplo, si espera usar el punto de conexión privado pero la solicitud llegó a Cosmos DB a través de la red pública de Internet, es posible que el DNS en la VM no estuviera configurado para resolver el punto de conexión de la cuenta en la dirección IP privada, por lo que, en su lugar, se realizó a través de la IP pública de la cuenta.
   - Si la solicitud llegó a Cosmos DB a través de la ruta de acceso esperada, la solicitud se bloqueó porque la identidad de la red de origen no estaba configurada como permitida para la cuenta. Compruebe la configuración de la cuenta en función de la ruta de acceso a través de la que llegó la solicitud a Cosmos DB.
      - Red de Internet pública: compruebe las configuraciones de filtro de intervalo IP y el [acceso a la red pública](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation) de la cuenta.
      - Punto de conexión de servicio: compruebe las configuraciones de filtro de red virtual y el [acceso a la red pública](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation) de la cuenta.
      - Punto de conexión privado: compruebe la configuración de DNS privado del cliente y la configuración del punto de conexión privado de la cuenta. Esto podría deberse al acceso a la cuenta desde un punto de conexión privado que esté configurado para otra cuenta.

Si actualizó recientemente las configuraciones de firewall de la cuenta, tenga en cuenta que los cambios pueden tardar **hasta 15 minutos en aplicarse**.

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
