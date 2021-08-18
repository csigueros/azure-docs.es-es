---
title: Consulta de datos geoespaciales con Azure Cosmos DB
description: Consulta de datos espaciales con Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: df7cd564dea276ce1192c14cf39e8dde37d4f28c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206644"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Consulta de datos geoespaciales con Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo se explica cómo consultar datos geoespaciales de Azure Cosmos DB con SQL y LINQ. Actualmente, solo las cuentas de SQL API de Azure Cosmos DB admiten almacenamiento y acceso a datos geoespaciales. Azure Cosmos DB admite las siguientes funciones integradas de Open Geospatial Consortium (OGC) para realizar consultas geoespaciales. Para más información sobre el conjunto completo de funciones integradas en el lenguaje SQL, consulte [Consulta de funciones del sistema de Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Funciones integradas SQL espaciales

Esta es una lista de funciones geoespaciales del sistema útiles para realizar consultas en Azure Cosmos DB:

|**Uso**|**Descripción**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Devuelve la distancia entre dos expresiones Point, Polygon o LineString de GeoJSON.|
|ST_WITHIN (spatial_expr, spatial_expr) | Devuelve una expresión booleana que indica si el primer objeto de GeoJSON (Point, Polygon o LineString) está en el segundo objeto de GeoJSON (Point, Polygon o LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Devuelve una expresión booleana que indica si los dos objetos de GeoJSON especificados (Point, Polygon o LineString) intersecan.|
|ST_ISVALID| Devuelve un valor booleano que indica si la expresión de Point, Polygon o LineString de GeoJSON especificada es válida.|
| ST_ISVALIDDETAILED| Devuelve un valor JSON que contiene un valor booleano si la expresión de GeoJSON Point, Polygon o LineString especificada es válida. Si no es válida, devuelve el motivo como un valor de cadena.|

Las funciones espaciales pueden usarse para realizar consultas de proximidad con datos espaciales. Por ejemplo, esta es una consulta que devuelve todos los documentos de la familia que estén dentro de un radio de 30 km de la ubicación especificada mediante la función integrada `ST_DISTANCE`.

**Consultar**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Si incluye la indexación espacial en la directiva de indexación, las "consultas de distancia" se atenderán eficazmente a través del índice. Para más información sobre la indexación espacial, consulte [Indexación geoespacial](sql-query-geospatial-index.md). Si no tiene un índice espacial para las rutas especificadas, la consulta realizará un examen del contenedor.

`ST_WITHIN` se puede usar para comprobar si un punto se encuentra dentro de un elemento Polygon. Normalmente, los elementos Polygon se usan para representar límites, como códigos postales, fronteras o formaciones naturales. Una vez más, si incluye la indexación espacial en la directiva de indexación, las consultas "interiores" se atenderán eficazmente a través del índice.

Los argumentos de Polygon en `ST_WITHIN` solo pueden contener un anillo individual; es decir, los elementos Polygon no deben contener orificios.

**Consultar**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> De forma parecida a cómo funcionan los tipos no coincidentes en una consulta de Azure Cosmos DB, si el valor de ubicación especificado en cualquier argumento está mal formado o no es válido, se evalúa con el valor **sin definir** y el documento evaluado se omite de los resultados de la consulta. Si la consulta no devuelve resultados, ejecute `ST_ISVALIDDETAILED` para depurarla y saber por qué el tipo espacial no es válido.
>
>

Azure Cosmos DB también admite la realización de consultas inversas; es decir, puede indexar elementos polígonos o líneas en Azure Cosmos DB y, a continuación, consultar las áreas que contienen un punto especificado. Este patrón se utiliza habitualmente en logística para identificar, por ejemplo, el momento en que un camión entra o sale de un área designada.

**Consultar**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Resultados**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` y `ST_ISVALIDDETAILED` se pueden usar para comprobar si un objeto espacial es válido. Por ejemplo, la consulta siguiente comprueba la validez de un punto con un valor de latitud fuera del intervalo (-132,8). `ST_ISVALID` devuelve solo un valor booleano y `ST_ISVALIDDETAILED` devuelve el valor booleano y una cadena que contiene el motivo por el que se considera no válida.

**Consultar**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Resultados**

```json
    [{
      "$1": false
    }]
```

Estas funciones también se pueden usar para validar elementos Polygon. Por ejemplo, aquí usamos `ST_ISVALIDDETAILED` para validar un elemento Polygon que no está cerrado.

**Consultar**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Resultados**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Consultas LINQ en el SDK de .NET

El SDK de .NET de SQL también proporciona los métodos de código auxiliar `Distance()` y `Within()` para su uso en expresiones LINQ. El proveedor LINQ de SQL traduce estas llamadas al método a las llamadas de función integrada de SQL equivalentes (ST_DISTANCE y ST_WITHIN respectivamente).

Este es un ejemplo de una consulta LINQ que busca todos los documentos del contenedor de Azure Cosmos cuyo valor de `location` se encuentra en un radio de 30 km del punto especificado mediante LINQ.

**Consulta LINQ de distancia**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

De forma similar, aquí vemos una consulta para buscar todos los documentos cuya `location` está dentro del cuadro o del elemento Polygon especificado.

**Consulta LINQ interna**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya sabe cómo empezar a trabajar con la compatibilidad geoespacial en Azure Cosmos DB, puede hacer lo siguiente:

* Obtener más información sobre [Consultar Azure Cosmos DB](sql-query-getting-started.md)
* Más información sobre los [datos de ubicación geoespaciales y GeoJSON en Azure Cosmos DB](sql-query-geospatial-intro.md)
* Más información sobre la [indexación de datos espaciales con Azure Cosmos DB](sql-query-geospatial-index.md)
