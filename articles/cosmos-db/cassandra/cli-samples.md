---
title: Ejemplos de la CLI de Azure para Cassandra API para Azure Cosmos DB
description: Ejemplos de la CLI de Azure para Cassandra API para Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9deae17e0fa5b03e033c76abd3be136881aa2d7e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786589"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-cassandra-api"></a>Ejemplos de la CLI de Azure para Cassandra API para Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

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

## <a name="cassandra-api-samples"></a>Ejemplos de Cassandra API

|Tarea | Descripción |
|---|---|
| [Crear una cuenta, un espacio de claves y una tabla de Azure Cosmos](../scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una cuenta, un espacio de claves y una tabla de Cassandra API para Azure Cosmos DB. |
| [Creación de una cuenta de Azure Cosmos, un espacio de claves y una tabla con escalabilidad automática](../scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una cuenta, un espacio de claves y una tabla de Cassandra API con escalabilidad automática para Azure Cosmos DB. |
| [Operaciones de capacidad de proceso](../scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Lee, actualiza y migra entre la escalabilidad automática y la capacidad de proceso estándar en un espacio de claves y una tabla.|
| [Bloquear recursos contra la eliminación](../scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Bloquee los recursos para impedir que se eliminen.|
|||
