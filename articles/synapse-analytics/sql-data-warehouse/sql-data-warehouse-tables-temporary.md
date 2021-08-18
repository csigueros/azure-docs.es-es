---
title: Tablas temporales
description: Directrices esenciales para el uso de tablas temporales en el grupo de SQL dedicado, con énfasis en los principios de las tablas temporales de nivel de sesión.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 9898bc94aa79b374174f80592dc250e660f7f8c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734591"
---
# <a name="temporary-tables-in-dedicated-sql-pool"></a>Tablas temporales en un grupo de SQL dedicado

Este artículo contiene directrices esenciales para el uso de tablas temporales y resalta los principios de las tablas temporales de nivel de sesión. 

La información de este artículo puede ayudarle a dividir en secciones el código y así mejorar su reusabilidad y facilidad de mantenimiento.

## <a name="what-are-temporary-tables"></a>¿Qué son las tablas temporales?

Las tablas temporales son útiles al procesar datos, especialmente durante la transformación donde los resultados intermedios son transitorios. En el grupo de SQL dedicado, las tablas temporales existen en el nivel de sesión.  

Solo son visibles para la sesión en la que se crearon y se eliminan automáticamente cuando esa sesión se cierra.  

Las tablas temporales ofrecen ventajas para el rendimiento porque sus resultados se escriben en el almacenamiento local en lugar de en el remoto.

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Tablas temporales en el grupo de SQL dedicado

En el recurso de grupo de SQL dedicado, las tablas temporales ofrecen ventajas para el rendimiento porque sus resultados se escriben en el almacenamiento local, en lugar de en el remoto.

### <a name="create-a-temporary-table"></a>Creación de una tabla temporal

Las tablas temporales se crean colocando `#` delante del nombre de la tabla.  Por ejemplo:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

También se pueden crear tablas temporales mediante `CTAS` siguiendo exactamente el mismo método:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` es un comando eficaz y ofrece un uso eficiente del espacio del registro de transacciones. 
> 
> 

## <a name="dropping-temporary-tables"></a>Eliminación de tablas temporales
Cuando se crea una nueva sesión, no debe existir ninguna tabla temporal.  

Si va a llamar al mismo procedimiento almacenado, lo que crea un archivo temporal con el mismo nombre, para tener la seguridad de que las instrucciones `CREATE TABLE` se ejecutan correctamente, se puede usar una sencilla comprobación de existencia previa con `DROP`, como en el ejemplo siguiente:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Por coherencia con la codificación, se recomienda usar este patrón tanto para tablas como para tablas temporales.  También es buena idea usar `DROP TABLE` para quitar las tablas temporales cuando ya no las necesite en el código.  

En el desarrollo de procedimientos almacenados, es habitual que los comandos de eliminación se empaqueten juntos al final de un procedimiento para garantizar que estos objetos se limpian.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularización de código
Como las tablas temporales se pueden ver en cualquier parte de una sesión de usuario, se puede aprovechar esta funcionalidad para ayudarle a dividir en secciones el código de aplicación.  

Por ejemplo, el siguiente procedimiento almacenado genera DDL para actualizar todas las estadísticas de la base de datos por nombre de la estadística:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    #stats_ddl
;
GO
```

En este punto, la única acción que se ha producido es la creación de un procedimiento almacenado que genera una tabla temporal, #stats_ddl, con instrucciones DDL.  

Este procedimiento almacenado quita #stats_ddl si ya existe para tener la seguridad de que no dará error si se ejecuta más de una vez dentro de una sesión.  

Sin embargo, puesto que no hay ningún elemento `DROP TABLE` al final del procedimiento almacenado, cuando se complete este, saldrá de la tabla creada para que se pueda leer fuera del procedimiento almacenado.  

En el grupo de SQL dedicado, a diferencia de otras bases de datos SQL Server, es posible usar la tabla temporal fuera del procedimiento almacenado que la ha creado.  Las tablas temporales del grupo de SQL dedicado se pueden usar **en cualquier parte** dentro de la sesión. Esta característica puede dar lugar a código más modular y administrable como en el ejemplo siguiente:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitaciones de tablas temporales
El grupo de SQL dedicado impone algunas limitaciones al implementar tablas temporales.  Actualmente, solo se admiten tablas temporales con ámbito de sesión.  No se admiten tablas temporales globales.  

Además, no se pueden crear vistas en tablas temporales.  Solo se pueden crear tablas temporales con distribución hash o round robin.  No se admite la distribución de tablas temporales replicadas. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el desarrollo de tablas, consulte el artículo [Diseño de tablas mediante el grupo de SQL dedicado](sql-data-warehouse-tables-overview.md).

