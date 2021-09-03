---
title: Cláusula GROUP BY en Azure Cosmos DB
description: Obtenga información sobre la cláusula GROUP BY de SQL para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tisande
ms.openlocfilehash: 2aa08f0d2cf0ccb78d231e30e7c60cde47ba4c86
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206732"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Cláusula GROUP BY en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

La cláusula GROUP BY divide los resultados de la consulta de acuerdo con los valores de una o más propiedades especificadas.

## <a name="syntax"></a>Sintaxis

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumentos

- `<scalar_expression_list>`

   Especifica las expresiones que se usarán para dividir los resultados de la consulta.

- `<scalar_expression>`
  
   Se permite cualquier expresión escalar, excepto para las subconsultas escalares y los agregados escalares. Cada expresión escalar debe contener al menos una referencia de propiedad. No hay ningún límite en cuanto al número de expresiones individuales o la cardinalidad de cada expresión.

## <a name="remarks"></a>Observaciones
  
  Cuando una consulta utiliza una cláusula GROUP BY, la cláusula SELECT solo puede contener el subconjunto de propiedades y las funciones del sistema incluidas en la cláusula GROUP BY. Una excepción son las [funciones de agregado](sql-query-aggregate-functions.md), que pueden aparecer en la cláusula SELECT sin que se incluyan en la cláusula GROUP BY. También puede incluir siempre valores literales en la cláusula SELECT.

  La cláusula GROUP BY debe estar después de las cláusulas SELECT, FROM y WHERE y antes de la cláusula OFFSET LIMIT. Actualmente no se puede usar GROUP BY con una cláusula ORDER BY, si bien está previsto que sea posible hacerlo en el futuro.

  La cláusula GROUP BY no permite nada de lo siguiente:
  
- Propiedades de alias o funciones del sistema de alias (el alias sigue estando permitido en la cláusula SELECT)
- Subconsultas
- Funciones del sistema de agregado (solo se permiten en la cláusula SELECT)

No se admiten las consultas con una función del sistema agregada y una subconsulta con `GROUP BY`. Por ejemplo, la siguiente consulta no se admite:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

Además, las consultas de `GROUP BY` entre particiones pueden tener un máximo de 21 [funciones de agregado del sistema](sql-query-aggregate-functions.md). 

## <a name="examples"></a>Ejemplos

En estos ejemplos se usa un [conjunto de datos de nutrición](https://github.com/AzureCosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) de ejemplo.

Esta es una consulta que devuelve el recuento total de elementos de cada grupo foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Estos son algunos de los resultados (la palabra clave TOP se usa para limitar los resultados):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Esta consulta tiene dos expresiones que se usan para dividir los resultados:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Estos son algunos de los resultados:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Esta consulta tiene una función del sistema en la cláusula GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Estos son algunos de los resultados:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Esta consulta usa palabras clave y funciones del sistema en la expresión de propiedad del elemento:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Los resultados son:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Funciones de agregado](sql-query-aggregate-functions.md)
