---
title: Consulta de los datos con la API de Azure Cosmos DB para MongoDB
description: Obtenga información sobre cómo consultar datos de la API para MongoDB de Azure Cosmos DB mediante comandos del shell de MongoDB.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 2e3604447a5909e7812f5a6acd64468cb0846d12
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354239"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Consulta de los datos con la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

La [API de Azure Cosmos DB para MongoDB](mongodb-introduction.md) admite las [consultas de MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Consulta de los datos almacenados en la base de datos de Cosmos mediante el shell de MongoDB

Puede empezar a trabajar con los ejemplos de este documento y ver el vídeo [Consulta de Azure Cosmos DB con el shell de MongoDB](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/).

## <a name="sample-document"></a>Documento de ejemplo

En las consultas de este artículo se usa el documento de ejemplo siguiente.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="example-query-1"></a><a id="examplequery1"></a> Consulta 1 de ejemplo 

Dado el documento de familia de ejemplo anterior, la consulta siguiente devuelve los documentos donde el campo Id. coincide con `WakefieldFamily`.

**Consultar**

```bash
db.families.find({ id: "WakefieldFamily"})
```

**Resultados**

```json
{
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-2"></a><a id="examplequery2"></a>Consulta 2 de ejemplo 

La consulta siguiente devuelve todos los elementos secundarios de la familia. 

**Consultar**

```bash 
db.families.find( { id: "WakefieldFamily" }, { children: true } )
``` 

**Resultados**

```json
{
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
}
```

## <a name="example-query-3"></a><a id="examplequery3"></a>Consulta 3 de ejemplo 

La consulta siguiente devuelve todas las familias que están registradas. 

**Consultar**

```bash
db.families.find( { "isRegistered" : true })
``` 

**Resultados**

No se devolverá ningún documento. 

## <a name="example-query-4"></a><a id="examplequery4"></a>Consulta 4 de ejemplo

La consulta siguiente devuelve todas las familias que no están registradas. 

**Consultar**

```bash
db.families.find( { "isRegistered" : false })
``` 

**Resultados**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-5"></a><a id="examplequery5"></a>Consulta 5 de ejemplo

La consulta siguiente devuelve todas las familias que no están registradas y el estado es NY. 

**Consultar**

```bash
db.families.find( { "isRegistered" : false, "address.state" : "NY" })
``` 

**Resultados**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-6"></a><a id="examplequery6"></a>Consulta 6 de ejemplo

La consulta siguiente devuelve todas las familias en las que los grados de los elementos secundarios son 8.

**Consultar**

```bash
db.families.find( { children : { $elemMatch: { grade : 8 }} } )
```

**Resultados**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-7"></a><a id="examplequery7"></a>Consulta 7 de ejemplo

La consulta siguiente devuelve todas las familias en las que el valor de tamaño de la matriz secundaria es 3.

**Consultar**

```bash
db.Family.find( {children: { $size:3} } )
```

**Resultados**

No se ha devuelto ningún resultado ya que no hay familias con más de dos hijos. Solo si el parámetro es 2, esta consulta se realizará correctamente y devolverá el documento completo.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Aprendió a consultar mediante la API de Cosmos DB para MongoDB

Ahora puede continuar con el tutorial siguiente para obtener información sobre cómo distribuir sus datos globalmente.

> [!div class="nextstepaction"]
> [Distribución de datos global](tutorial-global-distribution-sql-api.md)

