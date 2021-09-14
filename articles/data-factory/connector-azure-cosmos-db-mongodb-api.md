---
title: Copia de datos desde la API de Azure Cosmos DB para MongoDB
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo copiar datos de almacenes de datos de origen compatibles desde o hacia la API de Azure Cosmos DB para MongoDB para almacenes de receptor admitidos mediante Data Factory.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: 0147782482308ac8b625926e51c59315f084237d
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123304675"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Copia de datos desde o hacia la API de Azure Cosmos DB para MongoDB mediante Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo, se describe cómo usar Copiar actividad en Azure Data Factory para copiar datos desde y hacia la API de Azure Cosmos DB para MongoDB. El artículo se basa en [Actividad de copia en Azure Data Factory](copy-activity-overview.md), en el que se ofrece información general acerca de la actividad de copia.

>[!NOTE]
>Este conector solo admite la copia de datos desde y hacia la API de Azure Cosmos DB para MongoDB. Para la API de SQL, vea [Conector de API de SQL de Cosmos DB](connector-azure-cosmos-db.md). Actualmente, no se admiten otros tipos de API.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde la API de Azure Cosmos DB para MongoDB a cualquier almacén de datos de receptor compatible, o bien puede copiar datos desde cualquier almacén de datos de origen compatible a la API de Azure Cosmos DB para MongoDB. Para obtener una lista de almacenes de datos que la actividad de copia admite como orígenes y receptores, consulte [Almacenes de datos y formatos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).

Puede usar el conector de la API de Azure Cosmos DB para MongoDB para:

- Copiar datos desde y hacia la [API de Azure Cosmos DB para MongoDB](../cosmos-db/mongodb-introduction.md).
- Escriba en Azure Cosmos DB como **insert** o **upsert**.
- Importe y exporte documentos JSON tal cual, o copie datos desde o hacia un conjunto de datos tabular. Entre los ejemplos se incluyen una base de datos SQL y un archivo CSV. Para copiar datos tal cual desde o hacia archivos JSON u otra colección de Azure Cosmos DB, consulte Importación o exportación de documentos JSON.

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-cosmos-dbs-api-for-mongodb-using-ui"></a>Creación de un servicio vinculado a la API de Azure Cosmos DB para MongoDB mediante la interfaz de usuario

Siga estos pasos para crear un servicio vinculado a la API de Azure Cosmos DB para MongoDB en la interfaz de usuario de Azure Portal.

1. Vaya a la pestaña Administrar del área de trabajo de Azure Data Factory o Synapse y seleccione Servicios vinculados; luego haga clic en Nuevo:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Synapse.":::

2. Busque Cosmos y seleccione la API de Azure Cosmos DB para el conector de MongoDB.

    :::image type="content" source="media/connector-azure-cosmos-db-mongodb-api/azure-cosmos-db-mongodb-api-connector.png" alt-text="Selección de la API de Azure Cosmos DB para conector de MongoDB.":::    

1. Configure los detalles del servicio, pruebe la conexión y cree el servicio vinculado.

    :::image type="content" source="media/connector-azure-cosmos-db-mongodb-api/configure-azure-cosmos-db-mongodb-api-linked-service.png" alt-text="Configuración de un servicio vinculado a la API de Azure Cosmos DB para MongoDB.":::

## <a name="connector-configuration-details"></a>Detalles de configuración del conector

En las secciones siguientes, se proporcionan detalles sobre las propiedades que puede usar para definir entidades de Data Factory específicas de la API de Azure Cosmos DB para MongoDB.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de la API de Azure Cosmos DB para MongoDB:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** tiene que establecerse en **CosmosDbMongoDbApi**. | Sí |
| connectionString |Especifique la cadena de conexión para la API de Azure Cosmos DB para MongoDB. La encontrará en Azure Portal -> su hoja de Cosmos DB -> cadena de conexión principal o secundaria. <br/>Para la versión 3.2 del servidor, el patrón de cadena es `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/>Para la versión 3.6 y superior del servidor, el patrón de cadena es `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<cosmosdb-name>@`.<br/><br />También puede establecer una contraseña en Azure Key Vault y extraer la configuración de `password` de la cadena de conexión. Consulte el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md) para obtener información detallada.|Sí |
| database | Nombre de la base de datos a la que desea acceder. | Sí |
| isServerVersionAbove32 | Especifique si la versión del servidor es superior a la 3.2. Los valores permitidos son **true** y **false** (predeterminado). Esta elección determinará el controlador que se va a usar en el servicio. | Sí |
| connectVia | Instancia de [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado (si el almacén de datos se encuentra en una red privada). Si no se especifica esta propiedad, se usa el valor predeterminado de Azure Integration Runtime. |No |

**Ejemplo**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase",
            "isServerVersionAbove32": "false"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Para ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Conjuntos de datos y servicios vinculados](concepts-datasets-linked-services.md). Las siguientes propiedades son compatibles con el conjunto de datos de la API de Azure Cosmos DB para MongoDB:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** del conjunto de datos tiene que establecerse en **CosmosDbMongoDbApiCollection**. |Sí |
| collectionName |Nombre de la colección de Azure Cosmos DB. |Sí |

**Ejemplo**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

En esta sección, se proporciona una lista de las propiedades admitidas por el origen y el receptor de la API de Azure Cosmos DB para MongoDB.

Para ver una lista completa de las secciones y propiedades que hay disponibles para definir actividades, consulte [Canalizaciones](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>API de Azure Cosmos DB para MongoDB como origen

La sección **source** de la actividad de copia admite las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** del origen de la actividad de copia tiene que establecerse en **CosmosDbMongoDbApiSource**. |Sí |
| filter | Especifica el filtro de selección mediante operadores de consulta. Para que se devuelvan todos los documentos de una colección, omita este parámetro o pase un documento vacío ({}). | No |
| cursorMethods.project | Especifica los campos a devolver en los documentos para la proyección. Para devolver todos los campos en los documentos coincidentes, omita este parámetro. | No |
| cursorMethods.sort | Especifica el orden en que la consulta devuelve los documentos coincidentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods.limit |    Especifica el número máximo de documentos que devuelve el servidor. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No | 
| cursorMethods.skip | Especifica el número de documentos que se omitirán y desde donde empieza MongoDB a devolver resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Especifica el número de documentos a devolver en cada lote de la respuesta de la instancia de MongoDB. En la mayoría de los casos, modificar el tamaño del lote no afectará al usuario ni a la aplicación. Cosmos DB limita el tamaño de cada lote a 40 MB como máximo, que es la suma de los números de batchSize del tamaño de los documentos, por lo que debe reducir este valor si el tamaño del documento es mayor. | No<br/>(el valor predeterminado es **100**) |

>[!TIP]
>ADF admite el consumo de documentos BSON en **modo strict**. Asegúrese de que la consulta de filtro está en modo strict en lugar de en modo Shell. Para obtener una descripción más detallada, vea el [manual de MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Ejemplo**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>API de Azure Cosmos DB para MongoDB como receptor

La sección **sink** de la actividad de copia admite las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** del receptor “Copy Activity” (Actividad de copia) tiene que establecerse en **CosmosDbMongoDbApiSink**. |Sí |
| writeBehavior |Describe cómo escribir datos en Azure Cosmos DB. Valores permitidos: **insert** y **upsert**.<br/><br/>El comportamiento de **upsert** consiste en reemplazar el documento si ya existe un documento con el mismo `_id`; en caso contrario, inserta el documento.<br /><br />**Nota**: Data Factory genera automáticamente un `_id` para un documento si no se especifica un `_id` en el documento original o mediante la asignación de columnas. Esto significa que debe asegurarse de que, para que **upsert** funcione según lo esperado, el documento tenga un identificador. |No<br />(el valor predeterminado es **insert**) |
| writeBatchSize | La propiedad **writeBatchSize** controla el tamaño de los documentos que se escribirán en cada lote. Puede intentar aumentar el valor de **writeBatchSize** para mejorar el rendimiento y reducir el valor si el documento tiene un tamaño grande. |No<br />(el valor predeterminado es **10 000**) |
| writeBatchTimeout | Tiempo que se concede a la operación de inserción por lotes para que finalice antes de que se agote el tiempo de espera. El valor permitido es TimeSpan. | No<br/>(El valor predeterminado es **00:30:00** [30 minutos]). |

>[!TIP]
>Para importar documentos JSON tal cual, vea la sección [Importar o exportar documentos JSON](#import-and-export-json-documents); para copiar de datos con formato tabular, vea [Asignación de esquemas](#schema-mapping).

**Ejemplo**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importación y exportación de documentos JSON

Puede utilizar este conector de Azure Cosmos DB para hacer lo siguiente con facilidad:

* Copiar documentos entre dos colecciones de Azure Cosmos DB tal cual.
* Importar documentos JSON desde varios orígenes a Azure Cosmos DB, incluidos MongoDB, Azure Blob Storage, Azure Data Lake Store y otros almacenes basados en archivos compatibles con Azure Data Factory.
* Exportar documentos JSON de una colección de Azure Cosmos DB a varios almacenes basados en archivos.

Para lograr una copia independiente del esquema:

* Cuando use la herramienta Copiar datos, seleccione la opción **Export as-is to JSON files or Cosmos DB collection** (Exportar tal cual a archivos JSON o colección de Cosmos DB).
* Cuando use la creación de actividades, elija el formato JSON con el almacén de archivos correspondiente para el origen o el receptor.

## <a name="schema-mapping"></a>Asignación de esquemas

Para copiar datos de la API de Azure Cosmos DB para MongoDB en un receptor tabular o inverso, vea [Asignación de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

En concreto, para escribir en Cosmos DB y asegurarse de rellenar Cosmos DB con el identificador de objeto correcto de los datos de origen (por ejemplo, si tiene una columna “id” en la tabla de la base de datos SQL y quiere usar el valor como el identificador del documento en MongoDB para inserción/upsert), necesita establecer la asignación de esquemas correcta según la definición de modo estricto de MongoDB (`_id.$oid`), como se indica a continuación:

![Asignar identificador en receptor de MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Después de ejecutar “Copiar actividad”, a continuación se genera el elemento ObjectId de JSON en el receptor:

```json
{
    "_id&quot;: ObjectId(&quot;592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Pasos siguientes

Para ver una lista de los almacenes de datos que la actividad de copia admite como orígenes y receptores en Azure Data Factory, consulte los [almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
