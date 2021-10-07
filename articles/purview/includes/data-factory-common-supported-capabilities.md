---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 8c4baccdbfb0f6162e666d6901e0080202a3b7e9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212352"
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
| Azure Files | Sí | 
| Azure SQL Database \* | Sí | 
| Azure SQL Managed Instance \* | Sí | 
| Azure Synapse Analytics \* | Sí | 
| Azure Table Storage | Sí |
| Amazon S3 | Sí | 
| Hive \* | Sí | 
| Oracle \* | Sí |
| Tabla de SAP *(cuando se conecta con SAP ECC o SAP S/4HANA)* | Sí |
| SQL Server \* | Sí | 
| Teradata \* | Sí |

*\* Actualmente, Azure Purview no admite la consulta ni el procedimiento almacenado para el linaje o el examen. El linaje se limita a los orígenes de tabla y vista.*

Si usa el entorno de ejecución de integración autohospedado, tenga en cuenta la versión mínima con compatibilidad con linaje para:
- Copiar datos desde Oracle: versión 5.10 o posterior
- Copiar datos en Azure Synapse Analytics mediante el comando COPY o PolyBase: versión 5.10 o posterior

#### <a name="limitations-on-copy-activity-lineage"></a>Limitaciones en el linaje de la actividad de copia

Actualmente, si usa las siguientes características de la actividad de copia, el linaje no se admite todavía:

- Copia de datos en Azure Data Lake Storage Gen1 mediante el formato binario.
- Configuración de compresión para archivos binarios, de texto delimitado, de Excel, JSON y XML.
- Opciones de partición de origen para Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server y la tabla de SAP.
- Copia de datos en un receptor basado en archivos con la configuración de número máximo de filas por archivo.

Además del linaje, se indica el esquema de recursos de datos (que se muestra en la pestaña Recurso -> Esquema) para los siguientes conectores:

- Archivos CSV y Parquet en Azure Blob, Azure Files, ADLS Gen1, ADLS Gen2 y Amazon S3
- Azure Data Explorer, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata

### <a name="data-flow-support"></a>Compatibilidad de Data Flow

| Almacén de datos | Compatible |
| ------------------- | ------------------- | 
| Azure Blob Storage | Sí |
| Azure Cosmos DB (SQL API) \* | Sí | 
| Azure Data Lake Storage Gen1 | Sí |
| Azure Data Lake Storage Gen2 | Sí |
| Azure Database for MySQL \* | Sí | 
| Azure Database for PostgreSQL \* | Sí |
| Azure SQL Database \* | Sí |
| Azure SQL Managed Instance \* | Sí | 
| Azure Synapse Analytics \* | Sí |

*\* Actualmente, Azure Purview no admite la consulta ni el procedimiento almacenado para el linaje o el examen. El linaje se limita a los orígenes de tabla y vista.*

#### <a name="limitations-on-data-flow-lineage"></a>Limitaciones en el linaje del flujo de datos

Actualmente, el linaje del flujo de datos no se integra con el [conjunto de recursos](../concept-resource-sets.md) de Purview.
