---
title: Ejemplos de Bicep para Azure Cosmos DB Core (SQL API)
description: Use Bicep para crear y configurar Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 8cab03d2b3cd3c6d7b6a2f9bd15c686bea529de9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699724"
---
# <a name="bicep-for-azure-cosmos-db"></a>Bicep para Azure Cosmos DB

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo se muestran ejemplos de Bicep para cuentas de Core (SQL) API. También puede encontrar ejemplos de Bicep para las API de [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) y [Table](../table/manage-with-bicep.md).

## <a name="core-sql-api"></a>API Core (SQL)

|**Ejemplo**|**Descripción**|
|---|---|
|[Creación de una cuenta, una base de datos y un contenedor de Azure Cosmos con rendimiento de escalabilidad automática](manage-with-bicep.md#create-autoscale) | Crea una cuenta de API Core (SQL) en dos regiones, una base de datos y un contenedor con rendimiento de escalabilidad automática. |
|[Creación de una cuenta, una base de datos y un contenedor de Azure Cosmos con un almacén analítico](manage-with-bicep.md#create-analytical-store) | Crea una cuenta de Core (SQL) API en una región con un contenedor configurado con TTL analítico habilitado y una opción para usar el rendimiento manual o de escalado automático. |
|[Creación de una cuenta, una base de datos y un contenedor de Azure Cosmos con rendimiento estándar (manual)](manage-with-bicep.md#create-manual) | Crea una cuenta de API Core (SQL) en dos regiones, una base de datos y un contenedor con rendimiento estándar. |
|[Creación de una cuenta, una base de datos y un contenedor de Azure Cosmos con un procedimiento almacenado, un desencadenador y una UDF](manage-with-bicep.md#create-sproc) | Crea una cuenta de API Core (SQL) en dos regiones con un procedimiento almacenado, un desencadenador y una UDF para un contenedor. |
|[Creación de una cuenta de Azure Cosmos con una identidad de Azure AD, definiciones de roles y asignación de roles](manage-with-bicep.md#create-rbac) | Crea una cuenta de API Core (SQL) con una identidad de Azure AD, definiciones de roles y asignación de roles en una entidad de servicio. |
|[Creación de una cuenta de Azure Cosmos gratuita](manage-with-bicep.md#free-tier) |  Crea una cuenta de API Core (SQL) de Azure Cosmos DB gratuita. |

## <a name="next-steps"></a>Pasos siguientes

¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de bases de datos existente.

* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md).
* Si conoce las velocidades de solicitud típicas de la carga de trabajo de base de datos actual, lea sobre el [cálculo de las unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
