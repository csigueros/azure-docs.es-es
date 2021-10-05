---
title: Palabras clave de SQL para Azure Cosmos DB
description: Obtenga información sobre las palabras clave de SQL para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: 680b383cee86ec4233579b9305c47e60ad4d5ff4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625357"
---
# <a name="keywords-in-azure-cosmos-db"></a>Palabras clave en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo se detallan las palabras clave que se pueden usar en consultas SQL de Azure Cosmos DB.

## <a name="between"></a>BETWEEN

Puede usar la palabra clave `BETWEEN` para expresar consultas en intervalos de cadenas o valores numéricos. Por ejemplo, la consulta siguiente devuelve todos los elementos de la familia en los que el curso del primer hijo se encuentre entre 1 y 5, ambos incluidos.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

También puede usar la palabra clave `BETWEEN` en la cláusula `SELECT`, como en el ejemplo siguiente.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

En la API de SQL, a diferencia de SQL ANSI, puede expresar consultas en intervalos en propiedades de tipos combinados. Por ejemplo, `grade` podría ser un número, como `5`, en algunos elementos y una cadena, como `grade4`, en otros. En estos casos, al igual que en JavaScript, una comparación entre los dos tipos distintos da como resultado `Undefined`, por lo que el elemento se omite.

## <a name="distinct"></a>DISTINCT

La palabra clave `DISTINCT` elimina los duplicados en la proyección de la consulta.

En este ejemplo, la consulta proyecta los valores para cada apellido:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Los resultados son:

```json
[
    "Andersen"
]
```

También puede proyectar objetos únicos. En este caso, el campo lastName no existe en uno de los dos documentos, por lo que la consulta devuelve un objeto vacío.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Los resultados son:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` también se puede usar en la proyección dentro de una subconsulta:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Esta consulta proyecta una matriz que contiene el valor de givenName de cada hijo con los duplicados quitados. Esta matriz tiene establecido un alias como ChildNames y se proyecta en la consulta externa.

Los resultados son:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Las consultas con una función del sistema agregada y una subconsulta con `DISTINCT` solo se admiten en versiones específicas del SDK. Por ejemplo, las consultas con la forma siguiente solo se admiten en las siguientes versiones del SDK:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

**Versiones del SDK compatibles:**

|**SDK**|**Versiones admitidas**|
|-------|----------------------|
|.NET SDK|3.18.0 o posterior|
|SDK de Java|4.19.0 o posterior|
|SDK de Node.js|No compatible|
|SDK de Python|No compatible|

Hay algunas restricciones adicionales para las consultas con una función del sistema agregada y una subconsulta con `DISTINCT`:

|**Restricción**|**Ejemplo**|
|-------|----------------------|
|Cláusula WHERE en la consulta externa|SELECT COUNT(1) FROM (SELECT DISTINCT VALUE c.lastName FROM c) AS lastName WHERE lastName = "Smith"|
|Cláusula ORDER BY en la consulta externa|SELECT VALUE COUNT(1) FROM (SELECT DISTINCT VALUE c.lastName FROM c) AS lastName ORDER BY lastName|
|Cláusula GROUP BY en la consulta externa|SELECT COUNT(1) as annualCount, d.year FROM (SELECT DISTINCT c.year, c.id FROM c) AS d GROUP BY d.year|
|Subconsulta anidada|SELECT COUNT(1) FROM (SELECT y FROM (SELECT VALUE StringToNumber(SUBSTRING(d.date, 0, 4 FROM (SELECT DISTINCT c.date FROM c) d) AS y WHERE y > 2012)|
|Varias agregaciones|SELECT COUNT(1) as AnnualCount, SUM(d.sales) as TotalSales FROM (SELECT DISTINCT c.year, c.sales, c.id FROM c) AS d|

## <a name="like"></a>LIKE

Devuelve un valor booleano en función de si una cadena de caracteres específica coincide con un patrón especificado. Un patrón puede contener caracteres normales y caracteres comodín. Puede escribir consultas equivalentes de forma lógica mediante la palabra clave `LIKE` o la función del sistema [RegexMatch](sql-query-regexmatch.md). Observará que se usan los mismos índices con independencia del que elija. Por lo tanto, debe usar `LIKE` si su sintaxis le es más útil más que las expresiones regulares.

> [!NOTE]
> Dado que `LIKE` puede utilizar un índice, debe [crear un índice de intervalo](./../index-policy.md) para las propiedades que se van a comparar con `LIKE`.

Puede usar los siguientes caracteres comodín con LIKE:

| Carácter comodín | Descripción                                                  | Ejemplo                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | Cualquier cadena de cero o más caracteres.                      | WHERE   c.description LIKE   “%SO%PS%”      |
| _   (carácter de subrayado)     | Cualquier carácter individual.                                       | WHERE   c.description LIKE   “%SO_PS%”      |
| [ ]                  | Cualquier carácter individual del intervalo ([a-f]) o del conjunto ([abcdef]) que se haya especificado. | WHERE   c.description LIKE   “%SO[t-z]PS%”  |
| [^]                  | Cualquier carácter individual que no se encuentre en el intervalo ([^a-f]) o el conjunto ([^abcdef]) que se haya especificado. | WHERE   c.description LIKE   “%SO[^abc]PS%” |


### <a name="using-like-with-the--wildcard-character"></a>Utilizar LIKE con el carácter comodín %

El carácter `%` coincide con cualquier cadena de cero o más caracteres. Por ejemplo, al colocar un valor `%` al principio y al final del patrón, la consulta siguiente devuelve todos los elementos que tienen una descripción que contiene `fruit`:

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

Si solo ha usado un carácter `%` al principio del patrón, solo se devolverán los elementos con una descripción que empiece con `fruit`:

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>Uso de NOT LIKE

En el ejemplo siguiente se devuelven todos los elementos con una descripción que no contiene `fruit`:

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>Uso de la cláusula ESCAPE

Puede buscar patrones que incluyan uno o más caracteres comodín mediante la cláusula ESCAPE. Por ejemplo, si quiere buscar descripciones que contengan la cadena `20-30%`, no debe interpretar el valor `%` como un carácter comodín.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>Uso de caracteres comodín como caracteres literales

Puede incluir caracteres comodín entre corchetes para tratarlos como caracteres literales. Cuando se escribe un carácter comodín entre corchetes, se quitan los atributos especiales. Estos son algunos ejemplos:

| Modelo           | Significado |
| ----------------- | ------- |
| LIKE   “20-30[%]” | 20-30%  |
| LIKE   “[_]n”     | _n      |
| LIKE   “[ [ ]”    | [       |
| LIKE   “]”        | ]       |

## <a name="in"></a>IN

Use la palabra clave IN para comprobar si un valor especificado coincide con algún valor de una lista. Por ejemplo, la consulta siguiente devuelve todos los elementos de la familia donde `id` es `WakefieldFamily` o `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

El ejemplo siguiente devuelve todos los elementos cuyo estado sea cualquiera de los valores especificados:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

La API de SQL proporciona compatibilidad con la [iteración en las matrices JSON](sql-query-object-array.md#Iteration), donde se ha agregado una nueva construcción mediante la palabra clave IN en el origen FROM.

Si incluye la clave de partición en el filtro `IN`, la consulta se filtrará automáticamente solo por las particiones pertinentes.

## <a name="top"></a>TOP

La palabra clave TOP devuelve los primeros `N` resultados de la consulta en un orden indefinido. Como procedimiento recomendado, use TOP con la cláusula `ORDER BY` para limitar los resultados a los primeros `N` valores ordenados. La combinación de estas dos cláusulas es la única manera de indicar de forma previsible las filas a las que TOP afecta.

Puede usar TOP con un valor constante, como se muestra en el ejemplo anterior, o con un valor variable usando consultas parametrizadas.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Los resultados son:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción](sql-query-getting-started.md)
- [Combinaciones](sql-query-join.md)
- [Subconsultas](sql-query-subquery.md)
