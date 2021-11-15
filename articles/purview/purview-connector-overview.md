---
title: Introducción a los conectores de Purview
description: En este artículo se describen los diferentes almacenes de datos y funcionalidades admitidos en Purview
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8e7ec847de0498d91e0f294a3a60eab3a4018d48
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853650"
---
# <a name="supported-data-stores"></a>Almacenes de datos compatibles

Purview admite los siguientes almacenes de datos. Seleccione cada almacén de datos para obtener información detallada sobre las funcionalidades admitidas y las configuraciones correspondientes.

## <a name="purview-data-sources"></a>Orígenes de datos de Purview

|**Categoría**|  **Almacén de datos**  |**Metadatos técnicos** |**Clasificación** | **Directiva de acceso** |**Lineage** |
|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [Sí](register-scan-azure-blob-storage-source.md#register) | [Sí](register-scan-azure-blob-storage-source.md#scan)| Sí | Limitado**|
||    [Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [Sí](register-scan-azure-cosmos-database.md#register) | [Sí](register-scan-azure-cosmos-database.md#scan)|No|No**|
||    [Azure Data Explorer](register-scan-azure-data-explorer.md)| [Sí](register-scan-azure-data-explorer.md#register) | [Sí](register-scan-azure-data-explorer.md#scan)| No | No** |
||    [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [Sí](register-scan-adls-gen1.md#register) | [Sí](register-scan-adls-gen1.md#scan)| No | Limitado** |
||    [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [Sí](register-scan-adls-gen2.md#register) | [Sí](register-scan-adls-gen2.md#scan)| Sí | Limitado** |
||    [Grupo de SQL dedicado de Azure (antes denominado SQL DW)](register-scan-azure-synapse-analytics.md)| [Sí](register-scan-azure-synapse-analytics.md#register) | [Sí](register-scan-azure-synapse-analytics.md#scan)| No | No** |
||    [Archivos de Azure](register-scan-azure-files-storage-source.md)|[Sí](register-scan-azure-files-storage-source.md#register) | [Sí](register-scan-azure-files-storage-source.md#scan) |  No | Limitado** |
||    [Azure SQL Database](register-scan-azure-sql-database.md)| [Sí](register-scan-azure-sql-database.md#register) |[Sí](register-scan-azure-sql-database.md#scan)| No | No** |
||    [Instancia administrada de Azure SQL Database](register-scan-azure-sql-database-managed-instance.md)|  [Sí](register-scan-azure-sql-database-managed-instance.md#scan) | [Sí](register-scan-azure-sql-database-managed-instance.md#scan) | No | No** |
||    [Azure Synapse Analytics (área de trabajo)](register-scan-synapse-workspace.md)| [Sí](register-scan-synapse-workspace.md#register) | [Sí](register-scan-synapse-workspace.md#scan)| No| [Sí: canalizaciones de Synapse](how-to-lineage-azure-synapse-analytics.md)|
||    [Azure Database for MySQL](register-scan-azure-mysql-database.md)| [Sí](register-scan-azure-mysql-database.md#register) | [Sí](register-scan-azure-mysql-database.md#scan) | No | No** |
||    [Azure Database para PostgreSQL](register-scan-azure-postgresql.md)| [Sí](register-scan-azure-postgresql.md#register) | [Sí](register-scan-azure-postgresql.md#scan) | No | No** |
|Base de datos|    [Cassandra](register-scan-cassandra-source.md)|[Sí](register-scan-cassandra-source.md#register) | No | No| [Sí](how-to-lineage-cassandra.md)|
||    [Google BigQuery](register-scan-google-bigquery-source.md)| [Sí](register-scan-google-bigquery-source.md#register)| No | No| [Sí](how-to-lineage-google-bigquery.md)|
||    [Base de datos de metastore de Hive](register-scan-hive-metastore-source.md)| [Sí](register-scan-hive-metastore-source.md#register) | No | No| Sí** |
||    [Oracle DB](register-scan-oracle-source.md)| [Sí](register-scan-oracle-source.md#register)|  No | No| [Sí**](how-to-lineage-oracle.md)|
||    [SQL Server](register-scan-on-premises-sql-server.md)| [Sí](register-scan-on-premises-sql-server.md#register) |[Sí](register-scan-on-premises-sql-server.md#scan) | No| No** |
||    [Teradata](register-scan-teradata-source.md)| [Sí](register-scan-teradata-source.md#register)|  No | No| [Sí**](how-to-lineage-teradata.md)|
|Archivo|[Amazon S3](register-scan-amazon-s3.md)|[Sí](register-scan-amazon-s3.md)| [Sí](register-scan-amazon-s3.md)| No| Limitado** |
|Servicios y aplicaciones|    [Erwin](register-scan-erwin-source.md)| [Sí](register-scan-erwin-source.md#register)| No | No| [Sí](how-to-lineage-erwin.md)|
||    [Looker](register-scan-looker-source.md)| [Sí](register-scan-looker-source.md#register)| No | No| [Sí](how-to-lineage-looker.md)|
||    [Power BI](register-scan-power-bi-tenant.md)| [Sí](register-scan-power-bi-tenant.md#register)| No | No| [Sí](how-to-lineage-powerbi.md)|
||    [SAP ECC](register-scan-sapecc-source.md)| [Sí](register-scan-sapecc-source.md#register)| No | No| [Sí**](how-to-lineage-sapecc.md)|
||    [SAP S4HANA](register-scan-saps4hana-source.md)| [Sí](register-scan-saps4hana-source.md#register)| No | No| [Sí**](how-to-lineage-sapecc.md)|

\* Purview se basa en metadatos UPDATE_TIME de Azure Database for MySQL para los exámenes incrementales. En algunos casos, es posible que este campo no se conserve en la base de datos y se realice un examen completo. Para más información, vea [la tabla INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) para MySQL. 

\** Se admite el linaje si el conjunto de datos se usa como origen o receptor en la [actividad de copia de Data Factory](how-to-link-azure-data-factory.md). 

> [!NOTE]
> Actualmente, Purview no puede examinar un recurso que tenga `/`, `\` o `#` en el nombre. Para determinar el ámbito del examen y evitar examinar recursos que tengan esos caracteres en el nombre, use el ejemplo de [Registro y examen de una instancia de Azure SQL Database](register-scan-azure-sql-database.md#creating-the-scan).

\* Purview se basa en metadatos UPDATE_TIME de Azure Database for MySQL para los exámenes incrementales. En algunos casos, es posible que este campo no se conserve en la base de datos y se realice un examen completo. Para más información, vea [la tabla INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) para MySQL. 

> [!NOTE]
> Actualmente, Purview no puede examinar un recurso que tenga `/`, `\` o `#` en el nombre. Para determinar el ámbito del examen y evitar examinar recursos que tengan esos caracteres en el nombre, use el ejemplo de [Registro y examen de una instancia de Azure SQL Database](register-scan-azure-sql-database.md#creating-the-scan).

## <a name="scan-regions"></a>Regiones de la exploración
A continuación se muestra una lista de todas las regiones de origen de datos de Azure (centro de datos) donde se ejecuta el explorador de Purview. Si el origen de datos de Azure se encuentra en una región que no está en esta lista, el explorador se ejecutará en la región de la instancia de Purview.
 
### <a name="purview-scanner-regions"></a>Regiones del explorador de Purview

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- Oeste de Europa
- Norte de Europa
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral
- KoreaCentral
- CentralUS
- NorthCentralUS
- EastAsia
- WestCentralUS
- AustraliaSoutheast

## <a name="next-steps"></a>Pasos siguientes

- [Registro y examen del origen de Azure Blob Storage](register-scan-azure-blob-storage-source.md)
