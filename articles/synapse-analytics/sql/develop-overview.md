---
title: Recursos para desarrollar características de SQL de Synapse
description: Conceptos de desarrollo, decisiones de diseño, recomendaciones y técnicas de codificación para SQL de Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 17df008ccc5fcada0ad58313622fc997ccfa3f77
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860521"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decisiones de diseño y técnicas de codificación para características de SQL de Synapse en Azure Synapse Analytics
En este artículo encontrará una lista de recursos para las funciones del grupo de SQL dedicado y sin servidor de Synapse SQL. Los artículos recomendados se dividen en dos secciones: Decisiones de diseño clave y técnicas de codificación y desarrollo.

El objetivo de estos artículos es ayudarle a desarrollar el enfoque técnico óptimo para los componentes de Synapse SQL en Azure Synapse Analytics.

## <a name="key-design-decisions"></a>Decisiones de diseño clave
En los artículos siguientes se resaltan los conceptos y las decisiones de diseño para el desarrollo de SQL de Synapse:

| Artículo | Grupo de SQL dedicado | Grupo de SQL sin servidor |
| ------- | -------- | ------------- |
| [Conexiones](connect-overview.md)                    | Sí | Sí |
| [Clases de recursos y simultaneidad](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context) | Sí    | No |
| [Transactions](develop-transactions.md)              | Sí | No |
| [Esquemas definidos por el usuario](develop-user-defined-schemas.md) | Sí | Sí |
| [Distribución de tablas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)                 | Sí | No |
| [Índices de tablas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)                           | Sí | No |
| [Particiones de tabla](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)                     | Sí | No |
| [estadísticas](develop-tables-statistics.md)            | Sí | Sí |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)                                             | Sí | No |
| [Tablas externas](develop-tables-external-tables.md) | Sí | Sí |
| [CETAS](develop-tables-cetas.md)                     | Sí | Sí |


## <a name="recommendations"></a>Recomendaciones

A continuación encontrará artículos esenciales que resaltan las técnicas de codificación, las sugerencias y las recomendaciones específicas para el desarrollo:

| Artículo | Grupo de SQL dedicado | Grupo de SQL sin servidor |
| ------- | -------- | ------------- |
| [procedimientos almacenados](develop-stored-procedures.md)  | Sí                | Sí                      |
| [Etiquetas](develop-label.md)                           | Sí                | No                      |
| [Vistas](develop-views.md)                             | Sí                | Sí                     |
| [Tablas temporales](develop-tables-temporary.md)       | Sí                | Sí                     |
| [SQL dinámico](develop-dynamic-sql.md)                 | Sí                | Sí                     |
| [Bucle](develop-loops.md)                         | Sí                | Sí                     |
| [Opciones de Agrupar por](develop-group-by-options.md)       | Sí                | No                      |
| [Asignación de variables](develop-variable-assignment.md) | Sí                | Sí                     |

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información de referencia, consulte las [Instrucciones de T-SQL del grupo de SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?context=/azure/synapse-analytics/context/context).

