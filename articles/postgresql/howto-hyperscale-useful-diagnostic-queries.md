---
title: 'Consultas de diagnóstico útiles: Hiperescala (Citus) y Azure Database for PostgreSQL'
description: Consultas para conocer los datos distribuidos y mucho más
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: efd251e48beb4e2f2b3db16f694da14888e876dd
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822540"
---
# <a name="useful-diagnostic-queries"></a>Consultas de diagnóstico útiles

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>Búsqueda del nodo que contiene los datos de un inquilino específico

En el caso de uso de varios inquilinos, podemos determinar qué nodo de trabajo contiene las filas de un inquilino concreto.  Hiperescala (Citus) agrupa las filas de tablas distribuidas en particiones y coloca cada partición en un nodo de trabajo en el grupo de servidores. 

Supongamos que los inquilinos de la aplicación son almacenes y queremos encontrar qué nodo de trabajo contiene los datos del identificador de almacén 4.  Es decir, queremos encontrar la ubicación de la partición que contiene las filas cuya columna de distribución tiene el valor 4:

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

La salida contiene el host y el puerto de la base de datos de trabajo.

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>Búsqueda de la columna de distribución de una tabla

Cada tabla distribuida en Hiperescala (Citus) tiene una "columna de distribución". (Para más información, consulte [Modelado de datos distribuidos](concepts-hyperscale-choose-distribution-column.md)). Puede ser importante saber qué columna es. Por ejemplo, al combinar o filtrar tablas, puede ver mensajes de error con sugerencias como "agregar un filtro a la columna de distribución".

Las tablas `pg_dist_*` del nodo de coordinación contienen diversos metadatos sobre la base de datos distribuida. En concreto, `pg_dist_partition` contiene información sobre la columna de distribución de cada tabla. Puede usar una función de utilidad adecuada para buscar el nombre de la columna de distribución a partir de los detalles de bajo nivel de los metadatos. A continuación, se muestra un ejemplo y su salida:

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Salida de ejemplo:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>Detección de bloqueos

Esta consulta se ejecutará en todos los nodos de trabajo e identificará los bloqueos, cuánto tiempo se han abierto y las consultas infractoras:

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

Salida de ejemplo:

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>Consulta del tamaño de las particiones

Esta consulta le proporcionará el tamaño de todas las particiones de una tabla distribuida determinada, denominada `my_distributed_table`:

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

Salida de ejemplo:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>Consulta del tamaño de todas las tablas distribuidas

Esta consulta obtiene una lista de los tamaños de cada tabla distribuida más el tamaño de sus índices.

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

Salida de ejemplo:

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

Tenga en cuenta que existen otras funciones de Hiperescala (Citus) para consultar el tamaño de las tablas distribuidas; vea [Determinación del tamaño de la tabla](howto-hyperscale-table-size.md).

## <a name="identifying-unused-indices"></a>Identificación de índices no usados

La siguiente consulta identificará los índices no usados en los nodos de trabajo de una tabla distribuida determinada (`my_distributed_table`)

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

Salida de ejemplo:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>Supervisión del recuento de conexiones de cliente

La siguiente consulta cuenta las conexiones abiertas en el coordinador y las agrupa por el tipo.

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

Salida de ejemplo:

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="viewing-system-queries"></a>Visualización de consultas del sistema

### <a name="active-queries"></a>Consultas activas

La vista `pg_stat_activity` muestra las consultas que se están ejecutando. Puede filtrar para buscar las que lo están haciendo de manera activa, junto con el identificador de proceso de su back-end:

```sql
SELECT pid, query, state
  FROM pg_stat_activity
 WHERE state != 'idle';
```

### <a name="why-are-queries-waiting"></a>Por qué hay consultas en espera

También es posible consultar las razones más comunes de que haya consultas no inactivas en espera. Para obtener una explicación de las razones, vea la [documentación de PostgreSQL](https://www.postgresql.org/docs/current/monitoring-stats.html#WAIT-EVENT-TABLE).

```sql
SELECT wait_event || ':' || wait_event_type AS type, count(*) AS number_of_occurences
  FROM pg_stat_activity
 WHERE state != 'idle'
GROUP BY wait_event, wait_event_type
ORDER BY number_of_occurences DESC;
```

Salida de ejemplo cuando `pg_sleep` se ejecuta en una consulta independiente de manera simultánea:

```
┌─────────────────┬──────────────────────┐
│      type       │ number_of_occurences │
├─────────────────┼──────────────────────┤
│ ∅               │                    1 │
│ PgSleep:Timeout │                    1 │
└─────────────────┴──────────────────────┘
```

## <a name="index-hit-rate"></a>Tasa de aciertos del índice

Esta consulta le proporcionará la tasa de aciertos del índice en todos los nodos. Este valor es útil para determinar la frecuencia con que se usan los índices al realizar consultas.
Un valor del 95 % o superior es ideal.

``` postgresql
-- on coordinator
SELECT 100 * (sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit) AS index_hit_rate
  FROM pg_statio_user_indexes;

-- on workers
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT 100 * (sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit) AS index_hit_rate
    FROM pg_statio_user_indexes;
$cmd$);
```

Salida de ejemplo:

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 96.0           │
│ 10.0.0.20 │ 98.0           │
└───────────┴────────────────┘
```

## <a name="cache-hit-rate"></a>Tasa de aciertos de caché

Normalmente, la mayoría de las aplicaciones tienen acceso a una pequeña fracción de los datos totales a la vez. PostgreSQL mantiene los datos de acceso frecuente en memoria para evitar lecturas lentas del disco. Puede ver estadísticas sobre ello en la vista [pg_statio_user_tables](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STATIO-ALL-TABLES-VIEW).

Una medida importante es el porcentaje de datos procedentes de la memoria caché frente a los procedentes del disco en la carga de trabajo:

``` postgresql
-- on coordinator
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit)  AS heap_hit,
  100 * sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS cache_hit_rate
FROM
  pg_statio_user_tables;

-- on workers
SELECT nodename, result as cache_hit_rate
FROM run_command_on_workers($cmd$
  SELECT
    100 * sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS cache_hit_rate
  FROM
    pg_statio_user_tables;
$cmd$);
```

Salida de ejemplo:

```
┌───────────┬──────────┬─────────────────────┐
│ heap_read │ heap_hit │   cache_hit_rate    │
├───────────┼──────────┼─────────────────────┤
│         1 │      132 │ 99.2481203007518796 │
└───────────┴──────────┴─────────────────────┘
```

Si se encuentra con una relación significativamente inferior al 99 %, es probable que desee considerar la posibilidad de aumentar la memoria caché disponible para la base de datos.

## <a name="next-steps"></a>Pasos siguientes

* Conozca otras [tablas del sistema](reference-hyperscale-metadata.md) que son útiles para el diagnóstico
