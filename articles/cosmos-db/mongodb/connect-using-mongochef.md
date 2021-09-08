---
title: Uso de Studio 3T para conectarse a la conexión a la API de Azure Cosmos DB para MongoDB
description: Aprenda a conectarse a una API de Azure Cosmos DB para MongoDB mediante Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: gahl-levy
ms.author: gahllevy
ms.custom: seodec18
ms.openlocfilehash: 98020bb69da6f9719a465e50b5da79248750b17e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780011"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Conexión a una cuenta de Azure Cosmos mediante Studio 3T
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Para conectarse a una API de Azure Cosmos DB para MongoDB mediante Studio 3T, debe:

* Descargar e instalar [Studio 3T](https://studio3t.com/).
* Tener la información de [cadena de conexión](connect-mongodb-account.md) de su cuenta de Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Creación de la conexión en Studio 3T

Para agregar la cuenta de Azure Cosmos al administrador de conexiones de Studio 3T, use estos pasos:

1. Recupere la información de conexión para la API de Azure Cosmos DB para la cuenta de MongoDB mediante las instrucciones del artículo [Conectar una aplicación de MongoDB a Azure Cosmos DB](connect-mongodb-account.md).

    :::image type="content" source="./media/connect-using-mongochef/connection-string-blade.png" alt-text="Captura de pantalla de la página Cadena de conexión.":::

2. Haga clic en **Connect** (Conectar) para abrir Connection Manager (Administrador de conexiones) y, después, haga clic en **New Connection** (Nueva conexión).

    :::image type="content" source="./media/connect-using-mongochef/connection-manager.png" alt-text="Captura de pantalla del administrador de conexiones de Studio 3T con el botón Nueva conexión resaltado.":::
3. En la ventana **New Connection** (Nueva conexión), en la pestaña **Server** (Servidor), escriba el HOST (FQDN) de la cuenta de Azure Cosmos y el PUERTO.

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-server-tab.png" alt-text="Captura de pantalla de la pestaña de servidor del administrador de conexiones de Studio 3T":::
4. En la ventana **New Connection** (Nueva conexión), en la pestaña **Authentication** (Autenticación), elija el modo de autenticación **Basic (MONGODB-CR or SCRAM-SHA-1)** [Básico (MONGODB-CR o SCRAM-SHA-1)] y escriba el NOMBRE DE USUARIO y la CONTRASEÑA.  Acepte la base de datos de autenticación predeterminada (admin) o proporcione su propio valor.

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-authentication-tab.png" alt-text="Captura de pantalla de la pestaña de autenticación del administrador de conexiones de Studio 3T":::
5. En la ventana **New Connection** (Nueva conexión), en la pestaña **SSL**, active la casilla **Use SSL protocol to connect** (Usar protocolo SSL para conectar) y el botón de radio **Accept server self-signed SSL certificates** (Aceptar certificados SSL autofirmados del servidor).

    :::image type="content" source="./media/connect-using-mongochef/connection-manager-ssl-tab.png" alt-text="Captura de pantalla de la pestaña SSL del administrador de conexiones de Studio 3T":::
6. Haga clic en el botón **Test Connection** (Probar conexión) para validar la información de conexión, haga clic en **OK** (Aceptar) para volver a la ventana de la nueva conexión y, finalmente, haga clic en **Save** (Guardar).

    :::image type="content" source="./media/connect-using-mongochef/test-connection-results.png" alt-text="Captura de pantalla de la ventana de conexión de prueba de Studio 3T":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Uso de Studio 3T para crear una base de datos, una colección y unos documentos
Para crear una base de datos, una colección y unos documentos con Studio 3T, realice los pasos siguientes:

1. En **Connection Manager** (Administrador de conexiones), resalte la conexión y haga clic en **Connect** (Conectar).

    :::image type="content" source="./media/connect-using-mongochef/connect-account.png" alt-text="Captura de pantalla del administrador de conexiones de Studio 3T":::
2. Haga clic con el botón derecho en el host y elija **Add Database** (Agregar base de datos).  Especifique un nombre de base de datos y haga clic en **OK**(Aceptar).

    :::image type="content" source="./media/connect-using-mongochef/add-database.png" alt-text="Captura de pantalla de la opción Agregar base de datos de Studio 3T":::
3. Haga clic con el botón derecho en la base de datos y elija **Add Collection** (Agregar colección).  Especifique un nombre para la colección y haga clic en **Create**(Create).

    :::image type="content" source="./media/connect-using-mongochef/add-collection.png" alt-text="Captura de pantalla de la opción Agregar colección de Studio 3T":::
4. Haga clic en el elemento de menú **Collection** (Colección) y en **Add Document** (Agregar documento).

    :::image type="content" source="./media/connect-using-mongochef/add-document.png" alt-text="Captura de pantalla del elemento de menú Agregar documento de 3T":::
5. En el cuadro de diálogo Add Document (Agregar documento), pegue lo siguiente y haga clic en **Add Document**(Agregar documento).

    ```json
    {
        "_id": "AndersenFamily",
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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }
    ```
    
6. Agregue otro documento, esta vez con el contenido siguiente:

    ```json
    {
        "_id": "WakefieldFamily",
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
        "isRegistered": false
    }
    ```

7. Ejecute una consulta de ejemplo. Por ejemplo, busque familias con el apellido 'Andersen' y devuelva los campos parents (padres) y state (estado).

    :::image type="content" source="./media/connect-using-mongochef/query-document.png" alt-text="Captura de pantalla de los resultados de la consulta de Mongo Chef":::

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [usar Robo 3T](connect-using-robomongo.md) con la API de Azure Cosmos DB para MongoDB.
- Explore [ejemplos](nodejs-console-app.md) de MongoDB con la API de Azure Cosmos DB para MongoDB.
