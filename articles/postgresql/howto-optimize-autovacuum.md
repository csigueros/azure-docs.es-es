---
title: 'Optimización del vaciado automático en Azure Database for PostgreSQL: servidor único'
description: En este artículo se describe cómo puede optimizar el vaciado automático en Azure Database for PostgreSQL con un único servidor.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: c47f91d9aed9af4c4fbb1a16c27d59b5a5da5d94
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136278"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Optimización del vaciado automático en Azure Database for PostgreSQL con un único servidor

En este artículo se describe cómo optimizar con eficacia el vaciado automático en un servidor de Azure Database for PostgreSQL.

## <a name="overview-of-autovacuum"></a>Información general sobre el vaciado automático

PostgreSQL usa el control de simultaneidad multiversión (MVCC) para permitir mayor simultaneidad de base de datos. Cada actualización da como resultado una inserción y eliminación, y cada proceso de eliminación da como resultado que las filas estén marcadas de forma temporal para su eliminación. El marcado temporal identifica tuplas inactivas que se purgarán más adelante. Para llevar a cabo estas tareas, PostgreSQL ejecuta un trabajo de vaciado.

Puede activar un trabajo de vaciado de forma manual o automática. Aparecerán más tuplas inactivas cuando la base de datos experimente una gran cantidad de operaciones de actualización o eliminación, y aparecerán menos cuando esta esté inactiva. Cuando la carga de la base de datos es muy elevada, tiene que vaciar con más frecuencia, lo que hace poco práctico el vaciado de trabajos *manual*.

El vaciado automático se puede configurar y aprovecharse de las opciones de ajuste. Los valores predeterminados que incluye PostgreSQL intentan garantizar el funcionamiento del producto en todos los tipos de dispositivos. Estos dispositivos incluyen Raspberry Pi. Los valores de configuración ideales dependen de lo siguiente:

- Total de recursos disponibles, como SKU y tamaño de almacenamiento.
- Uso de los recursos.
- Características de objetos individuales.

## <a name="autovacuum-benefits"></a>Ventajas del vaciado automático

Si no vacía de vez en cuando, las tuplas inactivas que se acumulan en pueden convertirse en:

- Sobredimensionamiento de datos, como bases de datos y tablas aún más grandes.
- Índices de mayor tamaño poco óptimos.
- Aumento de E/S.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Supervisión del sobredimensionamiento con consultas de vaciado automático

La siguiente consulta de ejemplo está diseñada para identificar el número de tuplas inactivas y activas en una tabla llamada XYZ:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Configuraciones del vaciado automático

Los parámetros de configuración que controlan el vaciado automático se basan en las respuestas a dos preguntas clave:

- ¿Cuándo debe comenzar?
- ¿Cuánto debe limpiar después de iniciarse?

Aquí se incluyen algunos de los parámetros de configuración del vaciado automático que puede actualizar según las preguntas anteriores, junto con algunas pautas.

Parámetro|Descripción|Valor predeterminado
---|---|---
autovacuum_vacuum_threshold|Especifica el número mínimo de tuplas actualizadas o eliminadas necesarias para desencadenar una operación de vaciado en cualquier tabla. El valor predeterminado es de 50 tuplas. Configure este parámetro solo en el archivo postgresql.conf o en la línea de comandos del servidor. Para anular la configuración de tablas individuales, cambie los parámetros de almacenamiento de la tabla en cuestión.|50
autovacuum_vacuum_scale_factor|Especifica una fracción del tamaño de la tabla para agregar a autovacuum_vacuum_threshold cuando se decide activar una operación de vaciado. El valor predeterminado es 0,2 (20 % del tamaño de la tabla). Configure este parámetro solo en el archivo postgresql.conf o en la línea de comandos del servidor. Para anular la configuración de tablas individuales, cambie los parámetros de almacenamiento de la tabla en cuestión.|0,2
autovacuum_vacuum_cost_limit|Especifica el valor límite del costo usado en las operaciones de vaciado automáticas. Si se especifica -1, que es el valor predeterminado, se usará el valor normal vacuum_cost_limit. Si hay más de un trabajo, el valor se distribuye proporcionalmente entre los trabajos de vaciado automático en funcionamiento. La suma de los límites de cada trabajo no excede el valor de esta variable. Configure este parámetro solo en el archivo postgresql.conf o en la línea de comandos del servidor. Para anular la configuración de tablas individuales, cambie los parámetros de almacenamiento de la tabla en cuestión.|-1
autovacuum_vacuum_cost_delay|Especifica el valor de demora del costo usado en las operaciones de vaciado automáticas. Si se especifica -1, se usa el valor normal vacuum_cost_delay. El valor predeterminado es 20 milisegundos. Configure este parámetro solo en el archivo postgresql.conf o en la línea de comandos del servidor. Para anular la configuración de tablas individuales, cambie los parámetros de almacenamiento de la tabla en cuestión.|20 ms
autovacuum_naptime | Especifica el retraso mínimo entre las ejecuciones de vaciado automáticas en cualquier base de datos determinada. En cada ronda, el demonio examina la base de datos y emite los comandos VACUUM y ANALYZE según sea necesario para las tablas en esa base de datos. El retraso se mide en segundos. Configure este parámetro solo en el archivo postgresql.conf o en la línea de comandos del servidor.| 15 s
autovacuum_max_workers | Especifica el número máximo de procesos de vaciado automático (distintos del selector de vaciado automático) que pueden ejecutarse en cualquier momento. El valor predeterminado es tres. Establezca este parámetro solo al inicio del servidor.|3

Para anular la configuración de tablas individuales, cambie los parámetros de almacenamiento de la tabla en cuestión.

## <a name="autovacuum-cost"></a>Costo del vaciado automático

Aquí se muestran los "costos" de ejecutar una operación de vaciado:

- Se bloquean las páginas de datos que ejecuta el vaciado.
- El procesamiento y la memoria se usan cuando se está ejecutando el trabajo de vaciado.

Por ello, no ejecute trabajos de vaciado con demasiada frecuencia ni con demasiada poca frecuencia. Un trabajo de vaciado debe adaptarse a la carga de trabajo. Pruebe todos los cambios de parámetros del vaciado automático porque cada uno tiene sus ventajas y desventajas.

## <a name="autovacuum-start-trigger"></a>Desencadenador de inicio del vaciado automático

El vaciado automático se activa cuando el número de tuplas desactivadas excede autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. En este caso, reltuples es una constante.

La limpieza del vaciado automático debe mantenerse acorde a la carga de la base de datos. En caso contrario, podría quedarse sin almacenamiento y experimentar una ralentización general en las consultas. Como se amortiza con el tiempo, la velocidad a la que la operación de vaciado limpia las tuplas inactivas debe ser igual a la velocidad a la que se crean las tuplas inactivas.

Las bases de datos con muchas actualizaciones y eliminaciones tienen más tuplas inactivas y necesitan más espacio. En general, las bases de datos con muchas actualizaciones y eliminaciones se benefician de los valores bajos de autovacuum_vacuum_scale_factor y de autovacuum_vacuum_threshold. Los valores bajos evitan la acumulación prolongada de tuplas inactivas. De todas formas, puede usar valores más altos para ambos parámetros con bases de datos más pequeñas porque la necesidad de vaciado es menos urgente. El proceso frecuente de vaciado conlleva un costo de procesamiento y memoria.

El factor de escala predeterminado de 20 % funciona bien en las tablas con un porcentaje bajo de tuplas inactivas. No funciona bien en las tablas con un alto porcentaje de tuplas inactivas. Por ejemplo, en una tabla de 20 GB, este factor de escala se traduce en 4 GB de tuplas inactivas. En una tabla de 1 TB, equivale a 200 GB de tuplas inactivas.

Con PostgreSQL puede configurar estos parámetros a nivel de tabla o de instancia. Hoy en día, puede configurar estos parámetros a nivel de tabla solo en Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Estimación del costo del vaciado automático

La ejecución del vaciado automático es "costosa" y hay parámetros para controlar el tiempo de ejecución de las operaciones de vaciado. Los siguientes parámetros le ayudarán a estimar el costo de ejecutar el vaciado:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

El proceso de vaciado lee las páginas físicas y busca tuplas desactivadas. Se considera que cada página en shared_buffers tiene un costo de 1 (vacuum_cost_page_hit); todas las demás páginas tienen un costo de 20 (vacuum_cost_page_dirty) si existen tuplas inactivas, o de 10 (vacuum_cost_page_miss) si no existen tuplas inactivas. La operación de vaciado se detiene cuando el proceso excede el valor de autovacuum_vacuum_cost_limit.

Una vez que se alcanza el límite, el proceso se detiene durante el tiempo que especificó el parámetro autovacuum_vacuum_cost_delay antes de volver a iniciarlo. Si no se alcanza el límite, comienza el vaciado automático después del valor especificado por el parámetro autovacuum_naptime.

En resumen, los parámetros autovacuum_vacuum_cost_delay y autovacuum_vacuum_cost_limit controlan cuántos datos se pueden limpiar por unidad de tiempo. Tenga en cuenta que los valores predeterminados son demasiado bajos para la mayoría de los planes de tarifa. Los valores óptimos para estos parámetros dependen de esos planes de tarifa y deben configurarse en consecuencia.

El parámetro autovacuum_max_workers determina el número máximo de procesos de vaciado automático que pueden ejecutarse simultáneamente.

Con PostgreSQL puede configurar estos parámetros a nivel de tabla o de instancia. Hoy en día, puede configurar estos parámetros a nivel de tabla solo en Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optimización del vaciado automático por tabla

Puede configurar todos los parámetros de configuración anteriores por tabla. Este es un ejemplo:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

El vaciado automático es un proceso sincrónico que se realiza por tabla. Cuanto mayor es el porcentaje de tuplas inactivas que tiene una tabla, mayor es el "coste" del vaciado automático. Puede dividir las tablas que tienen una alta tasa de actualizaciones y eliminaciones en varias tablas. La división de tablas ayuda a paralelizar el vaciado automático y reduce el "coste" de completar el vaciado automático en una tabla. También puede aumentar el número de trabajos paralelos de vaciado automático para asegurarse de que los trabajos se programan de forma independiente.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca del uso y la optimización del vaciado automático, consulte la siguiente documentación de PostgreSQL:

- [Chapter 18, Server configuration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html) (Capítulo 18: configuración del servidor)
- [Chapter 24, Routine database maintenance tasks](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html) (Capítulo 24: tareas rutinarias de mantenimiento de base de datos)
