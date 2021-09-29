---
title: 'Determinación del tamaño de la tabla: Hiperescala (Citus) y Azure Database for PostgreSQL'
description: Cómo encontrar el tamaño real de las tablas distribuidas en un grupo de servidores de Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 10426baece6b14984dc10a6eadc1d5fc19db382c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631900"
---
# <a name="determine-table-and-relation-size"></a>Determinación del tamaño de las tablas y las relaciones

La manera habitual de encontrar los tamaños de las tablas en PostgreSQL, `pg_total_relation_size`, notifica un tamaño muy por debajo al de las tablas distribuidas en Hiperescala (Citus).
Toda esta función se realiza en un grupo de servidores de Hiperescala (Citus) para mostrar el tamaño de las tablas en el nodo de coordinación.  En realidad, los datos de las tablas distribuidas residen en los nodos de trabajo (en particiones), no en el coordinador. Una medida verdadera del tamaño de la tabla distribuida se obtiene con la suma de los tamaños de las particiones. Hiperescala (Citus) proporciona funciones auxiliares para consultar esta información.

<table>
<colgroup>
<col width="40%" />
<col width="59%" />
</colgroup>
<thead>
<tr class="header">
<th>Función</th>
<th>Devoluciones</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size(relation_name)</td>
<td><ul>
<li>Tamaño de los datos reales de la tabla ("<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">bifurcación principal</a>").</li>
<li>Una relación puede ser el nombre de una tabla o de un índice.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size(relation_name)</td>
<td><ul>
<li><p>citus_relation_size plus:</p>
<blockquote>
<ul>
<li>tamaño de la <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">asignación de espacio libre</a></li>
<li>tamaño de la <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">asignación de visibilidad</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size(relation_name)</td>
<td><ul>
<li><p>citus_table_size plus:</p>
<blockquote>
<ul>
<li>tamaño de los índices</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Estas funciones son análogas a tres de las [funciones de tamaño de objeto](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) estándar de PostgreSQL, excepto que, si no se pueden conectar a un nodo, generan un error.

## <a name="example"></a>Ejemplo

Aquí se muestra cómo enumerar los tamaños de todas las tablas distribuidas:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Salida:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [escalar un grupo de servidores](howto-hyperscale-scale-grow.md) para que contenga más datos.
* Distinga los [tipos de tabla](concepts-hyperscale-nodes.md) en un grupo de servidores de Hiperescala (Citus).
