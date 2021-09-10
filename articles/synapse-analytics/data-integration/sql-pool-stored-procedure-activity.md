---
title: Transformación de datos mediante la actividad de procedimiento almacenado de grupo de SQL
description: Explica cómo usar la actividad de procedimiento almacenado de grupo de SQL para invocar un procedimiento almacenado en Azure Synapse Analytics.
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: pipeline
author: linda33wj
ms.author: jingwang
ms.reviewer: jrasnick
ms.date: 05/13/2021
ms.openlocfilehash: 4c1f12d4e61f254f753f3feb59ca46997f88b751
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257072"
---
# <a name="transform-data-by-using-sql-pool-stored-procedure-activity-in-azure-synapse-analytics"></a>Transformación de datos mediante la actividad de procedimiento almacenado de grupo de SQL en Azure Synapse Analytics

[!INCLUDE[appliesto-xxx-asa-md](../../data-factory/includes/appliesto-xxx-asa-md.md)]

Las actividades de transformación en una [canalización](../../data-factory/concepts-pipelines-activities.md) transforman y procesan sus datos sin procesar para convertirlos en predicciones y perspectivas. Este artículo se basa en el artículo sobre [transformación de datos](../../data-factory/transform-data.md), que presenta información general de la transformación de datos y las actividades de transformación admitidas.

En Azure Synapse Analytics, puede usar la actividad de procedimiento almacenado de grupo de SQL para invocar un procedimiento almacenado en un grupo de SQL dedicado.

## <a name="syntax-details"></a>Detalles de la sintaxis

En la actividad de procedimiento almacenado de grupo de SQL, se admite la siguiente configuración:

| Propiedad                  | Descripción                              | Obligatorio |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nombre de la actividad                     | Sí      |
| description               | Texto que describe para qué se usa la actividad. | No       |
| type                      | Para la actividad de procedimiento almacenado de grupo de SQL, el tipo de actividad es **SqlPoolStoredProcedure**. | Sí      |
| sqlPool         | Referencia a un [grupo de SQL dedicado](../sql/overview-architecture.md) en el área de Azure Synapse actual. | Sí      |
| storedProcedureName       | Especifique el nombre del procedimiento almacenado que se invocará. | Sí      |
| storedProcedureParameters | Especifique los valores para los parámetros del procedimiento almacenado. Use `"param1": { "value": "param1Value","type":"param1Type" }` para pasar valores de parámetros y su tipo compatible con el origen de datos. Si necesita pasar NULL para un parámetro, use `"param1": { "value": null }` (todo en minúsculas). | No       |

Ejemplo:

```json
{
    "name": "SQLPoolStoredProcedureActivity",
    "description":"Description",
    "type": "SqlPoolStoredProcedure",
    "sqlPool": {
        "referenceName": "DedicatedSQLPool",
        "type": "SqlPoolReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
 
- [Canalización y actividad](../../data-factory/concepts-pipelines-activities.md)
- [Grupo de SQL dedicado](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
