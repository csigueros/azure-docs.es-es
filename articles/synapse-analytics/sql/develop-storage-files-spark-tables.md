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
ms.openlocfilehash: 642024d9554b51bc60df90cf3d5a7bdd799440b5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432097"
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

| Tipo de datos de Spark | Tipo de datos de SQL | Comentarios |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | **Spark**: *LongType* representa números enteros con signo de 8 bytes. [Referencia](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |
| `BooleanType`, `boolean`                    | `bit` (Parquet), `varchar(6)` (CSV)  | |
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | **Spark**: *DecimalType* representa números decimales con signo de precisión arbitraria. Con el respaldo interno de java.math.BigDecimal. BigDecimal consta de un valor entero de precisión arbitraria sin escalar y una escala de enteros de 32 bits. <br> **SQL**: números de precisión y escala fijas. Cuando se utiliza la precisión máxima, los valores válidos se sitúan entre - 10^38 +1 y 10^38 - 1. Los sinónimos ISO para decimal son dec y dec(p, s) . numeric es funcionalmente idéntico a decimal. [Referencia](/sql/t-sql/data-types/decimal-and-numeric-transact-sql]) |
| `IntegerType`, `Integer`, `int`             | `int`                 | **Spark**: *IntegerType* representa números enteros con signo de 4 bytes. [Referencia](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | **Spark**: *ByteType* representa números enteros con signo de 1 byte [de -128 a 127] y ShortType representa números enteros con signo de 2 bytes [de -32768 a 32767]. <br> **SQL**: tinyint representa números enteros con signo de 1 byte [de 0 a 255] y smallint representa números enteros con signo de 2 bytes [de -32768 a 32767]. [Referencia](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ShortType`, `Short`, `smallint`            | `smallint`            | Igual que el anterior. |
| `DoubleType`, `Double`                      | `float`               | **Spark**: *DoubleType* representa números de punto flotante de doble precisión de 8 bytes. Para **SQL**, [visite esta página](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `FloatType`, `float`, `real`                | `real`                | **Spark**: *FloatType* representa números de punto flotante de doble precisión de 4 bytes. Para **SQL**, [visite esta página](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `DateType`, `date`                          | `date`                | **Spark**: *DateType* representa valores de campos de año, mes y día, sin una zona horaria.|
| `TimestampType`, `timestamp`                | `datetime2`           | **Spark**: *TimestampType* representa valores de campos de año, mes, día, hora, minuto y segundo, con la zona horaria local de la sesión. El valor de marca de tiempo representa un punto absoluto en el tiempo.
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | **Spark**: *StringType* representa valores de una cadena de caracteres. *VarcharType(n)* es una variante de StringType que tiene una limitación de longitud. Si la cadena de entrada supera la limitación de longitud, se producirá un error en la escritura de datos. Este tipo solo se puede usar en el esquema de tabla, no en funciones ni operadores.<br> *CharType(n)* es una variante de *VarcharType(n)* que tiene una longitud fija. La lectura de una columna de tipo *CharType(n)* siempre devuelve valores de cadena de longitud n. Al comparar columnas de tipo Char, la corta se rellenará hasta la longitud de la larga. <br> **SQL**: en *Varchar(n)* , n puede ser 8000 como máximo; si es una columna con particiones, n puede ser 2048 como máximo. <br> Úselo con la intercalación `Latin1_General_100_BIN2_UTF8`. |
| `BinaryType`, `binary`                      | `varbinary(n)`        | **SQL**: en *Varbinary(n)* , n puede ser 8000 como máximo; si es una columna con particiones, n puede ser 2048 como máximo. |
| `array`, `map`, `struct`                    | `varchar(max)`        | **SQL**: serializa en JSON con intercalación `Latin1_General_100_BIN2_UTF8`. |

\* La intercalación de nivel de base de datos es Latin1_General_100_CI_AS_SC_UTF8 \* La intercalación de nivel de columna de cadena es Latin1_General_100_BIN2_UTF8

\** ArrayType, MapType y StructType se representan como elementos JSON.

## <a name="next-steps"></a>Pasos siguientes

Vaya al artículo [Control de acceso a almacenamiento ](develop-storage-files-storage-access-control.md) para más información sobre el control de acceso al almacenamiento.
