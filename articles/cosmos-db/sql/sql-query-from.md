---
title: Cláusula FROM en Azure Cosmos DB
description: Obtenga información sobre la sintaxis de SQL y un ejemplo de la cláusula FROM para Azure Cosmos DB. En este artículo también se muestran ejemplos para determinar el ámbito de los resultados y para obtener subelementos mediante la cláusula FROM.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 8d747d853e7439c20c3110a4cab84a4bb46a3b5a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206614"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Cláusula FROM en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

La cláusula FROM (`FROM <from_specification>`) es opcional, a menos que el origen se filtre o se proyecte posteriormente en la consulta. Una consulta como `SELECT * FROM Families` se enumera en todo el contenedor `Families`. También puede usar el identificador especial ROOT para el contenedor en lugar de usar el nombre del contenedor.

La cláusula `FROM` exige las reglas siguientes por consulta:

* Se puede establecer un alias para el contenedor, como `SELECT f.id FROM Families AS f` o simplemente `SELECT f.id FROM Families f`. Aquí `f` es el alias de `Families`. AS es una palabra clave opcional para [establecer un alias](sql-query-working-with-json.md#aliasing) para el identificador.  

* Una vez establecido un alias, el nombre de origen original no puede enlazarse. Por ejemplo, `SELECT Families.id FROM Families f` no es válido sintácticamente porque no puede establecerse el alias del identificador `Families` y ya no puede resolverse.  

* Todas las propiedades a las que se hace referencia deben ser completas para evitar todo enlace ambiguo si no se aplica un cumplimiento estricto del esquema. Por ejemplo, `SELECT id FROM Families f` no es válido sintácticamente porque la propiedad `id` no está enlazada.

## <a name="syntax"></a>Sintaxis
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=
        ROOT
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumentos
  
- `<from_source>`  
  
  Especifica un origen de datos, con o sin alias. Si no se especifica, se deducirá de `<container_expression>` con las siguientes reglas:  
  
-  Si la expresión es un nombre de contenedor, se utilizará container_name como alias.  
  
-  Si la expresión es `<container_expression>`, se utilizará property_name como alias. Si la expresión es un nombre de contenedor, se utilizará container_name como alias.  
  
- AS `input_alias`.  
  
  Especifica que `input_alias` es un conjunto de valores que la expresión del contenedor subyacente ha devuelto.  
 
- `input_alias` IN  
  
  Especifica que `input_alias` debe representar el conjunto de valores obtenidos al recorrer en iteración todos los elementos de las matrices que devuelve la expresión del contenedor subyacente. Se omite cualquier valor que la expresión de contenedor subyacente devuelva que no sea una matriz.  
  
- `<container_expression>`  
  
  Especifica la expresión de contenedor que se usará para recuperar los documentos.  
  
- `ROOT`  
  
  Especifica que debe recuperarse ese documento predeterminado del contenedor actualmente conectado.  
  
- `container_name`  
  
  Especifica que debe recuperarse ese documento del contenedor indicado. El nombre del contenedor debe coincidir con el nombre del contenedor conectado actualmente.  
  
- `input_alias`  
  
  Especifica que debe recuperarse ese documento desde otro origen que define el alias proporcionado.  
  
- `<container_expression> '.' property_name`  
  
  Especifica que se debe acceder a la propiedad `property_name` para recuperar ese documento.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica que debe recuperarse ese documento mediante el acceso a la propiedad `property_name` o al elemento de matriz array_index de todos los documentos que ha recuperado la expresión de contenedor específica.  
  
## <a name="remarks"></a>Observaciones
  
Todos los alias proporcionados o deducidos en `<from_source>(` deben ser únicos. La sintaxis de property_name de `<container_expression>.` es la misma que la de `<container_expression>' ['"property_name"']'`. Sin embargo, esta última puede usarse si un nombre de propiedad contiene algún carácter que no sea un identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Control de propiedades que faltan, elementos de matriz que faltan y valores sin definir
  
Si una expresión de contenedor accede a propiedades o elementos de matriz y el valor no existe, ese valor se omitirá y no se seguirá procesando.  
  
### <a name="container-expression-context-scoping"></a>Ámbito de contexto de la expresión del contenedor  
  
El ámbito de las expresiones de contenedor puede ser de contenedor o de documento:  
  
- Una expresión es de ámbito de contenedor si el origen subyacente de la expresión de contenedor es ROOT o `container_name`. Este tipo de expresión representa un conjunto de documentos que se recuperan directamente del contenedor y no dependen del procesamiento de otras expresiones de contenedor.  
  
- Una expresión es de ámbito de documento si el origen subyacente de la expresión de contenedor es el valor `input_alias` introducido anteriormente en la consulta. Esta expresión representa un conjunto de documentos obtenidos al evaluar la expresión de contenedor en el ámbito de cada documento perteneciente al conjunto asociado con el contenedor del alias. El conjunto de resultados será una combinación de los conjuntos obtenidos al evaluar la expresión de contenedor para cada uno de los documentos del conjunto subyacente.

## <a name="examples"></a>Ejemplos

### <a name="get-subitems-by-using-the-from-clause"></a>Obtener subelementos mediante la cláusula FROM

La cláusula FROM puede reducir el origen a un subconjunto menor. Para enumerar únicamente un subárbol en cada elemento, la subraíz puede convertirse en el origen, como se muestra en el ejemplo siguiente:

```sql
    SELECT *
    FROM Families.children
```

Los resultados son:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

La consulta anterior usaba una matriz como origen, pero también puede usar un objeto. La consulta se considera cualquier valor JSON definido válido en el origen para incluirlo en el resultado. En el ejemplo siguiente, se excluirían `Families` que no tienen un valor `address.state`.

```sql
    SELECT *
    FROM Families.address.state
```

Los resultados son:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula WHERE](sql-query-where.md)
