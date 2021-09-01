---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 08/10/2021
ms.openlocfilehash: 20a9b85c2d68e1c9b3c33362f393eaec0e255ce7
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201888"
---
### <a name="copy-activity-support"></a>Compatibilidad de actividad de copia

| Almacén de datos | Compatible | 
| ------------------- | ------------------- | 
| Azure Blob Storage | Sí |
| Azure Cognitive Search | Sí | 
| Azure Cosmos DB (SQL API) \* | Sí | 
| API de Azure Cosmos DB para MongoDB \* | Sí |
| Azure Data Explorer \* | Sí | 
| Azure Data Lake Storage Gen1 | Sí | 
| Azure Data Lake Storage Gen2 | Sí | 
| Azure Database for Maria DB \* | Sí | 
| Azure Database for MySQL \* | Sí | 
| Azure Database for PostgreSQL \* | Sí |
| Azure File Storage | Sí | 
| Azure SQL Database \* | Sí | 
| Azure SQL Managed Instance \* | Sí | 
| Azure Synapse Analytics \* | Sí | 
| Azure Table Storage | Sí |
| Amazon S3 | Sí | 
| Hive \* | Sí | 
| Tabla de SAP *(cuando se conecta con SAP ECC o SAP S/4HANA)* | Sí |
| SQL Server \* | Sí | 
| Teradata \* | Sí |

*\* Actualmente, Azure Purview no admite la consulta ni el procedimiento almacenado para el linaje o el examen. El linaje se limita a los orígenes de tabla y vista.*

> [!Note]
> La característica de linaje tiene cierta sobrecarga de rendimiento en la actividad de copia. Aquellos que configuren las conexiones en Purview pueden observar que determinados trabajos de copia tardan más tiempo en completarse. Pero, en su mayor parte, el impacto es o ninguno o insignificante. Póngase en contacto con soporte técnico y proporcione la comparación de tiempo si los trabajos de copia tardan mucho más tiempo del habitual en terminar.

#### <a name="known-limitations-on-copy-activity-lineage"></a>Limitaciones conocidas en el linaje de la actividad de copia

Actualmente, si usa las siguientes características de la actividad de copia, el linaje no se admite todavía:

- Copia de datos en Azure Data Lake Storage Gen1 mediante el formato binario.
- Copia de datos en Azure Synapse Analytics mediante PolyBase o la instrucción COPY.
- Configuración de compresión para archivos binarios, de texto delimitado, de Excel, JSON y XML.
- Opciones de partición de origen para Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server y la tabla de SAP.
- Opción de detección de partición de origen para almacenes basados en archivos.
- Copia de datos en un receptor basado en archivos con la configuración de número máximo de filas por archivo.
- Adición de más columnas durante la copia.

Además del linaje, se indica el esquema de recursos de datos (que se muestra en la pestaña Recurso -> Esquema) para los siguientes conectores:

- Archivos CSV y Parquet en Azure Blob, Azure File Storage, ADLS Gen1, ADLS Gen2 y Amazon S3
- Azure Data Explorer, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata
