---
title: Creación y administración de Table API de Azure Cosmos DB con Bicep
description: Use Bicep para crear y configurar Table API de Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: fe911a945de6b7b15c49641b7aff3850c3759da2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594575"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-bicep"></a>Administración de recursos de Table API de Azure Cosmos DB mediante Bicep

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

En este artículo, aprenderá a usar Bicep para facilitar la implementación y administración de las cuentas y tablas de Table API de Azure Cosmos DB.

En este artículo solo se incluyen ejemplos para cuentas de Table API. También puede encontrar ejemplos de Bicep para artículos sobre [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) y [SQL](../sql/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Los nombres de cuenta están limitados a 44 caracteres, todo en minúsculas.
> * Para cambiar los valores de rendimiento, vuelva a implementar la plantilla con las RU/s actualizadas.
> * Cuando agrega o elimina ubicaciones en una cuenta de Azure Cosmos, no puede modificar otras propiedades simultáneamente. Estas operaciones se deben realizar por separado.

Para crear cualquiera de los siguientes recursos de Azure Cosmos DB, copie el ejemplo siguiente en un archivo de Bicep nuevo. También puede crear un archivo de parámetros para usarlo al implementar varias instancias del mismo recurso con otros nombres y valores. Hay muchas maneras de implementar plantillas de Azure Resource Manager, como la [CLI de Azure](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) y [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

> [!TIP]
> Para habilitar rendimiento compartido cuando se usa Table API, habilite el rendimiento de nivel de cuenta en Azure Portal. El rendimiento compartido de nivel de cuenta no se puede establecer mediante Bicep.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Cuenta de Azure Cosmos para Table con rendimiento de escalabilidad automática

Cree una cuenta de Azure Cosmos para Table API con una tabla con rendimiento de escalabilidad automática.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Cuenta de Azure Cosmos para Table con rendimiento aprovisionado estándar

Cree una cuenta de Azure Cosmos para Table API con una tabla con rendimiento estándar.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table/main.bicep":::

## <a name="next-steps"></a>Pasos siguientes

Estos son algunos recursos adicionales:

* [Documentación de Bicep](../../azure-resource-manager/bicep/index.yml)
* [Instalación de herramientas de Bicep](../../azure-resource-manager/bicep/install.md)
