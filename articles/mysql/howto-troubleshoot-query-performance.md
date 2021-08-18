---
title: Solución de problemas relacionados con el rendimiento de consultas en Azure Database for MySQL
description: Aprenda a usar EXPLAIN para solucionar problemas de rendimiento de consultas en Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: e4945f6013c05135ea906fb813017ca152aa9d62
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088339"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Uso de EXPLAIN para solucionar problemas relacionados con el rendimiento de consultas en Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

**EXPLAIN** es una herramienta útil a la hora de optimizar consultas. Las instrucciones de EXPLAIN se pueden usar para obtener información sobre cómo se ejecutan las instrucciones SQL. La salida siguiente muestra un ejemplo de la ejecución de una instrucción de EXPLAIN.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Como se puede ver en este ejemplo, el valor de *clave* es NULL. Esta salida significa que MySQL no encuentra ningún índice optimizado para la consulta y realiza un recorrido de tabla completo. Vamos a optimizar esta consulta mediante la adición de un índice en la columna **ID**.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

La nueva herramienta EXPLAIN muestra que MySQL utiliza ahora un índice para limitar el número de filas a 1, lo cual permite, a su vez, reducir drásticamente el tiempo de búsqueda.

## <a name="covering-index"></a>Índice de cobertura

Un índice de cobertura consta de todas las columnas de una consulta del índice para reducir la recuperación de valores de las tablas de datos. Este es un ejemplo en la siguiente instrucción **GROUP BY**.

```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Como se puede ver en la salida, MySQL no utiliza índices porque no hay índices adecuados disponibles. También aparece *Using temporary; Using file sort*, lo que significa que MySQL crea una tabla temporal para satisfacer la cláusula **GROUP BY**.

La creación de un índice solo en la columna **c2** no supone ninguna diferencia y MySQL todavía necesita crear una tabla temporal:

```sql
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

En este caso, se puede crear un **índice de cobertura** en **c1** y **c2**, mediante el cual se puede agregar el valor **c2** directamente en el índice para eliminar búsquedas de datos adicionales.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Como puede ver en el ejemplo anterior de EXPLAIN, MySQL usa ahora el índice de cobertura y evita la creación de una tabla temporal. 

## <a name="combined-index"></a>Índice combinado

Un índice combinado consta de valores de varias columnas y puede considerarse como una matriz de filas que se ordenan mediante la concatenación de valores de las columnas indexadas.  Este método puede ser útil en una instrucción **GROUP BY**.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MySQL realiza una operación de *ordenación de archivos* que es bastante lenta, especialmente cuando tiene que ordenar muchas filas. Para optimizar esta consulta, se puede crear un índice combinado en las columnas que se están ordenando.

```sql
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

Ahora EXPLAIN muestra como MySQL puede usar un índice combinado para evitar una ordenación adicional dado que el índice ya está ordenado.

## <a name="conclusion"></a>Conclusión

El uso de EXPLAIN y de distintos tipos de índice puede aumentar el rendimiento significativamente. El hecho de tener un índice en la tabla no significa necesariamente que MySQL pueda usarlo para sus consultas. Valide siempre sus suposiciones con EXPLAIN y optimice las consultas con índices.

## <a name="next-steps"></a>Pasos siguientes

- Para buscar respuestas de otros usuarios a sus preguntas o publicar una nueva pregunta o respuesta, visite [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
