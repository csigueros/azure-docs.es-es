---
title: Tablas de metadatos compartidos
description: Azure Synapse Analytics proporciona un modelo de metadatos compartido en el que la creación de una tabla en el grupo de Apache Spark sin servidor hará que sea accesible desde el grupo de SQL sin servidor y el grupo de SQL dedicado sin duplicar los datos.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 9852f146651ca6bcb5c1935ca78fce61bca5093f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433321"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Tablas de metadatos compartidos de Azure Synapse Analytics


Azure Synapse Analytics permite que los diferentes motores de cálculo de áreas de trabajo compartan bases de datos y tablas entre sus grupos de Apache Spark y el grupo de SQL sin servidor.

Una vez creada una base de datos con un trabajo de Spark, puede crear en ella, mediante Spark, tablas que usen Parquet o CSV como formato de almacenamiento. Los nombres de tabla se convertirán a minúsculas y se deben consultar con el nombre en minúscula. Estas tablas estarán disponibles de forma inmediata para que cualquiera de los grupos de Spark del área de trabajo de Azure Synapse realice consultas en ellas. También se pueden usar desde cualquiera de los trabajos de Spark sujetos a permisos.

Las tablas creadas, administradas y externas de Spark también están disponibles como tablas externas con el mismo nombre en la base de datos sincronizada correspondiente en el grupo de SQL sin servidor. [La exposición de una tabla de Spark en SQL](#expose-a-spark-table-in-sql) proporciona más información sobre la sincronización de la tabla.

Dado que las tablas se sincronizan con el grupo de SQL sin servidor de forma asincrónica, se producirá un pequeño retraso hasta que aparezcan.

## <a name="manage-a-spark-created-table"></a>Administración de una tabla creada con Spark

Use Spark para administrar las bases de datos creadas con Spark. Por ejemplo, elimínela mediante un trabajo del grupo de Apache Spark sin servidor y cree tablas en ella desde Spark.

Los objetos de las bases de datos sincronizadas no se pueden modificar desde un grupo de SQL sin servidor.

## <a name="expose-a-spark-table-in-sql"></a>Exposición de una tabla de Spark en SQL

### <a name="shared-spark-tables"></a>Tablas de Spark compartidas

Spark proporciona dos tipos de tablas que Azure Synapse expone en SQL automáticamente:

- Tablas administradas

  Spark proporciona muchas opciones para almacenar datos en tablas administradas, como TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA y LIBSVM. Normalmente, estos archivos se almacenan en el directorio `warehouse` en donde se guardan los datos de las tablas administradas.

- Tablas externas

  Spark también proporciona maneras de crear tablas externas basadas en datos existentes, ya sea proporcionando la opción `LOCATION` o usando el formato de Hive. Estas tablas externas se pueden realizar con diferentes formatos de datos, incluido Parquet.

En la actualidad, Azure Synapse solo comparte tablas de Spark administradas y externas que almacenan sus datos en los formatos Parquet o CSV con los motores SQL. Las tablas que se basan en otros formatos no se sincronizan automáticamente. Es posible que pueda sincronizar estas tablas de forma explícita como una tabla externa en su propia base de datos SQL, siempre que el motor de SQL admita el formato subyacente de la tabla.

### <a name="share-spark-tables"></a>Uso compartido de tablas de Spark

Las tablas de Spark administradas y externas que se pueden compartir se exponen en el motor SQL como tablas externas con las siguientes propiedades:

- El origen de datos de la tabla externa de SQL es el origen de datos que representa la carpeta de ubicación de la tabla de Spark.
- El formato de archivo de la tabla externa de SQL es Parquet.
- La credencial de acceso de la tabla externa de SQL es de paso a través.

Dado que todos los nombres de tabla de Spark son nombres válidos para una tabla SQL y los nombres de columna de Spark también son nombres válidos para una columna SQL, los nombres de columna y tabla de Spark se usarán para la tabla externa de SQL.

Las tablas de Spark proporcionan tipos de datos diferentes a los motores de Synapse SQL. En la tabla siguiente se asignan los tipos de datos de la tabla Spark a los tipos SQL:

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

## <a name="security-model"></a>Modelo de seguridad

Las bases de datos y las tablas de Spark, al igual que sus representaciones sincronizadas en el motores SQL, estarán protegidas en el nivel de almacenamiento subyacente. Teniendo en cuenta que actualmente no tienen permisos para los propios objetos, estos se pueden visualizar en el explorador de objetos.

La entidad de seguridad que crea una tabla administrada se considera el propietario de dicha tabla y tiene todos los derechos en la tabla, así como en las carpetas y los archivos subyacentes. Además, el propietario de la base de datos se convertirá automáticamente en copropietario de la tabla.

Si crea una tabla externa de Spark o SQL con la autenticación de paso a través, los datos solo se protegen en los niveles de carpeta y archivo. Si alguien consulta este tipo de tabla externa, la identidad de seguridad del remitente de la consulta se pasa al sistema de archivos, que comprobará los derechos de acceso.

Para más información sobre cómo establecer permisos en las carpetas y los archivos, consulte [Base de datos compartida de Azure Synapse Analytics](database.md).

## <a name="examples"></a>Ejemplos

### <a name="create-a-managed-table-in-spark-and-query-from-serverless-sql-pool"></a>Creación de una tabla administrada en Spark y realización de consultas desde el grupo de SQL sin servidor

En este escenario, tiene una base de datos de Spark denominada `mytestdb`. Consulte [Creación y conexión a una base de datos de Spark con un grupo de SQL sin servidor](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Cree una tabla administrada de Spark con SparkSQL mediante la ejecución del siguiente comando:

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

Este comando crea la tabla `myparquettable` en la base de datos `mytestdb`. Los nombres de tabla se convertirán a minúsculas. Después de un breve retraso, puede ver la tabla del grupo de SQL sin servidor. Por ejemplo, ejecute la siguiente instrucción desde el grupo de SQL sin servidor.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Compruebe que `myparquettable` se incluye en los resultados.

>[!NOTE]
>Una tabla que no use Parquet o CSV como formato de almacenamiento no se sincronizará.

A continuación, inserte algunos valores en la tabla de Spark, por ejemplo, con las siguientes instrucciones C# de Spark en un cuaderno C#:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

Ahora puede leer los datos desde el grupo de SQL sin servidor de la siguiente manera:

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

Debe obtener la siguiente fila como resultado:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-in-spark-and-query-from-serverless-sql-pool"></a>Creación de una tabla externa en Spark y realización de consultas desde el grupo de SQL sin servidor

En este ejemplo, cree una tabla de Spark externa con los archivos de datos de Parquet que creó en el ejemplo anterior para la tabla administrada.

Por ejemplo, con SparkSQL ejecute:

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Reemplace el marcador de posición `<storage-name>` por el nombre de la cuenta de almacenamiento de ADLS gen2 que está usando, `<fs>` por el nombre del sistema de archivos que está usando y `<synapse_ws>` por el nombre del área de trabajo de Synapse que está usando para ejecutar este ejemplo.

En el ejemplo anterior se crea la tabla `myextneralparquettable` en la base de datos `mytestdb`. Después de un breve retraso, puede ver la tabla del grupo de SQL sin servidor. Por ejemplo, ejecute la siguiente instrucción desde el grupo de SQL sin servidor.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Compruebe que `myexternalparquettable` se incluye en los resultados.

Ahora puede leer los datos desde el grupo de SQL sin servidor de la siguiente manera:

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

Debe obtener la siguiente fila como resultado:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre los metadatos compartidos de Azure Synapse Analytics](overview.md)
- [Más información sobre las bases de datos de metadatos compartidos de Azure Synapse Analytics](database.md)


