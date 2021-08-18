---
title: Trabajo con JSON en Azure Cosmos DB
description: Obtenga información sobre cómo consultar las propiedades JSON anidadas y tener acceso a ellas, así como usar caracteres especiales en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 5a4b3a574d41221718ab4b7e6c9c3424d808dc98
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206611"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Trabajo con JSON en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En la API de SQL (Core) de Azure Cosmos DB, los elementos se almacenan como JSON. El sistema de tipos y las expresiones se restringen para tratar únicamente con tipos JSON. Para más información, consulte la [especificación de JSON](https://www.json.org/).

Resumiremos algunos aspectos importantes del trabajo con JSON:

- Los objetos JSON siempre comienzan con una llave de apertura `{` y terminan con una llave de cierre `}`.
- Puede tener propiedades JSON [anidadas](#nested-properties) entre sí.
- Los valores de propiedad JSON pueden ser matrices.
- Los nombres de propiedad JSON distinguen entre mayúsculas y minúsculas.
- El nombre de la propiedad JSON puede ser cualquier valor de cadena (incluidos espacios o caracteres que no son letras).

## <a name="nested-properties"></a>Propiedades anidadas

Puede acceder a JSON anidado con un descriptor de acceso de punto. Puede usar las propiedades JSON anidadas en las consultas de la misma manera que puede usar cualquier otra propiedad.

Este es un documento con JSON anidado:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

En este caso, las propiedades `state`, `country` y `city` están anidadas en la propiedad `address`.

En el ejemplo siguiente, se proyectan dos propiedades anidadas: `f.address.state` y `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Los resultados son:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Uso con matrices

Además de las propiedades anidadas, JSON también admite matrices.

Este es un documento de ejemplo con una matriz:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Al trabajar con matrices, puede tener acceso a un elemento específico dentro de la matriz haciendo referencia a su posición:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

Sin embargo, en la mayoría de los casos, usará una [subconsulta](sql-query-subquery.md) o [autocombinación](sql-query-join.md) al trabajar con matrices.

Por ejemplo, este es un documento que muestra el saldo diario de la cuenta bancaria de un cliente.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Si deseara ejecutar una consulta que mostrase todos los clientes que hubieran tenido un saldo negativo en algún momento, podría usar [EXISTS](sql-query-subquery.md#exists-expression) con una subconsulta:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Diferencia entre NULL y undefined

Si una propiedad no está definida en un elemento, su valor es `undefined`. Una propiedad con el valor `null` se debe definir explícitamente y asignar el valor `null`.

Por ejemplo, observe este elemento de ejemplo:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

En este ejemplo, la propiedad `isRegistered` tiene un valor de `undefined` porque se omite del elemento. La propiedad `creationDate` tiene el valor `null`.

Azure Cosmos DB admite dos funciones del sistema útiles para la comprobación de tipos para las propiedades `null` y `undefined`:

* [IS_NULL](sql-query-is-null.md): comprueba si el valor de una propiedad es `null`
* [IS_DEFINED](sql-query-is-defined.md): comprueba si se ha definido un valor de propiedad

Más información sobre los [operadores admitidos](sql-query-operators.md) y su comportamiento para los valores `null` y `undefined`.

## <a name="reserved-keywords-and-special-characters-in-json"></a>Palabras clave reservadas y caracteres especiales en JSON

Puede acceder a las propiedades mediante el operador de la propiedad entre comillas `[]`. Por ejemplo, `SELECT c.grade` and `SELECT c["grade"]` son equivalentes. Esta sintaxis es útil para crear una secuencia de escape para una propiedad que contiene espacios en blanco, caracteres especiales o que tiene el mismo nombre que una palabra clave SQL o una palabra reservada.

Por ejemplo, a continuación se muestra un documento con una propiedad denominada `order` y una propiedad `price($)` que contiene caracteres especiales:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Si ejecuta una consulta que incluya la propiedad `order` o `price($)`, recibirá un error de sintaxis.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

El resultado es el siguiente:

`
Syntax error, incorrect syntax near 'order'
`

Debe volver a escribir las mismas consultas como se indica a continuación:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Expresiones JSON

La proyección también admite expresiones JSON, como se muestra en el siguiente ejemplo:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Los resultados son:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

En el ejemplo anterior, la cláusula `SELECT` tiene que crear un objeto JSON y, puesto que el ejemplo no proporciona ninguna clave, la cláusula usa el nombre de variable del argumento implícito `$1`. La consulta siguiente devuelve dos variables de argumentos implícitos: `$1` y `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Los resultados son:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Establecimiento de alias

Puede establecer alias para valores de manera explícita en las consultas. Si una consulta tiene dos propiedades con el mismo nombre, establezca alias para cambiar el nombre de una o ambas propiedades de modo que se elimine su ambigüedad en el resultado proyectado.

### <a name="examples"></a>Ejemplos

La palabra clave `AS` usada para los alias es opcional, tal como se muestra en el ejemplo siguiente al proyectar el segundo valor como `NameInfo`:

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Los resultados son:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Alias con palabras clave reservadas o caracteres especiales

No se pueden usar alias para proyectar un valor como un nombre de propiedad con un espacio, un carácter especial o una palabra reservada. Si deseara cambiar la proyección de un valor para que, por ejemplo, tuviera un nombre de propiedad con un espacio, podría usar una [expresión JSON](#json-expressions).

Este es un ejemplo:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula WHERE](sql-query-where.md)
