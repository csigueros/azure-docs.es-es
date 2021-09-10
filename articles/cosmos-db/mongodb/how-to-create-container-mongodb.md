---
title: Creación de un contenedor en MongoDB API de Azure Cosmos DB
description: Aprenda a crear un contenedor en MongoDB API de Azure Cosmos DB mediante Azure Portal, .NET, Java, Node.js y otros SDK.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-csharp
ms.openlocfilehash: 40458740092c7b462683512e3c419eff417019da
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038529"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Creación de un contenedor en MongoDB API de Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

En este artículo se explican las distintas formas de crear un contenedor en MongoDB API de Azure Cosmos DB. Se muestra cómo crear un contenedor mediante Azure Portal, la CLI de Azure, PowerShell o los SDK admitidos. En este artículo se muestra cómo crear un contenedor, especificar la clave de partición y aprovisionar el rendimiento.

En este artículo se explican las distintas formas de crear un contenedor en MongoDB API de Azure Cosmos DB. Si usa otra API, vea los artículos [SQL API](../how-to-create-container.md), [Cassandra API](../cassandra/how-to-create-container-cassandra.md), [Gremlin API](../how-to-create-container-gremlin.md) y [Table API](../table/how-to-create-container.md) para crear el contenedor.

> [!NOTE]
> Cuando cree contenedores, asegúrese de no utilizar el mismo nombre en dos de ellos con distintas mayúsculas y minúsculas. Algunos componentes de la plataforma de Azure no distinguen mayúsculas de minúsculas y esto puede producir confusión o problemas con los datos telemetría y las acciones que se realicen en los contenedores con estos nombres.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Creación mediante Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) o seleccione una ya existente.

1. Abra el panel **Data Explorer** y seleccione **Nuevo contenedor**. Después, proporcione los detalles siguientes:

   * Indique si va a crear una nueva base de datos o a usar una existente.
   * Escriba un identificador de contenedor.
   * Escriba una clave de partición.
   * Escriba el rendimiento que se va a aprovisionar (por ejemplo, 1000 RU).
   * Seleccione **Aceptar**.

    :::image type="content" source="../media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Captura de pantalla del cuadro de diálogo Agregar contenedor de Azure Cosmos DB API para MongoDB":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Creación mediante el SDK de .NET

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> El protocolo de conexión de MongoDB no entiende el concepto de [unidades de solicitud](../request-units.md). Para crear una colección con rendimiento aprovisionado en ella, use Azure Portal o los SDK de Cosmos DB para la API de SQL.

Si se produce una excepción de tiempo de espera al crear una colección, realice una operación de lectura para validar si la colección se ha creado correctamente. La operación de lectura emite una excepción hasta que la operación de creación de la colección se realiza correctamente. Para la lista de códigos de estado admitidos por la operación de creación, consulte el artículo [Códigos de estado HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Creación mediante la CLI de Azure

[Creación de una colección de Azure Cosmos DB para MongoDB API con la CLI de Azure](../scripts/cli/mongodb/create.md). Para obtener una lista de todos los ejemplos de la CLI de Azure en todas las API de Azure Cosmos DB, vea [Ejemplos de la CLI de Azure para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Creación mediante PowerShell

[Creación de una colección de Azure Cosmos DB para MongoDB API con PowerShell](../scripts/powershell/mongodb/create.md). Para obtener una lista de todos los ejemplos de PowerShell en todas las API de Azure Cosmos DB, vea [Ejemplos de PowerShell](powershell-samples.md).

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Creación de un contenedor mediante plantillas de Azure Resource Manager

[Creación de una colección de Azure Cosmos DB para MongoDB API con plantillas de Resource Manager](../manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Pasos siguientes

* [Creación de particiones en Azure Cosmos DB](../partitioning-overview.md)
* [Unidades de solicitud en Azure Cosmos DB](../request-units.md)
* [Aprovisionamiento del rendimiento en contenedores y bases de datos](../set-throughput.md)
* [Uso de la cuenta de Azure Cosmos](../account-databases-containers-items.md)
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).