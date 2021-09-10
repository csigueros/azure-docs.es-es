---
title: Creación de una API web mediante la API de Azure Cosmos DB para MongoDB y el SDK para .NET
description: Se presenta un ejemplo de código de .NET que se puede usar para conectarse a la API de Azure Cosmos DB para MongoDB y realizar consultas.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 8/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2aadb79f306165543c7b8e7c6935b9ff066681e4
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123307775"
---
# <a name="quickstart-build-a-net-web-api-using-azure-cosmos-dbs-api-for-mongodb"></a>Inicio rápido: Creación de una API web de .NET mediante la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

En esta guía de inicio rápido, se muestra cómo:
1. Crear una [cuenta de la API de Azure Cosmos DB para MongoDB](mongodb-introduction.md) 
2. Crear una API web de catálogo de productos con el [controlador de .NET de MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/)
3. Importar datos de ejemplo

## <a name="prerequisites-to-run-the-sample-app"></a>Requisitos previos para ejecutar la aplicación de ejemplo

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
* Una cuenta de Azure con una suscripción activa. [Cree una cuenta de Azure de forma gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). También puede [probar Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sin ninguna suscripción a Azure, de forma gratuita y sin compromiso alguno.

Si no tiene Visual Studio, descargue [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) con la carga de trabajo **ASP.NET y desarrollo web** instalada con el programa de instalación.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="learn-the-object-model"></a>Modelo de objetos

Antes de seguir con la creación de la aplicación, vamos a examinar la jerarquía de recursos de la API para MongoDB y el modelo de objetos que se usa para crear estos recursos y acceder a ellos. La API para MongoDB crea los recursos en el orden siguiente:

* API de Azure Cosmos DB para cuentas de MongoDB
* Bases de datos 
* Colecciones 
* Documentos

Para más información sobre la jerarquía de entidades, consulte el artículo [Modelo de recursos de Azure Cosmos DB](../account-databases-containers-items.md).

## <a name="install-the-sample-app-template"></a>Instalación de la plantilla de aplicación de ejemplo

Este ejemplo es una plantilla de proyecto de .NET que se puede instalar para crear una copia local. Ejecute los siguientes comandos en una ventana de comandos:

```bash
mkdir "C:\cosmos-samples"
cd "C:\cosmos-samples"
dotnet new -i Microsoft.Azure.Cosmos.Templates
dotnet new cosmosmongo-webapi
```

Los comandos anteriores:

1. Cree el directorio *C:\cosmos-samples* para el ejemplo. Elija una carpeta adecuada para su sistema operativo.
1. Cambie el directorio actual a la carpeta *C:\cosmos-samples*.
1. Instale la plantilla del proyecto para que esté disponible globalmente desde la CLI de .NET.
1. Cree una aplicación de ejemplo local con la plantilla del proyecto.

Si no desea usar la CLI de .NET, también puede [descargar las plantillas del proyecto como un archivo ZIP](https://github.com/Azure/azure-cosmos-dotnet-templates). Este ejemplo se encuentra en la carpeta `Templates/APIForMongoDBQuickstart-WebAPI`.

## <a name="review-the-code"></a>Revisión del código

Los pasos siguientes son opcionales. Si está interesado en aprender cómo se crean los recursos de base de datos en el código, revise los siguientes fragmentos de código. De lo contrario, vaya directamente a [Actualización de la configuración de la aplicación](#update-the-application-settings).

### <a name="setup-connection"></a>Configuración de la conexión

El siguiente fragmento de código es del archivo *Services/MongoService.cs*.

* La siguiente clase representa al cliente y .NET Framework la inserta en los servicios que la consumen:

    ```cs
        public class MongoService
        {
            private static MongoClient _client;

            public MongoService(IDatabaseSettings settings)
            {
                _client = new MongoClient(settings.MongoConnectionString);
            }

            public MongoClient GetClient()
            {
                return _client;
            }
        }
    ```

### <a name="setup-product-catalog-data-service"></a>Configuración del servicio de datos del catálogo de productos

Los fragmentos de código siguientes son del archivo *Services/ProductService.cs*.

* El código siguiente recupera la base de datos y la colección y los creará si aún no existen:

    ```csharp
    private readonly IMongoCollection<Product> _products;        

    public ProductService(MongoService mongo, IDatabaseSettings settings)
    {
        var db = mongo.GetClient().GetDatabase(settings.DatabaseName);
        _products = db.GetCollection<Product>(settings.ProductCollectionName);
    }
    ```

* El código siguiente recupera un documento por SKU, un identificador de producto único:

    ```csharp
    public Task<Product> GetBySkuAsync(string sku)
    {
        return _products.Find(p => p.Sku == sku).FirstOrDefaultAsync();
    }
    ```

* El código siguiente crea un producto y lo inserta en la colección:

    ```csharp
    public Task CreateAsync(Product product)
    {
        _products.InsertOneAsync(product);
    }
    ```

* El código siguiente busca y actualiza un producto:

    ```csharp
    public Task<Product> UpdateAsync(Product update)
    {
        return _products.FindOneAndReplaceAsync(
            Builders<Product>.Filter.Eq(p => p.Sku, update.Sku), 
            update, 
            new FindOneAndReplaceOptions<Product> { ReturnDocument = ReturnDocument.After });
    }
    ```

    De forma similar, puede eliminar documentos mediante el método [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html).

## <a name="update-the-application-settings"></a>Actualización de la configuración de la aplicación

En Azure Portal, copie la información de la cadena de conexión:

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Cosmos DB y, en el panel de navegación izquierdo, seleccione **Cadena de conexión** y, después, **Claves de lectura y escritura**. Deberá usar los botones de copia que se encuentran en el lado derecho de la pantalla para copiar la cadena de conexión principal en el archivo *appsettings.json* en el paso siguiente.

2. Abra el archivo *appsettings.json*.

3. Copie el valor de la **cadena de conexión principal** del portal (con el botón de copia) y especifíquelo en el valor de la propiedad **DatabaseSettings.MongoConnectionString** en el archivo **appsettings.json**.

4. Revise el valor del **nombre de la base de datos** en la propiedad **DatabaseSettings.DatabaseName** en el archivo **appsettings.json**.

5. Revise el valor del **nombre de la colección** en la propiedad **DatabaseSettings.ProductCollectionName** en el archivo **appsettings.json**.

> [!WARNING]
> Nunca compruebe las contraseñas u otros datos confidenciales en el código fuente.

Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Cosmos DB.

## <a name="load-sample-data"></a>Carga de datos de muestra

[Descargue](https://www.mongodb.com/try/download/database-tools) [mongoimport](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport), una herramienta de la CLI que importa fácilmente pequeñas cantidades de datos JSON, CSV o TSV. Usaremos mongoimport para cargar los datos de productos de ejemplo proporcionados en la carpeta `Data` de este proyecto.

En Azure Portal, copie la información de conexión y escríbala en el siguiente comando: 

```bash
mongoimport --host <HOST>:<PORT> -u <USERNAME> -p <PASSWORD> --db cosmicworks --collection products --ssl --jsonArray --writeConcern="{w:0}" --file Data/products.json
```

1. En [Azure Portal](https://portal.azure.com/), seleccione la API de Azure Cosmos DB para cuentas de MongoDB y, en el panel de navegación izquierdo, seleccione **Cadena de conexión** y, después, seleccione **Claves de lectura y escritura**. 

1. Copie el valor de **HOST** del portal (mediante el botón de copia) y escríbalo en lugar de **\<HOST\>** .

1. Copie el valor de **PORT** del portal (mediante el botón de copia) y escríbalo en lugar de **\<PORT\>** .

1. Copie el valor de **USERNAME** del portal (mediante el botón de copia) y escríbalo en lugar de **\<USERNAME\>** .

1. Copie el valor de **PASSWORD** del portal (mediante el botón de copia) y escríbalo en lugar de **\<PASSWORD\>** .

1. Revise el valor del **nombre de la base de datos** y actualícelo si creó uno distinto de `cosmicworks`.

1. Revise el valor del **nombre de la colección** y actualícelo si creó uno distinto de `products`.

> [!Note]
> Si desea omitir este paso, puede crear documentos con el esquema correcto mediante el punto de conexión POST proporcionado como parte de este proyecto de API web.

## <a name="run-the-app"></a>Ejecutar la aplicación

En Visual Studio, seleccione CTRL + F5 para ejecutar la aplicación. El explorador predeterminado se inicia con la aplicación.

Si prefiere la CLI, ejecute el siguiente comando en una ventana de comandos para iniciar la aplicación de ejemplo. Este comando también instalará las dependencias del proyecto y compilará el proyecto, pero no iniciará automáticamente el explorador.

```bash
dotnet run
```

Una vez que la aplicación esté en ejecución, vaya a `https://localhost:5001/swagger/index.html` para ver la [documentación de Swagger](https://swagger.io/) de la API web y enviar solicitudes de ejemplo.

Seleccione la API que desea probar y seleccione "Try it out" (Probarlo).

:::image type="content" source="./media/create-mongodb-dotnet/try-swagger.png" alt-text="Captura de pantalla de la página para probar puntos de conexión de API de la interfaz de usuario de Swagger de la API web.":::

Escriba los parámetros necesarios y seleccione "Execute" (Ejecutar).

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a crear una API para cuentas de MongoDB, una base de datos y una colección con código, y a ejecutar una aplicación de API web. Ahora, puede importar datos adicionales en la base de datos. 

¿Intenta planear la capacidad para una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, consulte sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
* Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).

> [!div class="nextstepaction"]
> [Importación de datos de MongoDB a Azure Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
