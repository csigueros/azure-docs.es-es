---
title: Plantillas de Azure Resource Manager para Azure Cosmos DB Core (API de SQL)
description: Use las plantillas de Azure Resource Manager para crear y configurar Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: bb39414e73ec3df2d2714d794f7060815b99bce1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735417"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Plantillas de Azure Resource Manager para Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En este artículo solo se muestran ejemplos de plantillas de Azure Resource Manager para cuentas de Core (SQL) API. También puede encontrar ejemplos de plantillas para [Cassandra API](cassandra/templates-samples.md), [Gremlin API](templates-samples-gremlin.md), [MongoDB API](mongodb/resource-manager-template-samples.md) y [Table API](table/resource-manager-templates.md).

## <a name="core-sql-api"></a>API Core (SQL)

|**Plantilla**|**Descripción**|
|---|---|
|[Creación de una cuenta, una base de datos y un contenedor de Azure Cosmos con rendimiento de escalabilidad automática](manage-with-templates.md#create-autoscale) | Esta plantilla crea una cuenta de API Core (SQL) en dos regiones, una base de datos y un contenedor con rendimiento de escalabilidad automática. |
|[Creación de una cuenta, una base de datos y un contenedor de Azure Cosmos con un almacén analítico](manage-with-templates.md#create-analytical-store) | Esta plantilla crea una cuenta de Core (SQL) API en una región con un contenedor configurado con TTL analítico habilitado y una opción para usar el rendimiento manual o de escalado automático. |
|[Creación de una cuenta, una base de datos y un contenedor de Azure Cosmos con rendimiento estándar (manual)](manage-with-templates.md#create-manual) | Esta plantilla crea una cuenta de API Core (SQL) en dos regiones, una base de datos y un contenedor con rendimiento estándar. |
|[Creación de una cuenta, una base de datos y un contenedor de Azure Cosmos con un procedimiento almacenado, un desencadenador y una UDF](manage-with-templates.md#create-sproc) | Esta plantilla crea una cuenta de API Core (SQL) en dos regiones con un procedimiento almacenado, un desencadenador y una UDF para un contenedor. |
|[Creación de una cuenta de Azure Cosmos con una identidad de Azure AD, definiciones de roles y asignación de roles](manage-with-templates.md#create-rbac) | Esta plantilla crea una cuenta de API Core (SQL) con una identidad de Azure AD, definiciones de roles y asignación de roles en una entidad de servicio. |
|[Creación de un punto de conexión privado para la cuenta de Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Esta plantilla crea un punto de conexión privado para una cuenta existente de la API Core (SQL) de Azure Cosmos en una red virtual existente. |
|[Creación de una cuenta de Azure Cosmos gratuita](manage-with-templates.md#free-tier) |  Esta plantilla crea una cuenta de API Core (SQL) de Azure Cosmos DB gratuita. |

Consulte la página de [referencia de Azure Resource Manager para Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) de la documentación de referencia.
