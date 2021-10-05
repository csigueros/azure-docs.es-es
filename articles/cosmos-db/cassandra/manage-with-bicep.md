---
title: Creación y administración de Cassandra API de Azure Cosmos DB con Bicep
description: Use Bicep para crear y configurar Cassandra API de Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: 0433f6830100fc23420006ec2b980b7ccf7f9692
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594611"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-bicep"></a>Administración de recursos de Cassandra API de Azure Cosmos DB mediante Bicep

[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

En este artículo, aprenderá a usar Bicep para implementar y administrar las cuentas, los espacios de claves y las tablas de Cassandra API de Azure Cosmos DB.

En este artículo se muestran ejemplos de Bicep para cuentas de Cassandra API. También puede encontrar ejemplos de Bicep para las API de [SQL](../sql/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) y [Table](../table/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Los nombres de cuenta están limitados a 44 caracteres, todo en minúsculas.
> * Para cambiar los valores de rendimiento, vuelva a implementar la plantilla con las RU/s actualizadas.
> * Cuando agrega o elimina ubicaciones en una cuenta de Azure Cosmos, no puede modificar otras propiedades simultáneamente. Estas operaciones se deben realizar por separado.

Para crear cualquiera de los recursos siguientes de Azure Cosmos DB, copie este ejemplo en un archivo de Bicep nuevo. También puede crear un archivo de parámetros para usarlo al implementar varias instancias del mismo recurso con otros nombres y valores. Hay muchas maneras de implementar plantillas de Azure Resource Manager, como la [CLI de Azure](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) y [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="cassandra-api-with-autoscale-provisioned-throughput"></a>Cassandra API con rendimiento aprovisionado de escalabilidad automática

Cree una cuenta de Azure Cosmos en dos regiones con opciones para la coherencia y la conmutación por error, con un espacio de claves y una tabla configurados para el rendimiento de escalabilidad automática.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="cassandra-api-with-standard-provisioned-throughput"></a>Cassandra API con rendimiento aprovisionado estándar

Cree una cuenta de Azure Cosmos en dos regiones con opciones para la coherencia y la conmutación por error, con un espacio de claves y una tabla configurados para el rendimiento estándar.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra/main.bicep":::

## <a name="next-steps"></a>Pasos siguientes

Estos son algunos recursos adicionales:

* [Documentación de Bicep](../../azure-resource-manager/bicep/index.yml)
* [Instalación de herramientas de Bicep](../../azure-resource-manager/bicep/install.md)
