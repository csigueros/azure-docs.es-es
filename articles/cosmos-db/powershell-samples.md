---
title: Ejemplos de Azure PowerShell para Core (SQL) API de Azure Cosmos DB
description: Obtenga ejemplos de Azure PowerShell para realizar tareas comunes en Azure Cosmos DB para Core (SQL) API.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/26/2021
ms.author: mjbrown
ms.openlocfilehash: 1afe6e2552cc71f13d34cc2d13c4f02dfe05f3b0
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123032564"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Ejemplos de Azure PowerShell para Core (SQL) API de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En la tabla siguiente se incluyen vínculos a scripts de Azure PowerShell usados habitualmente para Azure Cosmos DB. Use los vínculos de la derecha para ir a ejemplos específicos de la API. Los ejemplos comunes son los mismos en todas las API. En la [referencia de Azure PowerShell](/powershell/module/az.cosmosdb) hay páginas de referencia para todos los cmdlets de PowerShell de Azure Cosmos DB disponibles. El módulo `Az.CosmosDB` ahora es parte del módulo `Az`. [Descargue e instale](/powershell/azure/install-az-ps) la versión más reciente del módulo Az Module para obtener los cmdlets de Azure Cosmos DB. También puede obtener la versión más reciente de la [Galería de PowerShell](https://www.powershellgallery.com/packages/Az/5.4.0). También puede bifurcar estos ejemplos de PowerShell para Cosmos DB desde nuestro repositorio de GitHub, [Ejemplos de PowerShell para Cosmos DB en GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Para obtener cmdlets de PowerShell para otras API, consulte [Ejemplos de PowerShell para Cassandra](cassandra/powershell-samples.md), [Ejemplos de PowerShell para MongoDB API](powershell-samples-mongodb.md), [Ejemplos de PowerShell para Gremlin](powershell-samples-gremlin.md) y [Ejemplos de PowerShell para Table](powershell-samples-table.md).

## <a name="common-samples"></a>Ejemplos comunes

|Tarea | Descripción |
|---|---|
|[Actualización de una cuenta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Actualice el nivel de coherencia predeterminado de una cuenta de Cosmos DB. |
|[Actualización de las regiones de una cuenta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Actualice las regiones de una cuenta de Cosmos DB. |
|[Cambio de la prioridad de la conmutación por error o desencadenamiento de la conmutación por error](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cambie la prioridad de la conmutación por error regional de una cuenta de Azure Cosmos o desencadene una conmutación por error manual. |
|[Claves de cuenta o cadenas de conexión](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenga las claves principales y secundarias, las cadenas de conexión o vuelva a generar una clave de cuenta de una cuenta de Azure Cosmos DB. |
|[Creación de una cuenta de Cosmos con firewall de IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cree una cuenta de Azure Cosmos DB con firewall de IP habilitado. |
|||

## <a name="core-sql-api-samples"></a>Ejemplos de API Core (SQL)

|Tarea | Descripción |
|---|---|
|[Crear una cuenta, base de datos y contenedor](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cree una cuenta, una base de datos y un contenedor de Azure Cosmos DB. |
|[Creación de una cuenta, una base de datos y un contenedor con escalabilidad automática](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cree una cuenta, una base de datos y un contenedor de Azure Cosmos DB con escalabilidad automática. |
|[Creación de un contenedor con una clave de partición de gran tamaño](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cree un contenedor con una clave de partición de gran tamaño. |
|[Creación de un contenedor sin directiva de índice](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cree un contenedor de Azure Cosmos con directiva de índice desactivada.|
|[Enumerar u obtener las bases de datos o contenedores](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Enumere u obtenga las bases de datos o contenedores. |
|[Operaciones de capacidad de proceso](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operaciones relacionadas con la capacidad de proceso de una base de datos o un contenedor, como obtener, actualizar y migrar entre la escalabilidad automática y la capacidad de proceso estándar. |
|[Bloquear recursos contra la eliminación](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Evite la eliminación de recursos con bloqueos de recursos. |
|||

## <a name="next-steps"></a>Pasos siguientes

¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, obtenga información sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
* Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
