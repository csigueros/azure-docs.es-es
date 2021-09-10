---
title: Ejemplos de la CLI de Azure para Azure Cosmos DB API para MongoDB
description: Ejemplos de la CLI de Azure para Azure Cosmos DB API para MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 08/26/2021
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 04ac3178b439e755bbcbd33855ff0bab9a566784
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029207"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>Ejemplos de la CLI de Azure para Azure Cosmos DB API para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

En la tabla siguiente se incluyen vínculos a scripts de ejemplo de la CLI de Azure para Azure Cosmos DB. Use los vínculos de la derecha para ir a ejemplos específicos de la API. Los ejemplos comunes son los mismos en todas las API. Hay páginas de referencia para todos los comandos de CLI de Azure Cosmos DB disponibles en la [referencia de la CLI de Azure](/cli/azure/cosmosdb). Encontrará más ejemplos de scripts de la CLI de Azure Cosmos DB en el [repositorio de la CLI de Azure Cosmos DB en GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Estos ejemplos requieren la versión 2.12.1 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Ejemplos comunes

Estos ejemplos se aplican a todas las API de Azure Cosmos DB

|Tarea | Descripción |
|---|---|
| [Agregar o regiones de conmutación por error](../scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Agrega una región, cambia la prioridad de conmutación por error y desencadena una conmutación por error manual.|
| [Claves de cuenta y cadenas de conexión](../scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Muestra las claves de cuenta y las claves de solo lectura, vuelve a generar las claves y enumera las cadenas de conexión.|
| [Protección con firewall de IP](../scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una cuenta de Cosmos con firewall de IP configurado.|
| [Proteger una cuenta nueva con puntos de conexión de servicio](../scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una cuenta de Cosmos y la protege con puntos de conexión de servicio.|
| [Proteger una cuenta existente con puntos de conexión de servicio](../scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Actualiza una cuenta de Cosmos para protegerla con puntos de conexión de servicio cuando la subred se configura finalmente.|
|||

## <a name="mongodb-api-samples"></a>Ejemplos de MongoDB API

|Tarea | Descripción |
|---|---|
| [Crear una cuenta, una base de datos y una colección de Azure Cosmos](../scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una cuenta de Azure Cosmos DB y una base de datos y una colección de MongoDB API. |
| [Creación de una cuenta, una base de datos con escalabilidad automática y dos colecciones con rendimiento compartido de Azure Cosmos](../scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una cuenta, una base de datos con escalabilidad automática y dos colecciones con rendimiento compartido para MongoDB API de Azure Cosmos DB. |
| [Operaciones de capacidad de proceso](../scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Lee, actualiza y migra entre la escalabilidad automática y la capacidad de proceso estándar en una base de datos y una colección.|
| [Bloquear recursos contra la eliminación](../scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Bloquee los recursos para impedir que se eliminen.|
|||

## <a name="next-steps"></a>Pasos siguientes

¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
* Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).
