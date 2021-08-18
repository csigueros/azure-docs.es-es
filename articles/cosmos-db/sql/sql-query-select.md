---
title: Cláusula SELECT en Azure Cosmos DB
description: Obtenga información sobre la cláusula SELECT de SQL para Azure Cosmos DB. Use SQL como lenguaje de consulta de JSON para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 8fd6174d13f45deac2eaddb6c98441fad5ce5e4d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206663"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Cláusula SELECT en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Todas las consultas constan de una cláusula `SELECT` y cláusulas [FROM](sql-query-from.md) y [WHERE](sql-query-where.md) opcionales, según los estándares de SQL ANSI. Normalmente, se enumera el origen en la cláusula `FROM` y la cláusula `WHERE` aplica un filtro en el origen para recuperar un subconjunto de elementos JSON. La cláusula `SELECT` luego proyecta los valores JSON solicitados en la lista seleccionada.

## <a name="syntax"></a>Sintaxis

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argumentos
  
- `<select_specification>`  

  Propiedades o valor que se van a seleccionar para los resultados.  
  
- `'*'`  

  Especifica que se debe recuperar el valor sin cambios. En particular, si el valor procesado es un objeto, se recuperarán todas las propiedades.  
  
- `<object_property_list>`  
  
  Especifica la lista de propiedades que se recuperan. Los valores devueltos serán objetos con las propiedades especificadas.  
  
- `VALUE`  

  Especifica que se debe recuperar el valor JSON en lugar del objeto JSON completo. Esto, a diferencia de `<property_list>`, no encapsula el valor proyectado en un objeto.  

- `DISTINCT`
  
  Especifica que se deben quitar los duplicados de las propiedades proyectadas.  

- `<scalar_expression>`  

  Expresión que representa el valor que hay que calcular. Consulte la sección [Expresiones escalares](sql-query-scalar-expressions.md) para más información.  

## <a name="remarks"></a>Observaciones

La sintaxis `SELECT *` solo es válida si la cláusula FROM ha declarado exactamente un alias. `SELECT *` proporciona una proyección de identidad, que puede resultar útil si no se necesitan proyecciones. SELECT * solo es válida si se especifica cláusula FROM y se introdujo solo un origen de entrada.  
  
`SELECT <select_list>` y `SELECT *` son código sintáctico y pueden expresarse también mediante instrucciones SELECT sencillas, como se muestra a continuación.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   equivale a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   equivale a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Ejemplos

El siguiente ejemplo de consulta SELECT devuelve `address` de `Families` cuyo `id` coincide con `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Los resultados son:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción](sql-query-getting-started.md)
- [Ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula WHERE](sql-query-where.md)
