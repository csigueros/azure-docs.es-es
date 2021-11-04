---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 73cd54f16e29564fda5f9e41d0469d59ea99526d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478097"
---
|**Propiedad** | **Descripción de la propiedad**|
|---|---|
| `rawSizeBytes` | Tamaño de los datos sin formato (sin comprimir). En Avro/ORC/Parquet, es el tamaño antes de que se aplique la compresión específica del formato. Proporcione el tamaño de datos original; para ello, establezca esta propiedad en el tamaño de los datos sin comprimir expresado en bytes.|
| `kustoTable` |  Nombre de la tabla de destino existente. Invalida el valor de `Table` establecido en la hoja `Data Connection`. |
| `kustoDataFormat` |  Formato de datos. Invalida el valor de `Data format` establecido en la hoja `Data Connection`. |
| `kustoIngestionMappingReference` |  Nombre de la asignación de ingesta existente que se va a usar. Invalida el valor de `Column mapping` establecido en la hoja `Data Connection`.|
| `kustoIgnoreFirstRecord` | Si se establece en `true`, Kusto omite la primera fila del blob. Úselo en datos con formato tabular (CSV, TSV o similar) para omitir los encabezados. |
| `kustoExtentTags` | Cadena que representa [etiquetas](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context) que se adjuntarán a la extensión resultante. |
| `kustoCreationTime` |  Invalida [$IngestionTime](/azure/data-explorer/kusto/query/ingestiontimefunction?context=/azure/synapse-analytics/context/context?pivots=azuredataexplorer) para el blob, con el formato de una cadena ISO 8601. Se usa para la reposición. |
