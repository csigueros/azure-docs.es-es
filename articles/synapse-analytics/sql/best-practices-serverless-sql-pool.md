---
title: Procedimientos recomendados para el grupo de SQL sin servidor
description: Recomendaciones y procedimientos recomendados para trabajar con un grupo de SQL sin servidor.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 7611d9067422a0e2e342bbafeb315b5b1545a212
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545197"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Procedimientos recomendados para el grupo de SQL sin servidor en Azure Synapse Analytics

En este artículo, encontrará una colección de procedimientos recomendados para usar un grupo de SQL sin servidor. Un grupo de SQL sin servidor es un recurso de Azure Synapse Analytics. Si trabaja con un grupo de SQL dedicado, consulte [Procedimientos recomendados para grupos de SQL dedicados en Azure Synapse Analytics](best-practices-dedicated-sql-pool.md) para obtener instrucciones específicas.

El grupo de SQL sin servidor permite consultar archivos de las cuentas de Azure Storage. No tiene funcionalidades de ingesta o almacenamiento local. Todos los archivos de destino de las consultas son externos al grupo de SQL sin servidor. Todo lo relacionado con la lectura de archivos desde el almacenamiento puede afectar al rendimiento de las consultas.

Estas son algunas directrices generales:

- Asegúrese de que las aplicaciones cliente se coloquen con el grupo de SQL sin servidor.
  - Si usa aplicaciones cliente fuera de Azure, asegúrese de que usa un grupo de SQL sin servidor en una región cercana al equipo cliente. Entre los ejemplos de aplicaciones cliente, se incluyen Power BI Desktop, SQL Server Management Studio y Azure Data Studio.
- Asegúrese de que el almacenamiento y el grupo de SQL sin servidor se encuentren en la misma región. Entre los ejemplos de almacenamiento, se incluyen Azure Data Lake Storage y Azure Cosmos DB.
- Intente [optimizar el diseño de almacenamiento](#prepare-files-for-querying) mediante la creación de particiones y manteniendo los archivos en el intervalo entre 100 MB y 10 GB.
- Si va a devolver un gran número de resultados, asegúrese de que usa SQL Server Management Studio o Azure Data Studio y no Azure Synapse Studio. Azure Synapse Studio es una herramienta web que no está diseñada para grandes conjuntos de resultados.
- Si va a filtrar los resultados por una columna de cadena, intente usar una intercalación `BIN2_UTF8`.
- Considere la posibilidad de almacenar en caché los resultados en el lado cliente mediante el modo de importación de Power BI o Azure Analysis Services y actualícelos periódicamente. Los grupos de SQL sin servidor no pueden proporcionar una experiencia interactiva en el modo de consulta directa de Power BI si usa consultas complejas o procesa una gran cantidad de datos.

## <a name="client-applications-and-network-connections"></a>Aplicaciones cliente y conexiones de red

Asegúrese de que la aplicación cliente esté conectada al área de trabajo de Azure Synapse más cercana posible con la conexión óptima.
- Coloque una aplicación cliente con el área de trabajo de Azure Synapse. Si usa aplicaciones como Power BI o Azure Analysis Service, asegúrese de que se encuentren en la misma región donde ha colocado el área de trabajo de Azure Synapse. Si es necesario, cree las áreas de trabajo independientes que se emparejan con las aplicaciones cliente. Colocar una aplicación cliente y el área de trabajo de Azure Synapse en regiones distintas podría provocar una latencia mayor y una transmisión más lenta de los resultados.
- Si va a leer datos de la aplicación local, asegúrese de que el área de trabajo de Azure Synapse se encuentre en una región cercana a su ubicación.
- Asegúrese de que no haya incidencias de ancho de banda de red al leer una gran cantidad de datos.
- No use Azure Synapse Studio para devolver una gran cantidad de datos. Azure Synapse Studio es una herramienta web que usa el protocolo HTTPS para transferir datos. Use Azure Data Studio o SQL Server Management Studio para leer una gran cantidad de datos.

## <a name="storage-and-content-layout"></a>Diseño de almacenamiento y contenido

Estos son los procedimientos recomendados para el almacenamiento y el diseño del contenido en un grupo de SQL sin servidor.

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Ubicación conjunta del almacenamiento y del grupo de SQL sin servidor

Para minimizar la latencia, coloque la cuenta de Azure Storage o del almacenamiento analítico de Azure Cosmos DB junto con el punto de conexión del grupo de SQL sin servidor. Las cuentas de almacenamiento y los puntos de conexión aprovisionados durante la creación del área de trabajo se encuentran en la misma región.

Para un rendimiento óptimo, si accede a otras cuentas de almacenamiento con el grupo de SQL sin servidor, asegúrese de que se encuentren en la misma región. Si no están en la misma región, aumentará la latencia de la transferencia de red de los datos entre la región remota y la del punto de conexión.

### <a name="azure-storage-throttling"></a>Limitaciones de Azure Storage

Varias aplicaciones y servicios pueden acceder a la cuenta de almacenamiento. Las limitaciones de Storage se producen cuando el número de IOPS o el rendimiento combinados que generan las aplicaciones, los servicios y las cargas de trabajo del grupo de SQL sin servidor superan los límites de la cuenta de almacenamiento. Como resultado, se producirá un efecto negativo significativo en el rendimiento de las consultas.

Cuando se detecta la limitación, el grupo de SQL sin servidor dispone de controles integrados para resolverla. El grupo de SQL sin servidor realiza las solicitudes al almacenamiento a un ritmo más lento hasta que la limitación se resuelva.

> [!TIP]
> Para que la ejecución de las consultas sea óptima, no fuerce la cuenta de almacenamiento con otras cargas de trabajo durante la ejecución de consultas.

### <a name="azure-ad-pass-through-authentication-performance"></a>Rendimiento de la autenticación transferida de Azure AD

El grupo de SQL sin servidor permite acceder a los archivos del almacenamiento mediante la autenticación diferida de Azure Active Directory (Azure AD) o credenciales de firma de acceso compartido. Con la autenticación diferida de Azure AD, podría experimentar un rendimiento más lento que con firmas de acceso compartido.

Si necesita un mejor rendimiento, pruebe a usar las credenciales de firma de acceso compartido para acceder al almacenamiento.

### <a name="prepare-files-for-querying"></a>Preparación de los archivos para la consulta

Si es posible, puede preparar los archivos para mejorar el rendimiento:

- Convierta los archivos CSV y JSON de gran tamaño a Parquet. Parquet es un formato de columnas. Dado que está comprimido, el tamaño de sus archivos es menor que el de los archivos CSV y JSON que contienen los mismos datos. Si lee archivos Parquet, el grupo de SQL sin servidor omite las columnas y filas que no son necesarias en la consulta. El grupo de SQL sin servidor necesita menos tiempo y solicitudes de almacenamiento para leerlos.
- Si una consulta tiene como destino un solo archivo de gran tamaño, se beneficiará de dividirlo en varios archivos más pequeños.
- Trate de mantener el tamaño del archivo CSV entre 100 MB y 10 GB.
- Es mejor tener archivos de igual tamaño para una sola ruta de acceso OPENROWSET o una ubicación de tabla externa.
- Para dividir los datos, almacene las particiones en diferentes carpetas o nombres de archivo. Consulte [Uso de las funciones filename y filepath para seleccionar particiones de destino específicas](#use-filename-and-filepath-functions-to-target-specific-partitions).

### <a name="colocate-your-azure-cosmos-db-analytical-storage-and-serverless-sql-pool"></a>Colocación del almacenamiento analítico de Azure Cosmos DB y el grupo de SQL sin servidor

Asegúrese de que el almacenamiento analítico de Azure Cosmos DB se encuentre en la misma región que el área de trabajo de Azure Synapse. Las consultas entre regiones pueden producir latencias enormes. Use la propiedad "region" de la cadena de conexión para especificar explícitamente la región en la que está ubicado el almacén analítico (revise [Consulta de datos de Azure Cosmos DB con un grupo de SQL sin servidor en Azure Synapse Link](query-cosmos-db-analytical-store.md#overview)):

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>Optimizaciones de archivo .csv

Estos son los procedimientos recomendados para usar archivos .csv en un grupo de SQL sin servidor.

### <a name="use-parser_version-20-to-query-csv-files"></a>Uso de PARSER_VERSION 2.0 para consultar archivos CSV

Puede usar el analizador optimizado para rendimiento al consultar archivos CSV. Para más información, consulte [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Creación manual de estadísticas para archivos .csv

El grupo de SQL sin servidor se basa en las estadísticas para generar planes de ejecución de consulta óptimos. Se crearán automáticamente estadísticas de las columnas de los archivos Parquet cuando sea necesario. En este momento, no se crean automáticamente las estadísticas para las columnas de los archivos .csv. Cree manualmente las estadísticas para las columnas que utiliza en las consultas, especialmente las utilizadas en DISTINCT, JOIN, WHERE, ORDER BY y GROUP BY. Para más información, consulte [Estadísticas en un grupo de SQL sin servidor](develop-tables-statistics.md#statistics-in-serverless-sql-pool).

## <a name="data-types"></a>Tipos de datos

Estos son los procedimientos recomendados para usar tipos de datos en un grupo de SQL sin servidor.

### <a name="use-appropriate-data-types"></a>Uso del tipo de datos adecuado

Los tipos de datos que se usan en la consulta afectan al rendimiento y la simultaneidad. Puede obtener un mejor rendimiento si sigue estas instrucciones: 

- Use el tamaño de datos más pequeño que se adapte al mayor valor posible.
  - Si la longitud máxima de caracteres es de 30, utilice un tipo de datos de caracteres de esa longitud.
  - Si todos los valores de las columnas de caracteres tienen un tamaño fijo, use **char** o **nchar**. De lo contrario, use **varchar** o **nvarchar**.
  - Si el valor máximo de la columna de enteros es 500, use **smallint**, ya que es el menor tipo de datos que puede contener este valor. Puede encontrar intervalos de tipos de datos enteros en [este artículo](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Si es posible, use **varchar** y **char** en lugar de **nvarchar** y **nchar**.
  - Use el tipo **varchar** con alguna intercalación UTF8 si lee datos de Parquet, Azure Cosmos DB, Delta Lake o CSV con codificación UTF-8.
  - Use el tipo **varchar** sin intercalación UTF8 si lee datos de archivos .csv que no son Unicode (por ejemplo, ASCII).
  - Use el tipo **nvarchar** si lee datos de un archivo .csv UTF-16.
- Utilice tipos de datos basados en enteros si es posible. Las operaciones SORT, JOIN y GROUP BY se realizan más rápidamente en números enteros que en datos de caracteres.
- Si usa la inferencia de esquemas, [compruebe los tipos de datos inferidos](#check-inferred-data-types) e invalídelos explícitamente con tipos más pequeños, si es posible.

### <a name="check-inferred-data-types"></a>Comprobación de los tipos de datos inferidos

La [inferencia de esquemas](query-parquet-files.md#automatic-schema-inference) ayuda a escribir consultas rápidamente y a explorar los datos sin conocer los esquemas de archivo. El costo de esta comodidad es que los tipos de datos inferidos pueden ser mayores que los tipos de datos reales. Esta discrepancia sucede cuando no hay suficiente información en los archivos de origen para asegurarse de que se utilice el tipo de datos adecuado. Por ejemplo, los archivos Parquet no contienen metadatos sobre la longitud máxima de la columna de caracteres. Por tanto, el grupo de SQL sin servidor lo infiere como varchar(8000).

Puede usar [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) para comprobar los tipos de datos resultantes de la consulta.

En el ejemplo siguiente se muestra cómo se pueden optimizar los tipos de datos inferidos. Este procedimiento se usa para mostrar tipos de datos inferidos:

```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Este es el conjunto de resultados:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------|
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

Una vez que conozca los tipos de datos inferidos para la consulta, puede especificar los tipos de datos adecuados:

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Optimización de filtro

Estos son los procedimientos recomendados para usar consultas en un grupo de SQL sin servidor.

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Inserción de caracteres comodín en los niveles inferiores de la ruta de acceso

Puede usar caracteres comodín en la ruta de acceso para [consultar varios archivos y carpetas](query-data-storage.md#query-multiple-files-or-folders). El grupo de SQL sin servidor muestra los archivos de la cuenta de almacenamiento, empezando por el primer carácter comodín (*) mediante la API de almacenamiento. Elimina los archivos que no coinciden con la ruta de acceso especificada. Al reducir la lista inicial de archivos, puede mejorar el rendimiento si hay muchos que coincidan con la ruta de acceso especificada hasta el primer carácter comodín.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Uso de las funciones fileinfo y filepath para seleccionar particiones de destino específicas

A menudo, los datos se organizan en particiones. Puede indicar a un grupo de SQL sin servidor que consulte carpetas y archivos concretos. De este modo, se reduce el número de archivos y la cantidad de datos que la consulta tiene que leer y procesar. Como ventaja adicional, logrará un mejor rendimiento.

Para más información, lea acerca de las funciones [filename](query-data-storage.md#filename-function) y [filepath](query-data-storage.md#filepath-function), y consulte los ejemplos para [consultar archivos específicos](query-specific-files.md).

> [!TIP]
> Convierta siempre los resultados de las funciones filepath y filename a los tipos de datos adecuados. Si usa tipos de datos de caracteres, asegúrese de que se usa la longitud apropiada.

Las funciones empleadas para la eliminación de particiones, filepath y filename, no se admiten actualmente para tablas externas que no sean las creadas automáticamente para cada tabla creada en Apache Spark para Azure Synapse Analytics.

Si los datos almacenados no tienen particiones, considere la posibilidad de crearlas. De este modo, puede usar estas funciones para optimizar las consultas destinadas a esos archivos. Cuando [consulte tablas con particiones de Apache Spark para Azure Synapse](develop-storage-files-spark-tables.md) desde el grupo de SQL sin servidor, la consulta se dirige automáticamente solo a los archivos necesarios.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Use la intercalación adecuada para utilizar la delegación de predicados para las columnas de caracteres

Los datos de un archivo Parquet se organizan en grupos de filas. El grupo de SQL sin servidor omite los grupos de filas en función del predicado especificado en la cláusula WHERE, lo que reduce la E/S. El resultado es un mayor rendimiento de las consultas.

Tenga en cuenta que la delegación de predicado para las columnas de caracteres de los archivos Parquet solo es compatible con la intercalación Latin1_General_100_BIN2_UTF8. Puede especificar la intercalación para una columna específica mediante una cláusula WITH. Si no se especifica esta intercalación mediante una cláusula WITH, se usará la intercalación de la base de datos.

## <a name="optimize-repeating-queries"></a>Optimizar las consultas repetidas

Estos son los procedimientos recomendados para usar CETAS en un grupo de SQL sin servidor.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Uso de CETAS para mejorar el rendimiento de las consultas y las combinaciones

[CETAS](develop-tables-cetas.md) es una de las características más importantes que están disponibles en el grupo de SQL sin servidor. CETAS es una operación en paralelo que crea metadatos de tabla externa y exporta los resultados de la consulta SELECT a un conjunto de archivos de la cuenta de almacenamiento.

Puede utilizar CETAS para materializar partes de consultas de uso frecuente, como tablas de referencia unidas, en un nuevo conjunto de archivos. A continuación, puede realizar combinaciones con esta tabla externa única, en lugar de repetir combinaciones comunes en varias consultas.

A medida que CETAS genera archivos Parquet, se crean estadísticas automáticamente cuando la primera consulta selecciona como destino a esta tabla externa. El resultado mejora el rendimiento de las consultas posteriores que tienen como destino la tabla generada con CETAS.

## <a name="next-steps"></a>Pasos siguientes

Consulte en el artículo [Solución de problemas](resources-self-help-sql-on-demand.md) soluciones a problemas comunes. Si trabaja con un grupo de SQL dedicado en lugar de un grupo de SQL sin servidor, consulte [Procedimientos recomendados para grupos de SQL dedicados en Azure Synapse Analytics](best-practices-dedicated-sql-pool.md) para conocer instrucciones específicas.
