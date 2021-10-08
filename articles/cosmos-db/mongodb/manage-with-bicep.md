---
title: Creación y administración de MongoDB API para Azure Cosmos DB con Bicep
description: Use Bicep para crear y configurar la API de Azure Cosmos DB para MongoDB API.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: e6ded17c15b9a126a996f4e6f368a5ab5c735ed9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699776"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-bicep"></a>Administración de Azure Cosmos DB para recursos de MongoDB API mediante Bicep

[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

En este artículo obtendrá información sobre cómo usar Bicep para implementar y administrar sus cuentas de Azure Cosmos DB para MongoDB API, bases de datos y colecciones.

En este artículo se muestran ejemplos de Bicep para cuentas de Gremlin API. También puede encontrar ejemplos de Bicep para API de [SQL](../sql/manage-with-bicep.md), [Cassandra](../cassandra/manage-with-bicep.md), [MongoDB](../graph/manage-with-bicep.md) y [Gremlin](../table/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Los nombres de cuenta están limitados a 44 caracteres, todo en minúsculas.
> * Para cambiar los valores de rendimiento, vuelva a implementar la plantilla con las RU/s actualizadas.
> * Cuando agrega o elimina ubicaciones en una cuenta de Azure Cosmos, no puede modificar otras propiedades simultáneamente. Estas operaciones se deben realizar por separado.

Para crear cualquiera de los recursos de Azure Cosmos DB que figuran a continuación, copie el siguiente ejemplo en un nuevo archivo de Bicep. También puede crear un archivo de parámetros para usarlo al implementar varias instancias del mismo recurso con otros nombres y valores. Hay muchas maneras de implementar plantillas de Azure Resource Manager, como la [CLI de Azure](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) y [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="mongodb-api-with-autoscale-provisioned-throughput"></a>MongoDB API con rendimiento aprovisionado de escalabilidad automática

Esta plantilla creará una cuenta de Azure Cosmos para MongoDB API (3.2, 3.6 o 4.0) con dos colecciones que compartirán el rendimiento de escalabilidad automática en el nivel de base de datos.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="mongodb-api-with-standard-provisioned-throughput"></a>MongoDB API con rendimiento aprovisionado estándar

Cree una cuenta de Azure Cosmos para MongoDB API (3.2, 3.6 o 4.0) con dos colecciones que compartirán el rendimiento estándar (manual) de 400 RU/s en el nivel de base de datos.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/main.bicep":::

## <a name="next-steps"></a>Pasos siguientes

Estos son algunos recursos adicionales:

* [Documentación de Bicep](../../azure-resource-manager/bicep/index.yml)
* [Instalación de herramientas de Bicep](../../azure-resource-manager/bicep/install.md)
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de bases de datos existente.
  * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md).
  * Si conoce las velocidades de solicitud típicas de la carga de trabajo de base de datos actual, lea sobre el [cálculo de las unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).
