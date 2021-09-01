---
title: Administración del espacio de archivo de Azure SQL Database
description: En esta página se describe cómo administrar el espacio de archivo con bases de datos únicas o agrupadas de Azure SQL Database, y se proporcionan ejemplos de código para determinar si se debe reducir una base de datos única o agrupada, y cómo hacerlo.
services: sql-database
ms.service: sql-database
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, wiassaf
ms.date: 08/09/2021
ms.openlocfilehash: 27adb19b07dc67a91d1bdafb6aac54ad59eaa778
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178466"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Administración del espacio de archivo para bases de datos en Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

En este artículo se describen los diferentes tipos de espacio de almacenamiento para bases de datos en Azure SQL Database y los pasos que se pueden realizar cuando el espacio de archivo asignado se tiene que administrar de forma explícita.

> [!NOTE]
> Este artículo no se aplica a Instancia administrada de Azure SQL.

## <a name="overview"></a>Información general

Con Azure SQL Database, hay patrones de carga de trabajo donde la asignación de archivos de datos subyacentes para las bases de datos puede llegar a ser mayor que la cantidad de páginas de datos que se usan. Esta condición puede darse cuando el espacio usado aumenta y posteriormente se eliminan los datos. El motivo es que el espacio de archivo asignado no se reclama automáticamente cuando se eliminan los datos.

Es posible que sea necesario supervisar el uso del espacio de archivo y reducir los archivos de datos en los escenarios siguientes:

- Permitir el crecimiento de datos en un grupo elástico si el espacio de archivo asignado para sus bases de datos alcanza el tamaño máximo del grupo.
- Permitir la reducción del tamaño máximo de una instancia única de base de datos o grupo elástico.
- Permitir cambiar una instancia única de base de datos o grupo elástico a un nivel de servicio o un nivel de rendimiento diferente con un tamaño máximo inferior.

> [!NOTE]
> Las operaciones de reducción no deben considerarse una operación de mantenimiento normal. Los archivos de datos y de registro que crecen debido a operaciones empresariales periódicas y repetitivas no requieren operaciones de reducción. 

### <a name="monitoring-file-space-usage"></a>Supervisión del uso del espacio de archivo

La mayoría de las métricas de espacio de almacenamiento que aparecen en las API siguientes solo miden el tamaño de las páginas de datos que se usan:

- API de métricas basadas en Azure Resource Manager, como [get-metrics](/powershell/module/az.monitor/get-azmetric) de PowerShell

Sin embargo, las siguientes API también miden el tamaño del espacio asignado para las bases de datos y los grupos elásticos:

- T-SQL:  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

## <a name="understanding-types-of-storage-space-for-a-database"></a>Descripción de los tipos de espacio de almacenamiento para una base de datos

Comprender las cantidades de espacio de almacenamiento siguientes es importante para administrar el espacio de archivo de una base de datos.

|Cantidad de base de datos|Definición|Comentarios|
|---|---|---|
|**Espacio de datos usado**|La cantidad de espacio usado para almacenar los datos de la base de datos.|Por lo general, el espacio usado aumenta (disminuciones) en las inserciones (eliminaciones). En algunos casos, el espacio usado no cambia en las inserciones o eliminaciones, según la cantidad y el patrón de datos implicados en la operación y las posibles fragmentaciones. Por ejemplo, al eliminar una fila de cada página de datos no disminuye necesariamente el espacio usado.|
|**Espacio de datos asignado**|La cantidad de espacio de archivo de formato disponible para almacenar datos de la base de datos.|La cantidad de espacio asignado crece automáticamente, pero nunca disminuye después de las eliminaciones. Este comportamiento garantiza que las futuras inserciones son más rápidas puesto que no es necesario volver a formatear el espacio.|
|**Espacio de datos asignado, pero no usado**|La diferencia entre la cantidad de espacio de datos asignado y el espacio de datos usado.|Esta cantidad representa la cantidad máxima de espacio libre que se puede reclamar mediante la reducción de archivos de datos de base de datos.|
|**Tamaño máximo de datos**|La cantidad máxima de espacio que se puede usar para almacenar datos de base de datos.|La cantidad de espacio de datos asignado no puede crecer por encima del tamaño máximo de datos.|

En el siguiente diagrama se ilustra la relación entre los diferentes tipos de espacio de almacenamiento para una base de datos.

![tipos de espacio de almacenamiento y relaciones](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Consulta de la información de espacio de almacenamiento en una base de datos única

Las consultas siguientes pueden utilizarse para determinar las cantidades de espacio de almacenamiento para una base de datos única.  

### <a name="database-data-space-used"></a>Espacio de datos de la base de datos usado

Modifique la siguiente consulta para devolver la cantidad de espacio de datos de base de datos usado.  Las unidades de resultado de la consulta están en MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC;
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Espacio de datos de base de datos asignado y espacio asignado sin usar

Use la siguiente consulta para devolver la cantidad de espacio de datos de base de datos asignado y la cantidad de espacio asignado sin usar.  Las unidades de resultado de la consulta están en MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS';
```

### <a name="database-data-max-size"></a>Tamaño máximo de datos de base de datos

Modifique la siguiente consulta para devolver el tamaño máximo de datos de la base de datos.  Las unidades del resultado de la consulta están en bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes;
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Descripción de los tipos de espacio de almacenamiento para un grupo elástico

Comprender las cantidades de espacio de almacenamiento siguientes es importante para administrar el espacio de archivo de un grupo elástico.

|Cantidad de grupo elástico|Definición|Comentarios|
|---|---|---|
|**Espacio de datos usado**|La suma del espacio de datos utilizado por todas las bases de datos en el grupo elástico.||
|**Espacio de datos asignado**|La suma del espacio de datos asignado por todas las bases de datos en el grupo elástico.||
|**Espacio de datos asignado, pero no usado**|La diferencia entre la cantidad de espacio de datos asignado y el espacio de datos usado por todas las base de datos del grupo elástico.|Esta cantidad representa la cantidad máxima de espacio asignado para el grupo elástico que se puede reclamar mediante la reducción de archivos de datos de base de datos.|
|**Tamaño máximo de datos**|La cantidad máxima de espacio de datos que puede usar el grupo elástico para todas sus bases de datos.|El espacio asignado para el grupo elástico no puede exceder el tamaño máximo del grupo elástico.  Si se da esta condición, el espacio asignado que no se usa se puede reclamar mediante la reducción de archivos de datos de base de datos.|

> [!NOTE]
> El mensaje de error "El grupo elástico ha alcanzado su límite de almacenamiento" indica que se ha asignado suficiente espacio a los objetos de base de datos para cumplir el límite de almacenamiento del grupo elástico, pero puede haber espacio sin usar en la asignación de espacio de datos. Considere la posibilidad de aumentar el límite de almacenamiento del grupo elástico, o como una solución a corto plazo, la opción de liberar espacio de datos en la sección [**Reclamación del espacio asignado sin usar**](#reclaim-unused-allocated-space) disponible más adelante. También debe tener en cuenta el posible impacto negativo en el rendimiento por la reducción de los archivos de base de datos; vea la sección [**Recompilación de índices**](#rebuild-indexes) disponible más adelante.

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Consulta de la información de espacio de almacenamiento en un grupo elástico

Las consultas siguientes pueden utilizarse para determinar las cantidades de espacio de almacenamiento para un grupo elástico.  

### <a name="elastic-pool-data-space-used"></a>Espacio usado de datos de grupo elástico

Modifique la siguiente consulta para devolver la cantidad de espacio usado de datos del grupo elástico.  Las unidades de resultado de la consulta están en MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Espacio de datos de grupo elástico asignado y espacio asignado sin usar

Modifique los ejemplos siguientes para devolver una tabla que muestre el espacio asignado y sin usar para cada base de datos de un grupo elástico. La tabla ordena las bases de datos desde las que tienen más hasta las que tienen menos cantidad de espacio asignado sin usar.  Las unidades de resultado de la consulta están en MB.  

Los resultados de la consulta para determinar el espacio asignado para cada base de datos del grupo se pueden agregar juntos para determinar el espacio total asignado para el grupo elástico. El espacio de grupo elástico asignado no puede exceder el tamaño máximo del grupo elástico.  

> [!IMPORTANT]
> El módulo de Azure Resource Manager para PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. El módulo de AzureRM continuará recibiendo correcciones de errores hasta diciembre de 2020 como mínimo. Los argumentos para los comandos del módulo Az y los módulos AzureRm son esencialmente idénticos. Para obtener más información sobre la compatibilidad, vea [Presentación del nuevo módulo Az de Azure PowerShell](/powershell/azure/new-azureps-module-az).

El script de PowerShell requiere el módulo SQL Server PowerShell, consulte el artículo de [descarga del módulo de PowerShell](/sql/powershell/download-sql-server-ps-module) para la instalación.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

La captura de pantalla siguiente es un ejemplo de la salida del script:

![ejemplo de espacio asignado al grupo elástico y espacio asignado sin usar](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Tamaño máximo de datos del grupo elástico

Modifique la siguiente consulta T-SQL para devolver el tamaño máximo de datos de los grupos elásticos grabados.  Las unidades de resultado de la consulta están en MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

## <a name="reclaim-unused-allocated-space"></a>Reclamación del espacio asignado sin usar

> [!IMPORTANT]
> Los comandos de reducción afectan al rendimiento de la base de datos mientras se está ejecutando y, si es posible, se deben ejecutar durante períodos de poco uso.

### <a name="shrinking-data-files"></a>Reducción de los archivos de datos

Debido a un posible impacto en el rendimiento de la base datos, Azure SQL Database no reduce automáticamente los archivos de datos. Sin embargo, los clientes pueden reducirlos mediante un procedimiento de autoservicio en el momento que estimen oportuno. No debe ser una operación programada periódicamente, sino un evento único en respuesta a una reducción importante en el consumo de espacio usado por los archivos de datos.

En Azure SQL Database, puede usar los comandos `DBCC SHRINKDATABASE` o `DBCC SHRINKFILE` para reducir los archivos:

- `DBCC SHRINKDATABASE` reduce todos los archivos de datos y de registro de la base de datos, lo que normalmente no es necesario. El comando reduce un archivo a la vez. También [reduce el archivo de registro](#shrinking-transaction-log-file). Azure SQL Database reduce automáticamente los archivos de registro, si es necesario.
- El comando `DBCC SHRINKFILE` admite escenarios más avanzados:
    - Puede tener como objetivo archivos individuales según sea necesario, en lugar de reducir todos los archivos de la base de datos.
    - Cada comando `DBCC SHRINKFILE` se puede ejecutar en paralelo con otros comandos `DBCC SHRINKFILE` para reducir la base de datos más rápidamente, a costa de un mayor uso de recursos y una mayor probabilidad de bloquear las consultas de usuario, si se ejecutan durante la reducción.
    - Si el final del archivo no contiene datos, puede reducir el tamaño de archivo asignado mucho más rápido especificando el argumento TRUNCATEONLY. Esto no requiere el movimiento de datos dentro del archivo.
- Para más información sobre estos comandos de reducción, consulte [DBCC SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) o [DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql).

Los ejemplos siguientes se deben ejecutar mientras está conectado a la base de datos de usuario de destino, no a la base de datos `master`.

Para usar `DBCC SHRINKDATABASE` para reducir todos los archivos de datos y de registro de una base de datos determinada:

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'database_name');
```

En Azure SQL Database, una base de datos puede tener uno o varios archivos de datos. Los archivos de datos adicionales solo se pueden crear automáticamente. Para determinar el diseño de archivo de la base de datos, consulte la vista de catálogo `sys.database_files` mediante el siguiente script de ejemplo:

```sql
-- Review file properties, including file_id values to reference in shrink commands
SELECT file_id,
       name,
       CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8 / 1024. AS space_used_mb,
       CAST(size AS bigint) * 8 / 1024. AS space_allocated_mb,
       CAST(max_size AS bigint) * 8 / 1024. AS max_size_mb
FROM sys.database_files
WHERE type_desc IN ('ROWS','LOG');
GO
```

Ejecute una reducción en un archivo solo mediante el comando `DBCC SHRINKFILE`: por ejemplo:

```sql
-- Shrink database data file named 'data_0` by removing all unused at the end of the file, if any.
DBCC SHRINKFILE ('data_0', TRUNCATEONLY);
GO
```

También debe tener en cuenta el posible impacto negativo en el rendimiento por la reducción de los archivos de base de datos; consulte la sección [Mantenimiento de índices antes o después de la reducción](#rebuild-indexes) disponible más adelante. 

### <a name="shrinking-transaction-log-file"></a>Reducción del archivo de registro de transacciones

A diferencia de los archivos de datos, Azure SQL Database reduce automáticamente el archivo de registro de transacciones para evitar un uso de espacio excesivo que pueda provocar errores de espacio insuficiente. No suele ser necesario que los clientes reduzcan el archivo de registro de transacciones.

En los niveles de servicio Premium y Crítico para la empresa, si el registro de transacciones aumenta demasiado, puede contribuir de forma significativa al consumo de almacenamiento local para alcanzar el límite de [almacenamiento local máximo](resource-limits-logical-server.md#storage-space-governance). Si el consumo de almacenamiento local se aproxima al límite, los clientes pueden optar por reducir el registro de transacciones mediante el comando [DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql), tal y como se muestra en el ejemplo siguiente. Esta operación libera espacio de almacenamiento local en cuanto se completa el comando, sin necesidad de esperar a la operación de reducción automática periódica.

El siguiente ejemplo se debe ejecutar mientras está conectado a la base de datos de usuario de destino, no a la base de datos maestra.

```tsql
-- Shrink the database log file (always file_id = 2), by removing all unused space at the end of the file, if any.
DBCC SHRINKFILE (2, TRUNCATEONLY);
```

### <a name="auto-shrink"></a>Reducción automática

Como alternativa, puede habilitarse la reducción automática para una base de datos. Sin embargo, la reducción automática es menos eficaz al reclamar espacio de archivo que `DBCC SHRINKDATABASE` y `DBCC SHRINKFILE`.  

La reducción automática puede ser útil en el escenario específico en el que un grupo elástico contiene muchas bases de datos que experimentan un crecimiento y una reducción significativos del espacio de archivo de datos utilizado. No se trata de un escenario habitual. 

La reducción automática está deshabilitada de forma predeterminada, lo cual se recomienda para la mayoría de las bases de datos. Si es necesario habilitar la reducción automática, se recomienda deshabilitarla una vez que se hayan alcanzado los objetivos de administración del espacio, en lugar de mantenerla habilitada de forma permanente. Para más información, vea [Consideraciones para AUTO_SHRINK](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink).

Para habilitar la reducción automática, ejecute el siguiente comando mientras está conectado a su base de datos (no a la base de datos maestra).

```sql
-- Enable auto-shrink for the current database.
ALTER DATABASE CURRENT SET AUTO_SHRINK ON;
```

Para más información sobre este comando, consulte las opciones de [DATABASE SET](/sql/t-sql/statements/alter-database-transact-sql-set-options).

### <a name="index-maintenance-before-or-after-shrink"></a><a name="rebuild-indexes"></a> Mantenimiento de índices antes o después de la reducción

Una vez completada una operación de reducción en los archivos de datos, los índices pueden quedar fragmentados y perder su eficacia de optimización del rendimiento para determinadas cargas de trabajo, como las consultas que usan exploraciones grandes. Si se produce una degradación del rendimiento una vez completada la operación de reducción, considere la posibilidad de realizar el mantenimiento de índices para volver a generar los índices. 

Si la densidad de página en la base de datos es baja, una reducción tarda más porque tendrá que mover más páginas en cada archivo de datos. Microsoft recomienda determinar la densidad media de página antes de ejecutar los comandos de reducción. Si la densidad de página es baja, vuelva a generar u organizar los índices para aumentar la densidad de página antes de ejecutar la reducción. Para obtener más información, incluido un script de ejemplo para determinar la densidad de página, consulte [Optimización del mantenimiento de índices para mejorar el rendimiento de las consultas y reducir el consumo de recursos](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Pasos siguientes

- Para información sobre los tamaños máximos de base de datos, consulte:
  - [Límites del modelo de compra basado en núcleo virtual de Azure SQL Database para una base de datos única](resource-limits-vcore-single-databases.md)
  - [Límites de recursos para bases de datos únicas que utilizan el modelo de compra basado en DTU](resource-limits-dtu-single-databases.md)
  - [Límites del modelo de compra basado en núcleo virtual de Azure SQL Database para grupos elásticos](resource-limits-vcore-elastic-pools.md)
  - [Límites de recursos para grupos elásticos que utilizan el modelo de compra basado en DTU](resource-limits-dtu-elastic-pools.md)
