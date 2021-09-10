---
title: Características admitidas de Azure Synapse Link para Azure Cosmos DB
description: Descripción de la lista actual de acciones que admite Azure Synapse Link para Azure Cosmos DB
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 06/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 8400a479b45770570c43ec906a192bf4f05a71a0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123110400"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Características admitidas de Azure Synapse Link para Azure Cosmos DB

En este artículo se describen las funcionalidades que se admiten actualmente en Azure Synapse Link para Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Compatibilidad con Azure Synapse

Hay dos tipos de contenedores en Azure Cosmos DB:
* Contenedor de HTAP: un contenedor con Synapse Link habilitado. Este contenedor tiene almacén de transacciones y uno analítico. 
* Contenedor de OLTP: un contenedor con Synapse Link sin habilitar. Este contenedor solo tiene almacén de transacciones y no tiene uno analítico.

Puede conectarse a un contenedor de Azure Cosmos DB sin habilitar Synapse Link. En este escenario, solo se puede leer y escribir en el almacén transaccional. A continuación, se muestra la lista de las características admitidas actualmente en Synapse Link para Azure Cosmos DB. 

| Category              | Descripción |[Grupo de Apache Spark](../sql/on-demand-workspace-overview.md) | [Grupo de SQL sin servidor](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Compatibilidad con el tiempo de ejecución** |Tiempo de ejecución de Azure Synapse compatible para acceder a Azure Cosmos DB| ✓ | ✓ |
| **Compatibilidad con la API de Azure Cosmos DB** | Tipo de Azure Cosmos DB API compatible | SQL/MongoDB | SQL/MongoDB |
| **Object**  |Objetos como una tabla que se puede crear y apunta directamente al contenedor de Azure Cosmos DB| Dataframe, Vista, Tabla | Ver |
| **Lectura**    | Tipo de contenedor de Azure Cosmos DB que se puede leer | OLTP/HTAP | HTAP  |
| **Escritura**   | ¿Se puede usar el tiempo de ejecución de Azure Synapse para escribir datos en un contenedor de Azure Cosmos DB? | Sí | No |

* Si escribe datos en un contenedor de Azure Cosmos DB desde Spark, este proceso se lleva a cabo a través del almacén transaccional de Azure Cosmos DB. Esta operación afectará al rendimiento transaccional de Azure Cosmos DB al consumir unidades de solicitud.
* Actualmente no se admite la integración del grupo de SQL dedicado a través de tablas externas.
 
## <a name="supported-code-generated-actions-for-spark"></a>Acciones generadas por el código compatibles con Spark

| Gesto              | Descripción |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Cargar en DataFrame** |Permite cargar y leer datos en un DataFrame de Spark. |✓| ✓ |
| **Crear una tabla de Spark** |Permite crear una tabla que apunta a un contenedor de Azure Cosmos DB.|✓| ✓ |
| **Escribir un DataFrame en el contenedor** |Permite escribir un DataFrame en el contenedor.|✓| ✓ |
| **Cargar un DataFrame de streaming desde un contenedor** |Permite hacer streaming de datos con la fuente de cambios de Azure Cosmos DB.|✓| ✓ |
| **Escribir un DataFrame de streaming en un contenedor** |Permite hacer streaming de datos con la fuente de cambios de Azure Cosmos DB.|✓| ✓ |

## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Acciones generadas por el código compatibles con el grupo de SQL sin servidor

| Gesto              | Descripción |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Exploración de datos** |Exploración de datos de un contenedor con sintaxis T-SQL familiar e inferencia de esquemas automática|X| ✓ |
| **Creación de vistas y generación de informes de BI** |Creación de una vista SQL a fin de tener acceso directo a un contenedor para BI a través del grupo de SQL sin servidor |X| ✓ |
| **Combinación de orígenes de datos dispares junto con datos de Cosmos DB** | Almacenamiento de los resultados de la consulta que lee datos de contenedores de Cosmos DB junto con datos en Azure Blob Storage o Azure Data Lake Storage mediante CETAS |X| ✓ |

## <a name="next-steps"></a>Pasos siguientes

* Consulte el artículo [Conexión a Synapse Link para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md).
* [Más información sobre cómo realizar consultas en el almacén analítico de Cosmos DB con Spark 3](how-to-query-analytical-store-spark-3.md)
* [Más información sobre cómo realizar consultas en el almacén analítico de Cosmos DB con Spark 2](how-to-query-analytical-store-spark.md)
