---
title: Aprovisionamiento del rendimiento en recursos de Cassandra API de Azure Cosmos DB
description: Obtenga información sobre cómo aprovisionar el rendimiento del contenedor, la base de datos y la escalabilidad automática en los recursos de Cassandra API de Azure Cosmos DB. Usará Azure Portal, la CLI, PowerShell y otros SDK.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8d6f1565e2cc4c7acb49649fb79d8c80bc2a4d5c
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360638"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Aprovisionamiento el rendimiento de la base de datos, el contenedor o la escalabilidad automática en los recursos de Cassandra API de Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

En este artículo se explica cómo aprovisionar el rendimiento de Cassandra API de Azure Cosmos DB. Puede aprovisionar el rendimiento estándar (manual) o de escalabilidad automática de un contenedor, o de una base de datos y compartirlo entre los contenedores incluidos en ella. Para aprovisionar el rendimiento, use Azure Portal, la CLI de Azure o los SDK de Azure Cosmos DB.

Si usa una API diferente, consulte los artículos [API de SQL](how-to-provision-container-throughput.md), [API para MongoDB](how-to-provision-throughput-mongodb.md), [Gremlin API](how-to-provision-throughput-gremlin.md) para aprovisionar el rendimiento.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) o seleccione una ya existente.

1. Abra el panel **Data Explorer** y seleccione **Nueva tabla**. Después, proporcione los detalles siguientes:

   * Indique si va a crear un nuevo espacio de claves o a usar uno existente. Seleccione la opción **Aprovisionamiento del rendimiento de la base de datos** si desea aprovisionar el rendimiento en el nivel de base de datos.
   * Escriba el identificador de tabla en el comando CQL.
   * Escriba un valor de clave principal (por ejemplo, `/userrID`).
   * Escriba un rendimiento que quiera aprovisionar (por ejemplo, 1000 RU).
   * Seleccione **Aceptar**.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Captura de pantalla del Explorador de datos al crear una nueva recopilación con el rendimiento de nivel de base de datos":::

> [!Note]
> Si va a aprovisionar el rendimiento del contenedor de una cuenta de Azure Cosmos configurada con Cassandra API, use `/myPrimaryKey` para la ruta de acceso de la clave de partición.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> SDK de .NET

### <a name="provision-throughput-for-a-cassandra-table"></a>Aprovisionamiento del rendimiento en una tabla de Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Se pueden emitir comandos similares a través de cualquier controlador compatible con CQL.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Modificación o cambio del rendimiento de una tabla de Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Se pueden ejecutar comandos similares a través de cualquier controlador compatible con CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Las plantillas de Azure Resource Manager se pueden usar para aprovisionar el rendimiento de escalado automático en luna base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Consulte [Plantillas de Azure Resource Manager para Azure Cosmos DB](templates-samples-cassandra.md) para ejemplos.

## <a name="azure-cli"></a>Azure CLI

La CLI de Azure se puede usar para aprovisionar el rendimiento de escalado automático en una base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Para ejemplos, consulte [Ejemplos de la CLI de Azure para Azure Cosmos DB](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell se puede usar para aprovisionar el rendimiento de escalado automático en una base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Para ejemplos, consulte [Ejemplos de Azure PowerShell para Azure Cosmos DB](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para aprender sobre el aprovisionamiento del rendimiento en Azure Cosmos DB:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](request-units.md)