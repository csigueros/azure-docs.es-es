---
title: Creación y administración de Azure Cosmos DB con Bicep
description: Use Bicep para crear y configurar Azure Cosmos DB para Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: b5a3fcdffe3c93a396bb607595b1ee02775a26f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699444"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-bicep"></a>Administración de recursos de Core (SQL) API de Azure Cosmos DB con Bicep

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo, aprenderá a usar Bicep para implementar y administrar cuentas, bases de datos y contenedores de Azure Cosmos DB.

En este artículo se muestran ejemplos de Bicep para cuentas de Core (SQL) API. También puede encontrar ejemplos de Bicep para las API de [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) y [Table](../table/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Los nombres de cuenta están limitados a 44 caracteres, todo en minúsculas.
> * Para cambiar los valores de rendimiento, vuelva a implementar el archivo de Bicep con las RU/s actualizadas.
> * Cuando agrega o elimina ubicaciones en una cuenta de Azure Cosmos, no puede modificar otras propiedades simultáneamente. Estas operaciones se deben realizar por separado.
> * No se puede cambiar el nombre de los recursos de Azure Cosmos DB, ya que esto infringe la forma de funcionar de Azure Resource Manager con los URI de recursos.
> * Para aprovisionar el rendimiento en el nivel de base de datos y compartirlo entre todos los contenedores, aplique los valores de rendimiento a la propiedad de opciones de base de datos.

Para crear cualquiera de los siguientes recursos de Azure Cosmos DB, copie el ejemplo siguiente en un archivo de Bicep nuevo. También puede crear un archivo de parámetros para usarlo al implementar varias instancias del mismo recurso con otros nombres y valores. Hay muchas maneras de implementar plantillas de Azure Resource Manager, como la [CLI de Azure](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) y [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Cuenta de Azure Cosmos con rendimiento de escalabilidad automática

Cree una cuenta de Azure Cosmos en dos regiones con opciones para la coherencia y la conmutación por error, con una base de datos y un contenedor configurados para el rendimiento de escalabilidad automática con la mayoría de las opciones de directiva habilitadas.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/main.bicep":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Cuenta de Azure Cosmos con el almacén analítico

Cree una cuenta de Azure Cosmos en una región con un contenedor con TTL analítico habilitado y opciones para el rendimiento de escalado automático o manual.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Cuenta de Azure Cosmos con rendimiento aprovisionado estándar

Cree una cuenta de Azure Cosmos en dos regiones con opciones para la coherencia y la conmutación por error, con una base de datos y un contenedor configurados para el rendimiento de estándar con la mayoría de las opciones de directiva habilitadas.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/main.bicep":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Contenedor de Azure Cosmos DB con funcionalidad del lado servidor

Cree una cuenta, una base de datos y un contenedor de Azure Cosmos con un procedimiento almacenado, un desencadenador y una función definida por el usuario.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/main.bicep":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Cuenta de Azure Cosmos DB con Azure AD y RBAC

Cree una cuenta de Azure Cosmos, una definición de rol mantenida de forma nativa y una asignación de roles mantenida de forma nativa para una identidad de AAD.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/main.bicep":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Cuenta de Azure Cosmos DB de nivel gratis

Cree una cuenta de Azure Cosmos de nivel gratis y una base de datos con rendimiento compartido que se pueda compartir con hasta 25 contenedores.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/main.bicep":::

## <a name="next-steps"></a>Pasos siguientes

Estos son algunos recursos adicionales:

* [Documentación de Bicep](../../azure-resource-manager/bicep/index.yml)
* [Instalación de herramientas de Bicep](../../azure-resource-manager/bicep/install.md)
