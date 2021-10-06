---
title: 'Inicio rápido: Uso de grupos de SQL sin servidor'
description: En este inicio rápido, verá y aprenderá lo fácil que es consultar varios tipos de archivos mediante un grupo de SQL sin servidor.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8607355069bbae5983239ddbd3e8752143f31497
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403212"
---
# <a name="quickstart-use-serverless-sql-pool"></a>Inicio rápido: Uso de grupos de SQL sin servidor

El grupo de SQL sin servidor de Synapse es un servicio de consulta sin servidor que permite ejecutar consultas SQL en archivos colocados en Azure Storage. En este inicio rápido, aprenderá a consultar varios tipos de archivos mediante un grupo de SQL sin servidor. Los formatos admitidos se enumeran en [OPENROWSET](sql/develop-openrowset.md).

Este inicio rápido muestra cómo realizar consultas en: archivos CSV, Apache Parquet y JSON.

## <a name="prerequisites"></a>Requisitos previos

Elija un cliente SQL para emitir consultas:

- [Azure Synapse Studio](./get-started-create-workspace.md) es una herramienta web que se puede usar para examinar archivos en el almacenamiento y crear consultas SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) es una herramienta de cliente que permite ejecutar consultas SQL y cuadernos en la base de datos a petición.
- [SQL Server Management Studio](sql/get-started-ssms.md) es una herramienta de cliente que permite ejecutar consultas SQL en la base de datos a petición.

Parámetros de este inicio rápido:

| Parámetro                                 | Descripción                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Dirección del punto de conexión de servicio del grupo de SQL sin servidor    | Se usa como nombre de servidor.                                   |
| Región del punto de conexión de servicio del grupo de SQL sin servidor     | Se usará para determinar qué almacenamiento se utilizará en los ejemplos. |
| Nombre de usuario y contraseña para el acceso al punto de conexión. | Se usa para acceder al punto de conexión.                               |
| La base de datos que se utiliza para crear vistas.         | La base de datos que se utiliza como punto de partida en ejemplos.       |

## <a name="first-time-setup"></a>Primera configuración

Antes de usar los ejemplos:

- Crear una base de datos para las vistas (en caso de que quiera usar vistas).
- Crear las credenciales que va a usar el grupo de SQL sin servidor para acceder a los archivos en el almacenamiento.

### <a name="create-database"></a>Crear base de datos

Cree su propia base de datos para fines de demostración. Usará esta base de datos para crear sus vistas y para las consultas de ejemplo de este artículo.

> [!NOTE]
> Las bases de datos se usan solo para los metadatos de la vista, no para los datos reales.
>Anote el nombre de la base de datos que usa para usarlo más adelante en el artículo de inicio rápido.

Use la siguiente consulta, cambiando `mydbname` por un nombre de su elección:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Creación de un origen de datos

Para ejecutar consultas mediante el grupo de SQL sin servidor, cree un origen de datos que el grupo de SQL sin servidor pueda usar para acceder a los archivos del almacenamiento.
Ejecute el siguiente fragmento de código para crear el origen de datos que se usa en los ejemplos de esta sección:

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>Consulta de archivo CSV

La imagen siguiente es una vista previa del archivo que se va a consultar:

![Primeras 10 filas del archivo CSV sin encabezado, nueva línea al estilo de Windows.](./sql/media/query-single-csv-file/population.png)

La consulta siguiente muestra cómo leer un archivo CSV que no contiene una fila de encabezado, con una nueva línea al estilo de Windows y columnas delimitadas por comas:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Puede especificar el esquema en el momento de la compilación de la consulta.
Para obtener más ejemplos, consulte el artículo sobre cómo [consultar un archivo CSV](sql/query-single-csv-file.md).

## <a name="query-parquet-files"></a>Consulta de archivos de Parquet

En el ejemplo siguiente se muestran las funcionalidades de inferencia automática del esquema para los archivos Parquet. Devuelve el número de filas de septiembre de 2017 sin especificar un esquema.

> [!NOTE]
> No es necesario especificar columnas en la cláusula `OPENROWSET WITH` al leer los archivos Parquet. En este caso, el grupo de SQL sin servidor utiliza los metadatos del archivo de Parquet y enlaza las columnas por nombre.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

Obtenga más información sobre cómo [consultar archivos Parquet](sql/query-parquet-files.md).

## <a name="query-json-files"></a>Consulta de archivo JSON

### <a name="json-sample-file"></a>Archivo de ejemplo de JSON

Los archivos se almacenan en un contenedor *json*, carpeta *books* y contienen una entrada con un único libro con la siguiente estructura:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="query-json-files"></a>Consulta de archivo JSON

En la consulta siguiente se muestra cómo usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) para recuperar valores escalares (título, editor) de un libro con el título *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Estamos leyendo todo el archivo JSON como una única fila o columna. Por consiguiente, FIELDTERMINATOR, FIELDQUOTE y ROWTERMINATOR se establecen en 0x0b porque no esperamos encontrarlo en el archivo.

## <a name="next-steps"></a>Pasos siguientes

Ya está preparado para continuar con los siguientes artículos:

- [Consulta de archivos .csv](sql/query-single-csv-file.md)
- [Consulta de carpetas y varios archivos .csv](sql/query-folders-multiple-csv-files.md)
- [Consulta de archivos específicos](sql/query-specific-files.md)
- [Consulta de archivos Parquet](sql/query-parquet-files.md)
- [Consulta de tipos anidados de Parquet](sql/query-parquet-nested-types.md)
- [Consulta de archivos JSON](sql/query-json-files.md)
- [Creación y uso de vistas en SQL a petición (versión preliminar) en Azure Synapse Analytics](sql/create-use-views.md)
- [Creación y uso de tablas externas en SQL a petición (versión preliminar) en Azure Synapse Analytics](sql/create-use-external-tables.md)
- [Almacenamiento de resultados de búsqueda en el almacenamiento mediante SQL a petición (versión preliminar) en Azure Synapse Analytics](sql/create-external-table-as-select.md)
- [Consulta de archivos .csv](sql/query-single-csv-file.md)