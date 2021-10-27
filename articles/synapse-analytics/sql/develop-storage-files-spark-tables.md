---
title: Sincronización de Apache Spark para las definiciones de tablas externas en un grupo de SQL sin servidor
description: Información general sobre cómo consultar tablas de Spark mediante un grupo de SQL sin servidor
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 10/05/2021
author: maburd
ms.author: maburd
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: d26dfd80e6faf0292cea35c82bc2ec529dc481ab
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996007"
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

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Asignación de tipos de datos de Apache Spark a tipos de datos de SQL

Para más información sobre cómo asignar tipos de datos de Apache Spark a tipos de datos de SQL, consulte [Tablas de metadatos compartidos de Azure Synapse Analytics](../metadata/table.md).

## <a name="next-steps"></a>Pasos siguientes

Vaya al artículo [Control de acceso a almacenamiento ](develop-storage-files-storage-access-control.md) para más información sobre el control de acceso al almacenamiento.
