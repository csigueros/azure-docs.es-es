---
title: Test Plans de la guía para la migración de MySQL en el entorno local a Azure Database for MySQL
description: WWI creó un plan de prueba que incluía un conjunto de tareas de IT y de negocio. Las migraciones correctas requieren que se ejecuten todas las pruebas.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: a41f995d7fcef217780da4c89c5bc3e0acdf6bbc
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656947"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-test-plans"></a>Test Plans de la guía para la migración de MySQL en el entorno local a Azure Database for MySQL

### <a name="overview"></a>Información general

WWI creó un plan de prueba que incluía un conjunto de tareas de IT y de negocio. Las migraciones correctas requieren que se ejecuten todas las pruebas.

Pruebas:

  - Asegúrese de que la base de datos migrada es coherente con las tablas locales (los mismos recuentos de registros y resultados de consultas).

  - Asegúrese de que el rendimiento es aceptable (debe coincidir con el mismo rendimiento que si se estuviera ejecutando en el entorno local).

  - Asegúrese de que el rendimiento de las consultas de destino cumple los requisitos indicados.

  - Asegúrese de que la conectividad entre la red local y la red de Azure es aceptable.

  - Asegúrese de que todas las aplicaciones y usuarios identificados pueden conectarse a la instancia de datos migrada.

WWI ha identificado un fin de semana de migración y una ventana de tiempo que se inició a las 22:00 y finalizó a las 02:00, hora del Pacífico. Si la migración no se completó antes del objetivo de las 02:00 (objetivo de tiempo de inactividad de 4 horas) con todas las pruebas superadas, se iniciaron los procedimientos de reversión. Se documentaron problemas para futuros intentos de migración. Todas las ventanas de migraciones se reenviaron y reprogramaron en función de escalas de tiempo empresariales aceptables.

### <a name="sample-queries"></a>Consultas de ejemplo

Se ejecutaron una serie de consultas en el origen y el destino para comprobar que la migración de la base de datos se ha ejecutado correctamente. Las siguientes consultas y scripts ayudarán a determinar si las acciones de migración han movido todos los objetos de base de datos necesarios del origen al destino.

#### <a name="table-rows"></a>Filas de la tabla

Puede usar esta consulta para obtener todas las tablas y un recuento estimado de filas:

```
SELECT SUM(TABLE_ROWS)
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '{SchemaName}';
```

> [!NOTE]
> La tabla `INFORMATION_SCHEMA` proporcionará un conjunto estimado de valores entre las tablas. Para obtener una vista más precisa de las métricas, como el tamaño de la tabla, aumente el tamaño del ejemplo de página con el parámetro de servidor `innodb_stats_transient_sample_pages`. Aumentar este valor de servidor obligará a analizar más páginas y proporcionará resultados más precisos.

Ejecute la instrucción SQL `count(*)` en cada tabla para obtener un recuento preciso de filas. La ejecución de este comando puede tardar una gran cantidad de tiempo en tablas grandes. El siguiente script generará un conjunto de instrucciones SQL que se pueden ejecutar para obtener los recuentos exactos:

```
SELECT CONCAT( 
    'SELECT "', 
    table_name, 
    '" AS table_name, COUNT(*) AS exact_row_count FROM `', 
    table_schema, 
    '`.`', 
    table_name, 
    '` UNION ' 
)  
FROM INFORMATION_SCHEMA.TABLES 
WHERE table_schema = '**my_schema**';
```

#### <a name="table-fragmentation"></a>Fragmentación de tablas

Es probable que las tablas de datos sigan creciendo con el uso continuado de la aplicación. En algunos casos, es posible que los datos no aumenten, pero que cambien constantemente debido a eliminaciones y actualizaciones. Si es así, es posible que haya mucha fragmentación en los archivos de base de datos. La instrucción OPTIMIZE TABLE de MySQL puede reducir las necesidades de espacio de almacenamiento físico y mejorar la eficacia de E/S.

Puede [optimizar las tablas de MySQL](https://dev.mysql.com/doc/refman/8.0/en/optimize-table.html) ejecutando lo siguiente:

`optimize table TABLE_NAME`

#### <a name="database-objects"></a>Objetos de base de datos

Use la consulta siguiente para asegurarse de que se tienen en cuenta todos los demás objetos de base de datos. Aunque estas consultas pueden calcular los recuentos de las filas de la tabla, es posible que no tengan en cuenta la versión del objeto de base de datos determinado. Por ejemplo, aunque pueda existir un procedimiento almacenado, podría haber cambiado entre el inicio y el final de la migración. Se recomienda inmovilizar el desarrollo de objetos de base de datos durante la migración.

**Usuarios**

```
SELECT DISTINCT 
        USER 
FROM 
        mysql.user 
WHERE 
        user <> '' order by user
```

**Índices**

```
SELECT DISTINCT 
        INDEX_NAME 
FROM 
        information_schema.STATISTICS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```


**Restricciones**

```
SELECT DISTINCT 
        CONSTRAINT_NAME 
FROM 
        information_schema.TABLE_CONSTRAINTS 
WHERE 
        CONSTRAINT_SCHEMA = '{SchemaName}'
```

**Vistas**

```
SELECT 
        TABLE_NAME 
FROM 
        information_schema.VIEWS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**Procedimientos almacenados**

```
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'FUNCTION' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**Funciones**

```
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'PROCEDURE' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**Eventos**

```
SELECT 
        EVENT_NAME 
FROM 
        INFORMATION_SCHEMA.EVENTS 
WHERE 
        EVENT_SCHEMA = '{SchemaName}'
```

### <a name="rollback-strategies"></a>Estrategias de reversión

Las consultas anteriores proporcionan una lista de nombres de objetos y recuentos usados en una decisión de reversión. Los usuarios de la migración pueden realizar el primer paso de comprobación de objetos comprobando los recuentos de los objetos de origen y de destino. Una discrepancia en los recuentos de objetos no significa necesariamente que se requiera una reversión. La realización de una evaluación detallada podría indicar que la diferencia es leve y fácil de recuperar. La solución podría ser una migración manual de algunos objetos con errores. Por ejemplo, si se migraron todas las tablas y filas de datos y solo se omitieron algunas de las funciones, corrija esos objetos con errores y finalice la migración. Si la base de datos es relativamente pequeña, puede borrarse la instancia de Azure Database for MySQL y reiniciar la migración. Las bases de datos grandes pueden necesitar más tiempo del disponible en la ventana de migración para determinar los objetos que faltan. La migración tendrá que detenerse y revertirse.

La identificación de los objetos de la base de datos que faltan debe producirse rápidamente durante una ventana de migración. Recuentos cada minuto. Una opción podría ser exportar los nombres de objetos del entorno a un archivo y usar una herramienta de comparación de datos para identificar rápidamente los objetos que faltan. Otra opción podría ser exportar los nombres de objetos de la base de datos de origen e importar los datos a una tabla temporal del entorno de base de datos de destino. Compare los datos mediante una instrucción SQL **con script** y **probada**. La precisión y la velocidad de comprobación de los datos son fundamentales para el proceso de migración. No se dependa de leer y comprobar manualmente una larga lista de objetos de base de datos durante una ventana de migración. La posibilidad de errores humanos es demasiado grande. Sería mejor si administra por excepciones con herramientas.

### <a name="wwi-scenario"></a>Escenario de WWI

El CIO de WWI recibió un informe de confirmación de que todos los objetos de base de datos se migraron de la base de datos local a la instancia de Azure Database for MySQL. El equipo de base de datos ejecutó las consultas anteriores en la base de datos antes del comienzo de la migración y guardó todos los resultados en una hoja de cálculo.

La información del esquema de la base de datos de origen se usó para comprobar la fidelidad del objeto de migración de destino.

### <a name="checklist"></a>Lista de comprobación

  - Tenga consultas de prueba con script, probadas y listas para ejecutarse.

  - Debe saber cuánto tiempo pueden tardar en ejecutarse las consultas de prueba y convertirlas en parte de la escala de tiempo de migración documentada.

  - Tenga una estrategia de mitigación y reversión lista para los diferentes resultados posibles.

  - Tenga una escala de tiempo bien definida de eventos para la migración.  


> [!div class="nextstepaction"]
> [Líneas base de rendimiento](./performance-baselines.md)