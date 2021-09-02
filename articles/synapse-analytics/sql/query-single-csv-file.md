---
title: Consulta de archivos .csv mediante un grupo de SQL sin servidor
description: En este artículo, aprenderá a consultar archivos .csv únicos con distintos formatos de archivo mediante el grupo de SQL sin servidor.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6744970ec7aadfc4a9cb967c479307b441f4fb1b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453057"
---
# <a name="query-csv-files"></a>Consulta de archivo CSV

En este artículo, aprenderá a consultar un único archivo .csv mediante el grupo de SQL sin servidor en Azure Synapse Analytics. Los archivos CSV pueden tener distintos formatos: 

- Con y sin una fila de encabezado
- Valores delimitados por comas y tabulaciones
- Finales de línea al estilo de Windows y Unix
- Valores entre comillas y sin comillas, y caracteres de escape

A continuación se abordan todas las variaciones anteriores.

## <a name="quickstart-example"></a>Ejemplo de inicio rápido

La función `OPENROWSET` permite leer el contenido del archivo CSV al proporcionar la dirección URL al archivo.

### <a name="read-a-csv-file"></a>Leer un archivo CSV

La forma más fácil de ver el contenido del archivo `CSV` es proporcionar la dirección URL del archivo a la función `OPENROWSET` y especificar `FORMAT` del archivo CSV y 2.0 `PARSER_VERSION`. Si el archivo está disponible públicamente o si la identidad de Azure AD puede tener acceso a este archivo, debería poder ver el contenido del archivo mediante la consulta como la que se muestra en el ejemplo siguiente:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

La opción `firstrow` se utiliza para omitir la primera fila del archivo CSV, que representa el encabezado en este caso. Asegúrese de que puede tener acceso a este archivo. Si el archivo está protegido con una clave SAS o una identidad personalizada, necesitaría configurar una [credencial de nivel de servidor para el inicio de sesión de SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Si el archivo .csv contiene caracteres UTF-8, asegúrese de usar una intercalación de base de datos UTF-8 (por ejemplo, `Latin1_General_100_CI_AS_SC_UTF8`).
> Una falta de coincidencia entre la codificación de texto del archivo y la intercalación podría producir errores de conversión inesperados.
> Puede cambiar fácilmente la intercalación predeterminada de la base de datos actual mediante la siguiente instrucción T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`.

### <a name="data-source-usage"></a>Uso del origen de datos

En el ejemplo anterior se usa la ruta de acceso completa al archivo. Como alternativa, puede crear un origen de datos externo con la ubicación que apunta a la carpeta raíz del almacenamiento:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Una vez creado el origen de datos, puede usar ese origen de datos y la ruta de acceso relativa al archivo en la función `OPENROWSET`:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Si un origen de datos está protegido con una clave SAS o una identidad personalizada, puede configurar el [origen de datos con una credencial de ámbito de base de datos](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Especificación explícita del esquema

`OPENROWSET` permite especificar explícitamente qué columnas desea leer del archivo con la cláusula `WITH`:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

Los números posteriores a un tipo de datos de la cláusula `WITH` representan el índice de la columna en el archivo CSV.

> [!IMPORTANT]
> Si el archivo CSV contiene caracteres UTF-8, asegúrese de especificar explícitamente alguna intercalación UTF-8 (por ejemplo, `Latin1_General_100_CI_AS_SC_UTF8`) para todas las columnas de la cláusula `WITH` o establezca alguna intercalación UTF-8 en el nivel de base de datos.
> La falta de coincidencia entre la codificación de texto del archivo y la intercalación podría producir errores de conversión inesperados.
> Puede cambiar fácilmente la intercalación predeterminada de la base de datos actual mediante la siguiente instrucción T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`.
> Puede establecer fácilmente la intercalación en los tipos de columnas mediante la siguiente definición: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`.

En las secciones siguientes, puede ver cómo consultar varios tipos de archivos CSV.

## <a name="prerequisites"></a>Requisitos previos

El primer paso es **crear la base de datos** en la que se crearán las tablas. Luego, se inicializan los objetos, para lo que hay que ejecutar un [script de instalación](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) en esa base de datos. Este script de instalación creará los orígenes de datos, las credenciales con ámbito de base de datos y los formatos de archivo externos que se usan en estos ejemplos.

## <a name="windows-style-new-line"></a>Nueva línea al estilo de Windows

La consulta siguiente muestra cómo leer un archivo CSV sin una fila de encabezado, con una nueva línea al estilo de Windows y columnas delimitadas por comas.

Vista previa del archivo:

![Primeras 10 filas del archivo CSV sin encabezado, nueva línea al estilo de Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nueva línea al estilo de Unix

La consulta siguiente muestra cómo leer un archivo sin una fila de encabezado, con una nueva línea al estilo de Unix y columnas delimitadas por comas. Tenga en cuenta la ubicación diferente del archivo en comparación con los demás ejemplos.

Vista previa del archivo:

![Primeras 10 filas del archivo CSV sin fila de encabezado y con nueva línea al estilo de Unix.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Fila de encabezado

La consulta siguiente muestra cómo leer un archivo con una fila de encabezado, con una nueva línea al estilo de Unix y columnas delimitadas por comas. Tenga en cuenta la ubicación diferente del archivo en comparación con los demás ejemplos.

Vista previa del archivo:

![Primeras 10 filas del archivo CSV con una fila de encabezado y con nueva línea al estilo de Unix.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
    BULK 'csv/population-unix-hdr/population.csv',
    DATA_SOURCE = 'SqlOnDemandDemo',
    FORMAT = 'CSV', PARSER_VERSION = '2.0',
    FIELDTERMINATOR =',',
    HEADER_ROW = TRUE
    ) AS [r]
```

La opción `HEADER_ROW = TRUE` provocará que se lean los nombres de columna de la fila de encabezado del archivo. Es excelente para fines de exploración cuando no está familiarizado con el contenido del archivo. Para obtener el máximo rendimiento, [consulte la ](best-practices-serverless-sql-pool.md#use-appropriate-data-types)sección de uso de los tipos de datos apropiados en los procedimientos recomendados. Además, puede leer más sobre la [sintaxis de OPENROWSET aquí](develop-openrowset.md#syntax).

## <a name="custom-quote-character"></a>Carácter de comillas personalizado

La consulta siguiente muestra cómo leer un archivo con una fila de encabezado, con una nueva línea al estilo de Unix, columnas delimitadas por comas y valores entre comillas. Tenga en cuenta la ubicación diferente del archivo en comparación con los demás ejemplos.

Vista previa del archivo:

![Primeras 10 filas del archivo CSV con una fila de encabezado, con nueva línea al estilo de Unix y valores entre comillas.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Esta consulta devolvería los mismos resultados si se omitiese el parámetro FIELDQUOTE, ya que el valor predeterminado de FIELDQUOTE es una comilla doble.

## <a name="escape-characters"></a>Carácter de escape

La consulta siguiente muestra cómo leer un archivo con una fila de encabezado, con una nueva línea al estilo de Unix, columnas delimitadas por comas y un carácter de escape usado para el delimitador de campos (coma) sin valores. Tenga en cuenta la ubicación diferente del archivo en comparación con los demás ejemplos.

Vista previa del archivo:

![Primeras 10 filas del archivo CSV con una fila de encabezado, con nueva línea al estilo de Unix y un carácter de escape usado para el delimitador de campos.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Esta consulta produciría un error si no se especificara ESCAPECHAR, ya que la coma en "Slov,enia" se trataría como delimitador de campos en lugar de como parte del nombre del país o región. "Slov,enia" se trataría como dos columnas. Por lo tanto, la fila en particular tendría una columna más que las demás filas y una columna más de la definida en la cláusula WITH.

### <a name="escape-quoting-characters"></a>Caracteres de comillas de escape

La consulta siguiente muestra cómo leer un archivo con una fila de encabezado, con una nueva línea al estilo de Unix, columnas delimitadas por comas y un carácter de comillas dobles de escape entre valores. Tenga en cuenta la ubicación diferente del archivo en comparación con los demás ejemplos.

Vista previa del archivo:

![La consulta siguiente muestra cómo leer un archivo con una fila de encabezado, con una nueva línea al estilo de Unix, columnas delimitadas por comas y un carácter de comillas dobles de escape entre valores.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> El carácter de comillas se debe escapar con otro carácter de comillas. El carácter de comillas solo puede aparecer dentro del valor de la columna si el valor se encapsula entre caracteres de comillas.

## <a name="tab-delimited-files"></a>Archivos delimitados por tabulaciones

La consulta siguiente muestra cómo leer un archivo con una fila de encabezado, con una nueva línea al estilo de Unix y columnas delimitadas por tabulaciones. Tenga en cuenta la ubicación diferente del archivo en comparación con los demás ejemplos.

Vista previa del archivo:

![Primeras 10 filas del archivo CSV con una fila de encabezado, con nueva línea al estilo de Unix y delimitador de tabulaciones.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Devolver un subconjunto de columnas

Hasta ahora, ha especificado el esquema de archivos CSV mediante WITH y enumerando todas las columnas. Solo puede especificar las columnas que realmente necesita en la consulta mediante un número ordinal para cada columna necesaria. También omitirá las columnas que no sean de interés.

La consulta siguiente devuelve el número de nombres de país distintos incluidos en un archivo, especificando solo las columnas necesarias:

> [!NOTE]
> Eche un vistazo a la cláusula WITH en la consulta siguiente y observe que hay "2" (sin comillas) al final de la fila en la que se define la columna *[country_name]* . Significa que la columna *[country_name]* es la segunda columna del archivo. La consulta omitirá todas las columnas del archivo excepto la segunda.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Pasos siguientes

En los artículos siguientes obtendrá información sobre:

- [Consulta de archivos de Parquet](query-parquet-files.md)
- [Consulta de carpetas y varios archivos](query-folders-multiple-csv-files.md)
