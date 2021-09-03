---
title: Subconsultas SQL para Azure Cosmos DB
description: Obtenga información acerca de las subconsultas SQL y sus casos de uso comunes y los distintos tipos de subconsultas en Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tisande
ms.openlocfilehash: 5805c52581a1b8cd0320396d6b6c4eb07c57e3a8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206694"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Ejemplos de subconsultas SQL para Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Una subconsulta es una consulta anidada dentro de otra consulta. Una subconsulta también se denomina consulta interna o selección interna. La instrucción que contiene una subconsulta normalmente se llama consulta externa.

En este artículo se describen las subconsultas SQL y sus casos de uso comunes en Azure Cosmos DB. Todas las consultas de ejemplo de esta documentación se pueden ejecutar en [un conjunto de datos de nutrición de ejemplo](https://github.com/AzureCosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="types-of-subqueries"></a>Tipos de subconsultas

Existen dos tipos principales de subconsultas:

* **Correlacionado**: una subconsulta que hace referencia a valores de la consulta externa. La subconsulta se evalúa una vez para cada fila que procesa la consulta externa.
* **Sin correlacionar**: una subconsulta que es independiente de la consulta externa. Se puede ejecutar por sí misma sin depender de la consulta externa.

> [!NOTE]
> Azure Cosmos DB admite solo subconsultas correlacionadas.

Las subconsultas se pueden clasificar aún más en función del número de filas y columnas que devuelven. Existen tres tipos:
* **Tabla**: devuelve varias filas y varias columnas.
* **Valores múltiples**: devuelve varias filas y una sola columna.
* **Escalar**: devuelve una sola fila y una sola columna.

Las consultas SQL en Azure Cosmos DB siempre devuelven una sola columna (ya sea un valor simple o un documento complejo). Por lo tanto, solo las subconsultas escalares y multivalor se aplican en Azure Cosmos DB. Puede usar una subconsulta multivalor solo en la cláusula FROM como expresión relacional. Puede usar una subconsulta escalar como expresión escalar en la cláusula SELECT o WHERE, o como expresión relacional en la cláusula FROM.

## <a name="multi-value-subqueries"></a>Subconsultas multivalor

Las subconsultas multivalor devuelven un conjunto de documentos y siempre se usan dentro de la cláusula FROM. Se usan para:

* Optimizar las expresiones JOIN. 
* Evaluar expresiones costosas una vez y hacer referencia varias veces.

## <a name="optimize-join-expressions"></a>Optimizar las expresiones JOIN

Las subconsultas multivalor pueden optimizar las expresiones JOIN mediante la inserción de predicados después de cada expresión select-many, en lugar de hacerlo después de todas las combinaciones cruzadas en la cláusula WHERE.

Considere la consulta siguiente:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Para esta consulta, el índice coincidirá con cualquier documento que tenga una etiqueta con el nombre "infant formula". Es un elemento nutrient con un valor entre 0 y 10, y un elemento serving con una cantidad mayor que 1. La expresión JOIN aquí generará el producto cruzado de todos los elementos de las matrices tags, nutrients y servings para cada documento coincidente antes de aplicar cualquier filtro. 

Luego, la cláusula WHERE aplicará el predicado de filtro en cada tupla < c, t, n, s>. Por ejemplo, si un documento coincidente tenía 10 elementos en cada una de las tres matrices, se expandirá a 1×10×10×10 (es decir, 1000) tuplas. Usar subconsultas aquí puede ayudar a filtrar los elementos de matrices combinadas antes de unirse a la siguiente expresión.

Esta consulta es equivalente a la anterior, pero usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Se supone que solo un elemento de la matriz tags coincide con el filtro, y hay cinco elementos tanto para la matriz de nutrients como la de servings. Las expresiones JOIN luego se expandirán a 1×1×5×5 = 25 elementos, en lugar de los 1000 elementos de la primera consulta.

## <a name="evaluate-once-and-reference-many-times"></a>Evaluar una vez y hacer referencia varias veces

Las subconsultas pueden ayudar a optimizar las consultas con expresiones costosas, como funciones definidas por el usuario (UDF), cadenas complejas o expresiones aritméticas. Puede usar una subconsulta junto con una expresión JOIN para evaluar la expresión una vez, pero hacer referencia a ella varias veces.

La siguiente consulta ejecuta la UDF `GetMaxNutritionValue` dos veces:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Esta es una consulta equivalente que ejecuta la UDF solo una vez:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tenga en cuenta el comportamiento del producto cruzado de las expresiones JOIN. Si la expresión UDF se puede evaluar como undefined, debe asegurarse de que la expresión JOIN siempre produzca una sola fila mediante la devolución de un objeto de la subconsulta en lugar del valor directamente.
>

Este es un ejemplo similar que devuelve un objeto en lugar de un valor:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

El enfoque no se limita a las UDF. Se aplica a cualquier expresión potencialmente costosa. Por ejemplo, puede adoptar el mismo enfoque con la función matemática `avg`:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Imitar la combinación con datos de referencia externos

A menudo, es posible que deba hacer referencia a datos estáticos que no suelen cambiar, por ejemplo, unidades de medida o códigos de país. Es mejor no duplicar dichos datos para cada documento. Al evitar esta duplicación, ahorra en almacenamiento y mejora el rendimiento de escritura, ya que mantiene un tamaño de documento más pequeño. Puede usar una subconsulta para imitar la semántica de combinación interna con una colección de datos de referencia.

Por ejemplo, considere este conjunto de datos de referencia:

| **Unidad** | **Nombre**            | **Multiplicador** | **Unidad base** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogramo            | 1,00E-09       | Gramo          |
| µg       | Microgramo           | 1,00E-06       | Gramo          |
| mg       | Miligramo           | 1,00E-03       | Gramo          |
| e        | Gramo                | 1,00E+00       | Gramo          |
| kg       | Kilogramo            | 1,00E+03       | Gramo          |
| Mg       | Megagramo            | 1,00E+06       | Gramo          |
| Gg       | Gigagramo            | 1,00E+09       | Gramo          |
| nJ       | Nanojulio           | 1,00E-09       | Joule         |
| µJ       | Microjulio          | 1,00E-06       | Joule         |
| mJ       | Milijulio          | 1,00E-03       | Joule         |
| J        | Joule               | 1,00E+00       | Joule         |
| kJ       | Kilojulio           | 1,00E+03       | Joule         |
| MJ       | Megajulio           | 1,00E+06       | Joule         |
| GJ       | Gigajulio           | 1,00E+09       | Joule         |
| cal      | Caloría             | 1,00E+00       | Caloría       |
| kcal     | Caloría             | 1,00E+03       | Caloría       |
| UI       | Unidades internacionales |                |               |


La siguiente consulta imita la combinación con estos datos para que agregue el nombre de la unidad a la salida:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Subconsultas escalares

Una expresión de subconsulta escalar es una subconsulta que se evalúa como valor único. El valor de la expresión de subconsulta escalar es el valor de la proyección (cláusula SELECT) de la subconsulta.  Puede usar una expresión de subconsulta escalar en muchos lugares donde sea válida una expresión escalar. Por ejemplo, puede usar una subconsulta escalar en cualquier expresión en las cláusulas SELECT y WHERE.

Aunque el uso de una subconsulta escalar no siempre ayuda a la optimización. Por ejemplo, pasar una subconsulta escalar como argumento a un sistema o a funciones definidas por el usuario no brinda ninguna ventaja en el consumo de recursos de unidad (RU) ni en la latencia.

Las subconsultas escalares se pueden clasificar, además, de esta manera:
* Subconsultas escalares de expresión simple
* Subconsultas escalares agregadas

## <a name="simple-expression-scalar-subqueries"></a>Subconsultas escalares de expresión simple

Una subconsulta escalar de expresión simple es una subconsulta correlacionada que tienen una cláusula SELECT que no contiene ninguna expresión agregada. Estas subconsultas no brindan ninguna ventaja de optimización porque el compilador las convierte en una expresión simple mayor. No existe ningún contexto correlacionado entre las consultas internas y externas.

Estos son algunos ejemplos:

**Ejemplo 1**

```sql
SELECT 1 AS a, 2 AS b
```

Puede reescribir esta consulta, con una subconsulta escalar de expresión simple, como:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Ambas consultas producen este resultado:

```json
[
  { "a": 1, "b": 2 }
]
```

**Ejemplo 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Puede reescribir esta consulta, con una subconsulta escalar de expresión simple, como:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Resultado de la consulta:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Ejemplo 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Puede reescribir esta consulta, con una subconsulta escalar de expresión simple, como:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Resultado de la consulta:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Subconsultas escalares agregadas

Una subconsulta escalar agregada es una subconsulta que tiene una función agregada en su proyección o filtro que se evalúa como valor único.

**Ejemplo 1:**

Esta es una subconsulta con una única expresión de función agregada en su proyección:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Resultado de la consulta:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Ejemplo 2**

Esta es una subconsulta con varias expresiones de función agregada:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Resultado de la consulta:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Ejemplo 3**

Esta es una consulta con una subconsulta agregada tanto en la proyección como en el filtro:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Resultado de la consulta:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Una manera más eficaz de escribir esta consulta es combinar en la subconsulta y hacer referencia al alias de la subconsulta tanto en la cláusula SELECT como en la WHERE. Esta consulta es más eficaz porque tiene que ejecutar la subconsulta solo dentro de la instrucción de combinación, y no en la proyección y en el filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Expresión EXISTS

Azure Cosmos DB admite las expresiones EXISTS. Se trata de una subconsulta escalar agregada integrada en la API de SQL de Azure Cosmos DB. EXISTS es una expresión booleana que toma una expresión de subconsulta y devuelve true si la subconsulta devuelve alguna fila. De lo contrario, devuelve false.

Dado que la API de SQL de Azure Cosmos DB no distingue entre expresiones booleanas y otras expresiones escalares, puede usar EXISTS en cláusulas SELECT y WHERE. Esto es a diferencia de T-SQL, donde una expresión booleana (por ejemplo, EXISTS, BETWEEN e IN) está restringida al filtro.

Si la subconsulta EXISTS devuelve un valor único que está indefinido, EXISTS se evaluará como false. Por ejemplo, considere la siguiente consulta que se evalúa como false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Si se omite la palabra clave VALUE en la subconsulta anterior, la consulta se evaluará como true:
```sql
SELECT EXISTS (SELECT undefined) 
```

La subconsulta enmarcará la lista de valores en la lista seleccionada en un objeto. Si la lista seleccionada no tiene valores, la subconsulta devolverá el valor único "{}". Este valor está definido, por lo que EXISTS se evalúa como true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Ejemplo: reescritura de ARRAY_CONTAINS y JOIN como EXISTS.

Un caso de uso común de ARRAY_CONTAINS es para filtrar un documento por la existencia de un elemento en una matriz. En este caso, vamos a ver si la matriz tags contiene un elemento denominado "orange".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Puede reescribir la misma consulta para usar EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Además, ARRAY_CONTAINS solo puede comprobar si un valor es igual a cualquier elemento dentro de una matriz. Si necesita filtros más complejos en las propiedades de la matriz, use JOIN.

Considere la siguiente consulta que filtra según las unidades y propiedades de `nutritionValue` en la matriz: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Para cada uno de los documentos de la colección, se realiza un producto cruzado con los elementos de la matriz. Esta operación JOIN permite filtrar según las propiedades dentro de la matriz. Sin embargo, el consumo de RU de la consulta será alto. Por ejemplo, si 1000 documentos tenían 100 elementos en cada matriz, se expandirá a 1000×100 (es decir, 100 000) tuplas.

Usar EXISTS puede ayudar a evitar este costoso producto cruzado:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

En este caso, filtra por elementos de la matriz dentro de la subconsulta EXISTS. Si un elemento de la matriz coincide con el filtro, lo proyecta y EXISTS se evalúa como true.

También puede establecer un alias de EXISTS y hacer referencia a él en la proyección:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Resultado de la consulta:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Expresión ARRAY

Puede usar la expresión ARRAY para proyectar los resultados de una consulta como matriz. Puede usar esta expresión solo dentro de la cláusula SELECT de la consulta.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Resultado de la consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Al igual que con las demás subconsultas, son posibles los filtros con la expresión ARRAY.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Resultado de la consulta:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Las expresiones de la matriz también pueden ir después de la cláusula FROM en las subconsultas.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Resultado de la consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Pasos siguientes

- [Ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelado de datos de documentos](../modeling-data.md)
