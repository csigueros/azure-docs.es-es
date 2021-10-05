---
title: Creación y administración de Gremlin API de Azure Cosmos DB con Bicep
description: Use Bicep para crear y configurar Gremlin API de Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: a5ac7ad31cb09ab57948f9a7b1eb86e07ec608a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699526"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-bicep"></a>Administración de recursos de Gremlin API de Azure Cosmos DB mediante Bicep

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

En este artículo, aprenderá a usar Bicep para implementar y administrar cuentas, bases de datos y grafos de Gremlin API de Azure Cosmos DB.

En este artículo se muestran ejemplos de Bicep para cuentas de Gremlin API. También puede encontrar ejemplos de Bicep para las API de [SQL](../sql/manage-with-bicep.md), [Cassandra](../cassandra/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) y [Table](../table/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Los nombres de cuenta están limitados a 44 caracteres, todo en minúsculas.
> * Para cambiar los valores de rendimiento, vuelva a implementar la plantilla con las RU/s actualizadas.
> * Cuando agrega o elimina ubicaciones en una cuenta de Azure Cosmos, no puede modificar otras propiedades simultáneamente. Estas operaciones se deben realizar por separado.

Para crear cualquiera de los recursos siguientes de Azure Cosmos DB, copie este ejemplo en un archivo de Bicep nuevo. También puede crear un archivo de parámetros para usarlo al implementar varias instancias del mismo recurso con otros nombres y valores. Hay muchas maneras de implementar plantillas de Azure Resource Manager, como la [CLI de Azure](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) y [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="gremlin-api-with-autoscale-provisioned-throughput"></a>Gremlin API con rendimiento aprovisionado de escalabilidad automática

Cree una cuenta de Azure Cosmos para Gremlin API con una base de datos y un grafo con rendimiento de escalabilidad automática.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="gremlin-api-with-standard-provisioned-throughput"></a>Gremlin API con rendimiento aprovisionado estándar

Cree una cuenta de Azure Cosmos para Gremlin API con una base de datos y un grafo con un rendimiento estándar.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin/main.bicep":::

## <a name="next-steps"></a>Pasos siguientes

Estos son algunos recursos adicionales:

* [Documentación de Bicep](../../azure-resource-manager/bicep/index.yml)
* [Instalación de herramientas de Bicep](../../azure-resource-manager/bicep/install.md)
