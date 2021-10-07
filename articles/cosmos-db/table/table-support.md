---
title: Compatibilidad de Azure Table Storage con Azure Cosmos DB
description: Obtenga información acerca de la forma en que Table API de Azure Cosmos DB y las tablas de Azure Storage funcionan conjuntamente compartiendo el mismo modelo de datos de tabla y las mismas operaciones
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 08/25/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: f2d337f7154e2ae9412930aab9720a47bde4768e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762044"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Implementación con Table API de Azure Cosmos DB y Azure Table Storage
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Table API de Azure Cosmos DB y Azure Table Storage comparten el mismo modelo de datos de tablas y exponen las mismas operaciones de creación, eliminación, actualización y consulta a través de sus SDK.

> [!NOTE]
> El [modo de capacidad sin servidor](../serverless.md) ahora está disponible en Table API de Azure Cosmos DB.

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="azure-sdks"></a>SDK de Azure

### <a name="current-release"></a>Versión actual

Los siguientes paquetes de SDK funcionan con Table API de Azure Cosmos y Azure Table Storage.

* **.NET**: use [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) disponible en NuGet.

* **Python**: use [azure-data-tables](https://pypi.org/project/azure-data-tables/) disponible en PyPi.

* **JavaScript/TypeScript**: use el paquete [@azure/data-tables](https://www.npmjs.com/package/@azure/data-tables) disponible en npm.js.  

* **Java**: use el paquete [azure-data-tables](https://mvnrepository.com/artifact/com.azure/azure-data-tables/12.0.0) disponible en Maven.

### <a name="prior-releases"></a>Versiones anteriores

Los siguientes paquetes de SDK solo funcionan con Table API de Azure Cosmos DB.

* **.NET** - [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) disponible en NuGet.  Esta biblioteca solo funciona con Table API de Cosmos DB.

* **Python** - [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/) disponible en PyPi. Este SDK se conecta con Azure Table Storage y Table API de Azure Cosmos DB.

* **JavaScript/TypeScript** - [azure-storage](https://www.npmjs.com/package/azure-storage) disponible en npm.js. Este SDK de Azure Storage tiene la capacidad de conectarse a las cuentas de Azure Cosmos DB mediante Table API.

* **Java** - [SDK de cliente de Microsoft Azure Storage para Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) en Maven. Este SDK de Azure Storage tiene la capacidad de conectarse a las cuentas de Azure Cosmos DB mediante Table API.

* **C++**   - [Biblioteca cliente de Azure Storage para C++](https://github.com/Azure/azure-storage-cpp/). Esta biblioteca le permite compilar aplicaciones en Azure Storage.

* **Ruby** - [Biblioteca cliente de Azure Storage Table para Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Este proyecto proporciona un paquete Ruby que facilita el acceso a instancias de Azure Storage Table service.

* **PHP** - [Biblioteca cliente de PHP de Azure Storage Table](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Este proyecto proporciona una biblioteca cliente de PHP que facilita el acceso a instancias de Azure Storage Table service.

* **PowerShell** - [Módulo de PowerShell AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Este módulo de PowerShell contiene cmdlets para trabajar con tablas de almacenamiento.
