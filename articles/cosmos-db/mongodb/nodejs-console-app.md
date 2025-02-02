---
title: Uso de la API de Azure Cosmos DB para MongoDB para compilar una aplicación de Node.js
description: Tutorial que crea una base de datos en línea mediante la API de Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js
ms.openlocfilehash: 9edcb24280db6c3fb102b9d1c28d7b2a0e537294
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123031625"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Creación de una aplicación mediante Node.js y la API de Azure Cosmos DB para MongoDB 
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](../sql-api-get-started.md)
> * [.NET Core](../sql-api-get-started.md)
> * [Java](../create-sql-api-java.md)
> * [Node.js para MongoDB](nodejs-console-app.md)
> * [Node.js](../sql-api-nodejs-get-started.md)
>

En este ejemplo se muestra cómo crear una aplicación de consola mediante Node.js y la API de Azure Cosmos DB para MongoDB.

Para usar este ejemplo, tendrá que:

* [Crear](create-mongodb-dotnet.md#create-account) una cuenta de Cosmos configurada para usar la API de Azure Cosmos DB para MongoDB.
* Recuperar la información de la [cadena de conexión](connect-mongodb-account.md).

## <a name="create-the-app"></a>Creación de la aplicación

1. Cree un archivo *app.js* y copie y pegue el código siguiente.

    ```javascript
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Opcional**: si usa el **controlador Node.js 2.2 de MongoDB**, reemplace el fragmento de código siguiente:

    Original:

    ```javascript
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Se debe reemplazar por:

    ```javascript
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Modifique las siguientes variables en el archivo *app.js* según la configuración de la cuenta (aprenda a buscar la [cadena de conexión](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > El **controlador Node.js 3.0 de MongoDB** requiere la codificación de caracteres especiales en la contraseña de Cosmos DB. Asegúrese de codificar los caracteres "=" como %3D
    >
    > Ejemplo: la contraseña *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* se codifica como *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > El **controlador Node.js 2.2 de MongoDB** no requiere la codificación de caracteres especiales en la contraseña de Cosmos DB.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Abra su terminal favorito, ejecute **npm install mongodb --save** y ejecute luego su aplicación con **node app.js**.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [usar Studio 3T](connect-using-mongochef.md) con la API de Azure Cosmos DB para MongoDB.
- Aprenda a [usar Robo 3T](connect-using-robomongo.md) con la API de Azure Cosmos DB para MongoDB.
- Explore [ejemplos](nodejs-console-app.md) de MongoDB con la API de Azure Cosmos DB para MongoDB.
- ¿Intenta planear la capacidad para una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de base de datos existente.
    - Si todo lo que sabe es el número de núcleos virtuales y servidores del clúster de base de datos existente, lea sobre cómo [calcular las unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    - Si conoce las velocidades de solicitud típicas de la carga de trabajo de base de datos actual, lea sobre el [cálculo de las unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).