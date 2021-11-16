---
title: Introducción al uso de la API de Azure Cosmos DB para MongoDB y Python
description: Se presenta un ejemplo de código de Python que se puede usar para conectarse a la API de Azure Cosmos DB para MongoDB y realizar consultas.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 10/22/2021
ms.openlocfilehash: af59ce8dc6fa3fc4a2f62e3bc00e185cb8be1bbf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271310"
---
# <a name="quickstart-get-started-using-azure-cosmos-db-api-for-mongodb-and-python"></a>Inicio rápido: Introducción al uso de la API de Azure Cosmos DB para MongoDB y Python
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

En esta [guía de inicio rápido](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started), se muestra cómo:
1. Crear una [cuenta de la API de Azure Cosmos DB para MongoDB](mongodb-introduction.md) 
2. Conectarse a la cuenta mediante PyMongo
3. Crear una base de datos y una colección de ejemplo
4. Realizar operaciones CRUD en la colección de ejemplo

## <a name="prerequisites-to-run-the-sample-app"></a>Requisitos previos para ejecutar la aplicación de ejemplo

* [Python](https://www.python.org/downloads/) 3.9 o una versión posterior (es mejor ejecutar el [código de ejemplo](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) descrito en este artículo con esta versión recomendada. Aunque puede funcionar en versiones anteriores de Python 3).
* [PyMongo](https://pypi.org/project/pymongo/) instalado en la máquina

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

## <a name="get-the-code"></a>Obtención del código

Descargue el código de Python de ejemplo del [repositorio](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) o use git clone:

```shell
git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started
```

## <a name="retrieve-your-connection-string"></a>Recuperación de la cadena de conexión

Al ejecutar el código de ejemplo, debe escribir la cadena de conexión de la cuenta de la API para MongoDB. Utilice los pasos siguientes para encontrarla:

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Cosmos DB.

2. En el panel de navegación izquierdo, seleccione **Cadena de conexión** y, a continuación, seleccione **Claves de lectura y escritura**. Utilice los botones de copia situados en el lado derecho de la pantalla para copiar la cadena de conexión principal.

> [!WARNING]
> Nunca compruebe las contraseñas u otros datos confidenciales en el código fuente.


## <a name="run-the-code"></a>Ejecución del código

```shell
python run.py
```

## <a name="understand-how-it-works"></a>Comprenda cómo funciona

### <a name="connecting"></a>Connecting

El código siguiente solicita al usuario la cadena de conexión. Nunca es una buena idea tener la cadena de conexión en el código, ya que permite a cualquier persona con acceso a ella leer o escribir en la base de datos.

```python
CONNECTION_STRING = getpass.getpass(prompt='Enter your primary connection string: ') # Prompts user for connection string
```

El código siguiente crea una conexión de cliente a la API para MongoDB y prueba para asegurarse de que sea válida.

```python
client = pymongo.MongoClient(CONNECTION_STRING)
try:
    client.server_info() # validate connection string
except pymongo.errors.ServerSelectionTimeoutError:
    raise TimeoutError("Invalid API for MongoDB connection string or timed out when attempting to connect")
```

### <a name="resource-creation"></a>Creación de recursos
El código siguiente crea la base de datos y la colección de ejemplo que se usarán para realizar operaciones CRUD. Al crear recursos mediante programación, se recomienda usar los comandos de la extensión de la API para MongoDB (como se muestra aquí) porque estos comandos tienen la capacidad de establecer el rendimiento de los recursos (RU/s) y configurar el particionamiento. 

La creación implícita de recursos funcionará, pero tendrá como valor predeterminado los valores recomendados para el rendimiento y no se particionarán.

```python
# Database with 400 RU throughput that can be shared across the DB's collections
db.command({'customAction': "CreateDatabase", 'offerThroughput': 400})
```

```python
 # Creates a unsharded collection that uses the DBs shared throughput
db.command({'customAction': "CreateCollection", 'collection': UNSHARDED_COLLECTION_NAME})
```

### <a name="writing-a-document"></a>Escritura de un documento
El código siguiente inserta un documento de ejemplo que se seguirá usando a lo largo del ejemplo. Se obtiene el valor único del campo _id para poder consultarlo en operaciones posteriores.

```python
"""Insert a sample document and return the contents of its _id field"""
document_id = collection.insert_one({SAMPLE_FIELD_NAME: randint(50, 500)}).inserted_id
```

### <a name="readingupdating-a-document"></a>Lectura y actualización de un documento
El código siguiente consulta, actualiza y consulta de nuevo el documento que se insertó anteriormente.

```python
print("Found a document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))

collection.update_one({"_id": document_id}, {"$set":{SAMPLE_FIELD_NAME: "Updated!"}})
print("Updated document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))
```

### <a name="deleting-a-document"></a>Eliminación de un documento
Por último, eliminamos el documento que hemos creado de la colección.
```python
"""Delete the document containing document_id from the collection"""
collection.delete_one({"_id": document_id})
```

## <a name="next-steps"></a>Pasos siguientes
En este inicio rápido, ha aprendido a crear mediante código una cuenta de la API para MongoDB, una base de datos y una colección, y a ejecutar operaciones CRUD. 

¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de bases de datos existente.
* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
* Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).

> [!div class="nextstepaction"]
> [Importación de datos de MongoDB a Azure Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
