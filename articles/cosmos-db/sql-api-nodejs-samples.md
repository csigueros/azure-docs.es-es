---
title: Ejemplos de Node.js para administrar datos en Azure Cosmos DB
description: Busque ejemplos de Node.js en GitHub para tareas comunes en Azure Cosmos DB, incluidas las operaciones CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 243bdf7c3383669b98529622048f14988f0b8ac4
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112239203"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Ejemplos de Node.js para administrar datos en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Ejemplos del SDK de .NET V2](sql-api-dotnet-samples.md)
> * [Ejemplos del SDK de .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Ejemplos del SDK de Java V4](sql-api-java-sdk-samples.md)
> * [Ejemplos del SDK de Spring Data V3](sql-api-spring-data-sdk-samples.md)
> * [Ejemplos de Node.js](sql-api-nodejs-samples.md)
> * [Ejemplos de Python](sql-api-python-samples.md)
> * [Galería de ejemplos de código de Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

En el repositorio de GitHub [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) se incluyen soluciones de ejemplo que realizan operaciones CRUD y otras operaciones comunes en recursos de Azure Cosmos DB. Este artículo ofrece:

* Vínculos a las tareas de cada uno de los archivos de proyecto de ejemplo de Node.js.
* Vínculos al contenido de referencia de la API relacionada.

**Requisitos previos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Puede [activar los beneficios de suscripción a Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): su suscripción a Visual Studio le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.

[!INCLUDE [cosmos-db-emulator-docdb-api](includes/cosmos-db-emulator-docdb-api.md)]

También necesita el [SDK de JavaScript](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Cada ejemplo es independiente, es decir, se configura a sí mismo y posteriormente se limpia solo. Por tanto, los ejemplos emiten varias llamadas a [Containers.create](/javascript/api/%40azure/cosmos/containers). Cada vez que esto ocurre, se cobra a la suscripción una hora de uso de acuerdo con el nivel de rendimiento del contenedor que se va a crear.
   > 
   > 

## <a name="database-examples"></a>Ejemplos de base de datos

El archivo [DatabaseManagement ](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) muestra cómo realizar las operaciones CRUD en la base de datos. Para información sobre las bases de datos de Azure Cosmos antes de ejecutar los siguientes ejemplos, consulte el artículo conceptual [Uso de bases de datos, contenedores y elementos](account-databases-containers-items.md). 

| Tarea | Referencia de API |
| --- | --- |
| [Creación de una base de datos si no hay una](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases#createifnotexists-databaserequest--requestoptions-) |
| [Enumeración de las bases de datos de una cuenta](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases#readall-feedoptions-) |
| [Lectura de una base de datos por identificador](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database#read-requestoptions-) |
| [Eliminación de una base de datos](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database#delete-requestoptions-) |

## <a name="container-examples"></a>Ejemplos de contenedor

El archivo [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) muestra cómo realizar las operaciones CRUD en el contenedor. Para información sobre las colecciones de Azure Cosmos antes de ejecutar los siguientes ejemplos, consulte el artículo conceptual [Uso de bases de datos, contenedores y elementos](account-databases-containers-items.md). 

| Tarea | Referencia de API |
| --- | --- |
| [Creación de un contenedor si no hay uno](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers#createifnotexists-containerrequest--requestoptions-) |
| [Enumeración de contenedores de una cuenta](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers#readall-feedoptions-) |
| [Lectura del rendimiento de un contenedor](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container#read-requestoptions-) |
| [Eliminación de un contenedor](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container#delete-requestoptions-) |

## <a name="item-examples"></a>Ejemplos de elementos

El archivo [ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) muestra cómo realizar las operaciones CRUD en el elemento. Para información sobre los documentos de Azure Cosmos antes de ejecutar los ejemplos siguientes, consulte el artículo conceptual [Uso de bases de datos, contenedores y elementos](account-databases-containers-items.md). 

| Tarea | Referencia de API |
| --- | --- |
| [Creación de elementos](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items#create-t--requestoptions-) |
| [Lectura de todos los elementos de un contenedor](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items#readall-feedoptions-) |
| [Lectura de un elemento por identificador](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item#read-requestoptions-) |
| [Lectura de un elemento solo si el elemento ha cambiado](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [Consulta de documentos](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items) |
| [Reemplazo de un elemento](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item) |
| [Reemplazo de un artículo con la comprobación de ETag condicional](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [Eliminación de un elemento](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item) |

## <a name="indexing-examples"></a>Ejemplos de indización

El archivo [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) muestra cómo administrar la indexación. Para información sobre la indexación en Azure Cosmos DB antes de ejecutar los ejemplos siguientes, consulte los artículos conceptuales sobre las [directivas de indexación](index-policy.md), los [tipos de indexación](index-overview.md#index-types) y las [rutas de acceso de indexación](index-policy.md#include-exclude-paths). 

| Tarea | Referencia de API |
| --- | --- |
| [Indexación manual de un elemento determinado](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [Exclusión manual de un elemento determinado del índice](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [Exclusión de una ruta de acceso del índice](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy#excludedpaths) |
| [Crear un índice de intervalo en una ruta de acceso de cadena](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind), [IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy), [Items.query](/javascript/api/%40azure/cosmos/items) |
| [Creación de un contenedor con indexPolicy de forma predeterminada y posterior actualización en línea](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers)

## <a name="server-side-programming-examples"></a>Ejemplos de programación en el servidor

El archivo [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) del proyecto [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) muestra cómo realizar las siguientes tareas. Para información sobre la programación del lado servidor en Azure Cosmos DB antes de ejecutar los ejemplos siguientes, consulte el artículo conceptual [Procedimientos almacenados, desencadenadores y funciones definidas por el usuario](stored-procedures-triggers-udfs.md). 

| Tarea | Referencia de API |
| --- | --- |
| [Creación de un procedimiento almacenado](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures) |
| [Ejecución de un procedimiento almacenado](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure) |

Para más información sobre la programación en el servidor, consulte [Programación en el servidor de Azure Cosmos DB: procedimientos almacenados, desencadenadores de base de datos y UDF](stored-procedures-triggers-udfs.md).
