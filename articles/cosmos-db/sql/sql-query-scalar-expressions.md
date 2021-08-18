---
title: Expresiones escalares en consultas SQL de Azure Cosmos DB
description: Obtenga información sobre la sintaxis SQL de las expresiones escalares para Azure Cosmos DB. En este artículo también se describe cómo combinar expresiones escalares en expresiones complejas mediante el uso de operadores.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 239cc92edc64092556408b90c666dbe27d76b136
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206800"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Expresiones escalares en consultas SQL de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

La [cláusula SELECT](sql-query-select.md) admite expresiones escalares. Una expresión escalar es una combinación de símbolos y operadores que se puede evaluar para obtener un solo valor. Entre los ejemplos de expresiones escalares se incluyen: constantes, referencias de propiedad, referencias de elementos de matriz, referencias de alias o llamadas de función. Las expresiones escalares se pueden combinar en expresiones complejas con operadores.

## <a name="syntax"></a>Sintaxis
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumentos
  
- `<constant>`  
  
   Representa un valor constante. Vea la sección [Constantes](sql-query-constants.md) para obtener más información.  
  
- `input_alias`  
  
   Representa un valor definido por `input_alias`, introducido en la cláusula `FROM`.  
  Este valor nunca será **undefined**, los valores **undefined** se omiten de la entrada.  
  
- `<scalar_expression>.property_name`  
  
   Representa un valor de propiedad de un objeto. Si la propiedad no existe o se hace referencia a ella con un valor que no es un objeto, la expresión se evalúa como valor **undefined**.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representa un valor de propiedad con nombre `property_name` o un elemento de la matriz con el índice `array_index` de una matriz. Si la propiedad o el índice de matriz no existen o se hace referencia a ellos con un valor que no es un objeto o una matriz, la expresión se evalúa como valor undefined.  
  
- `unary_operator <scalar_expression>`  
  
   Representa un operador que se aplica a un único valor. Consulte la sección [Operadores](sql-query-operators.md) para más información.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representa un operador que se aplica a dos valores. Consulte la sección [Operadores](sql-query-operators.md) para más información.  
  
- `<scalar_function_expression>`  
  
   Representa un valor definido por el resultado de una llamada de función.  
  
- `udf_scalar_function`  
  
   Nombre de la función escalar definida por el usuario.  
  
- `builtin_scalar_function`  
  
   Nombre de la función escalar incorporada.  
  
- `<create_object_expression>`  
  
   Representa un valor obtenido al crear un objeto con propiedades especificadas y sus valores.  
  
- `<create_array_expression>`  
  
   Representa un valor obtenido al crear una matriz con valores especificados como elementos.  
  
- `parameter_name`  
  
   Representa un valor del nombre de parámetro especificado. Los nombres de parámetro deben tener un único \@ como primer carácter.  
  
## <a name="remarks"></a>Comentarios
  
  Todos los argumentos deben estar definidos al llamar a una función escalar incorporada o definida por el usuario. Si alguno de los argumentos no está definido, no se llamará a la función y el resultado será undefined.  
  
  Al crear un objeto, se omitirá cualquier propiedad a la que se le haya asignado un valor undefined y no se incluirá en el objeto creado.  
  
  Al crear una matriz, se omitirá cualquier elemento al que se le haya asignado un valor **undefined** y no se incluirá en el objeto creado. Esto hará que el siguiente elemento definido ocupe su lugar de manera que la matriz creada no tenga índices omitidos.  

## <a name="examples"></a>Ejemplos

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Los resultados son:

```json
    [{
      "$1": 1.33333
    }]
```

En la consulta siguiente, el resultado de la expresión escalar es un valor booleano:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Los resultados son:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Azure Cosmos DB](../introduction.md)
- [Ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Subconsultas](sql-query-subquery.md)