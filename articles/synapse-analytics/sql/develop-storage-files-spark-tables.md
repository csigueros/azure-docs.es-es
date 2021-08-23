---
title: Sincronización de Apache Spark para las definiciones de tablas externas en un grupo de SQL sin servidor
description: Información general sobre cómo consultar tablas de Spark mediante un grupo de SQL sin servidor
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c7c2123b84aa6e3da362df9ce47d1e7780e8a856
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441767"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Sincronización de Apache Spark para las definiciones de tablas externas de Azure Synapse en un grupo de SQL sin servidor

El grupo de SQL sin servidor puede sincronizar automáticamente los metadatos desde Apache Spark. Se creará una base de datos de grupo de SQL sin servidor para cada base de datos que exista en los grupos de Apache Spark sin servidor. 

Para cada tabla externa de Spark con formato Parquet o CSV, y ubicada en Azure Storage, se crea una tabla externa en una base de datos del grupo de SQL sin servidor. Gracias a ello, puede apagar los grupos de Spark y seguir consultando las tablas externas de Spark desde el grupo de SQL sin servidor.

Cuando se crean particiones de una en Spark, los archivos del almacenamiento se organizan por carpetas. El grupo de SQL sin servidor usará los metadatos de la partición y solo se dirigirá a los archivos y carpetas pertinentes para la consulta.

La sincronización de metadatos se configura automáticamente para cada grupo de Apache Spark sin servidor aprovisionado en el área de trabajo de Azure Synapse. Puede empezar a realizar consultas en las tablas externas de Spark al instante.

Cada tabla externa en formato Parquet o CSV de Spark ubicada en Azure Storage se representa con una tabla externa en un esquema dbo que corresponde a una base de datos del grupo de SQL sin servidor. 

En el caso de las consultas de las tablas externas de Spark, ejecute una consulta cuyo destino sea un [spark_table] externo. Antes de ejecutar el ejemplo siguiente, asegúrese de que tiene un [acceso correcto a la cuenta de almacenamiento](develop-storage-files-storage-access-control.md) en la que se encuentran los archivos.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> La adición, eliminación o modificación de comandos de una tabla externa de Spark en una columna no se verá reflejada en la tabla externa en el grupo de SQL sin servidor.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Asignación de tipos de datos de Apache Spark a tipos de datos de SQL

| Tipo de datos de Spark | Tipo de datos de SQL               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| ShortType      | SMALLINT                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | FLOAT                       |
| DecimalType     | Decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)\* (en JSON)\** |
| MapType         | varchar(max)\* (en JSON)\** |
| StructType      | varchar(max)\* (en JSON)\** |

\* La intercalación usada es Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType y StructType se representan como elementos JSON.



## <a name="next-steps"></a>Pasos siguientes

Vaya al artículo [Control de acceso a almacenamiento ](develop-storage-files-storage-access-control.md) para más información sobre el control de acceso al almacenamiento.
