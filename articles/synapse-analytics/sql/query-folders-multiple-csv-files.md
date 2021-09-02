---
title: Consulta de carpetas y varios archivos mediante el grupo de SQL sin servidor
description: El grupo de SQL sin servidor admite la lectura de varios archivos o carpetas mediante caracteres comodín, que son similares a los caracteres comodín usados en el sistema operativo Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: a91ca96e69ae5408a3232513eea3ba1443c97064
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253604"
---
# <a name="query-folders-and-multiple-files"></a>Consulta de carpetas y varios archivos  

En este artículo, aprenderá a escribir una consulta mediante un grupo de SQL sin servidor en Azure Synapse Analytics.

El grupo de SQL sin servidor admite la lectura de varios archivos o carpetas mediante caracteres comodín, que son similares a los caracteres comodín usados en el sistema operativo Windows. Sin embargo, existe una mayor flexibilidad, ya que se permiten varios caracteres comodín.

## <a name="prerequisites"></a>Requisitos previos

El primer paso es **crear una base de datos** en la que se ejecutarán las consultas. Luego, se inicializan los objetos, para lo que hay que ejecutar un [script de instalación](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) en esa base de datos. Este script de instalación creará los orígenes de datos, las credenciales con ámbito de base de datos y los formatos de archivo externos que se usan en estos ejemplos.

Usará la carpeta *csv/taxi* para seguir las consultas de ejemplo. Contiene los datos de NYC Taxi - Yellow Taxi Trip Records de julio de 2016 a junio de 2018. Los archivos de *csv/taxi* se denominan según el año y el mes con el siguiente patrón: yellow_tripdata_\<year>-\<month>.csv

## <a name="read-all-files-in-folder"></a>Lectura de todos los archivos de una carpeta

En el ejemplo siguiente se leen todos los archivos de datos de NYC Yellow Taxi de la carpeta *csv/taxi* y se devuelve el número total de viajeros y carreras por año. También muestra el uso de las funciones de agregado.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos los archivos a los que se tiene acceso con la función OPENROWSET única deben tener la misma estructura (es decir, el número de columnas y sus tipos de datos).

### <a name="read-subset-of-files-in-folder"></a>Lectura de subconjunto de archivos en una carpeta

En el ejemplo siguiente se leen los archivos de datos de NYC Yellow Taxi de 2017 de la carpeta *csv/taxi* con un carácter comodín, y se devuelve el importe total de las tarifas por tipo de pago.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Todos los archivos a los que se tiene acceso con la función OPENROWSET única deben tener la misma estructura (es decir, el número de columnas y sus tipos de datos).

## <a name="read-folders"></a>Lectura de carpetas

La ruta de acceso que proporciona a OPENROWSET también puede ser una ruta de acceso a una carpeta. En las siguientes secciones se incluyen estos tipos de consultas.

### <a name="read-all-files-from-specific-folder"></a>Lectura de todos los archivos de una carpeta específica

Puede leer todos los archivos de una carpeta mediante el carácter comodín de nivel de archivo, tal como se muestra en [Lectura de todos los archivos de una carpeta](#read-all-files-in-folder). Sin embargo, hay una manera de consultar una carpeta y consumir todos los archivos de esa carpeta.

Si la ruta de acceso proporcionada en OPENROWSET apunta a una carpeta, se usarán todos los archivos de esa carpeta como origen de la consulta. La consulta siguiente leerá todos los archivos de la carpeta *csv/taxi*.

> [!NOTE]
> Observe la existencia de / al final de la ruta de acceso en la consulta siguiente. Denota una carpeta. Si se omite /, la consulta en su lugar se dirigirá a un archivo denominado *taxi*.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos los archivos a los que se tiene acceso con la función OPENROWSET única deben tener la misma estructura (es decir, el número de columnas y sus tipos de datos).

### <a name="read-all-files-from-multiple-folders"></a>Lectura de todos los archivos de varias carpetas

Es posible leer archivos de varias carpetas mediante un carácter comodín. La siguiente consulta leerá todos los archivos de todas las carpetas ubicadas en la carpeta *csv* que tengan nombres que empiecen con *t* y terminen con *i*.

> [!NOTE]
> Observe la existencia de / al final de la ruta de acceso en la consulta siguiente. Denota una carpeta. Si se omite /, la consulta en su lugar se dirigirá a los archivos denominados *t&ast;i*.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos los archivos a los que se tiene acceso con la función OPENROWSET única deben tener la misma estructura (es decir, el número de columnas y sus tipos de datos).

Puesto que solo tiene una carpeta que coincida con los criterios, el resultado de la consulta es el mismo que [Lectura de todos los archivos de una carpeta](#read-all-files-in-folder).

## <a name="traverse-folders-recursively"></a>Atravesar carpetas de forma recursiva

El grupo de SQL sin servidor puede atravesar carpetas de forma recursiva si se especifica /** al final de la ruta de acceso. La siguiente consulta leerá todos los archivos de todas las carpetas y subcarpetas ubicadas en la carpeta *csv*.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos los archivos a los que se tiene acceso con la función OPENROWSET única deben tener la misma estructura (es decir, el número de columnas y sus tipos de datos).

## <a name="multiple-wildcards"></a>Varios caracteres comodín

Puede usar varios caracteres comodín en diferentes niveles de la ruta de acceso. Por ejemplo, puede enriquecer la consulta anterior para leer archivos solo con datos de 2017, desde todas las carpetas en las que los nombres empiecen por *t* y terminen con *i*.

> [!NOTE]
> Observe la existencia de / al final de la ruta de acceso en la consulta siguiente. Denota una carpeta. Si se omite /, la consulta en su lugar se dirigirá a los archivos denominados *t&ast;i*.
> Hay un límite máximo de 10 caracteres comodín por consulta.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos los archivos a los que se tiene acceso con la función OPENROWSET única deben tener la misma estructura (es decir, el número de columnas y sus tipos de datos).

Puesto que solo tiene una carpeta que coincida con los criterios, el resultado de la consulta es el mismo que [Lectura de subconjunto de archivos en una carpeta](#read-subset-of-files-in-folder) y [Lectura de todos los archivos de una carpeta específica](#read-all-files-from-specific-folder). Los escenarios más complejos de uso de caracteres comodín se describen en [Consulta de archivos de Parquet](query-parquet-files.md).

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar más información en el artículo [Consulta de archivos específicos](query-specific-files.md).
