---
title: Copia de datos con MongoDB como origen o destino
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo copiar datos desde MongoDB a almacenes de datos receptores compatibles, o bien desde almacenes de datos de origen compatibles a MongoDB, a través de una actividad de copia de una canalización de Azure Data Factory o Synapse Analytics.
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 5642577cf8b8e1edf741c09bf4e1968d5cd69770
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831732"
---
# <a name="copy-data-from-or-to-mongodb-using-azure-data-factory-or-synapse-analytics"></a>Copia de datos de MongoDB con Azure Data Factory o Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se describe el uso de la actividad de copia en una canalización de Azure Data Factory o Synapse Analytics para copiar datos con una base de datos de MongoDB como origen y destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

>[!IMPORTANT]
>El nuevo conector de MongoDB proporciona compatibilidad nativa mejorada con MongoDB. Si usa la versión heredada del conector de MongoDB en la solución, que se admite tal cual solo en el caso de la compatibilidad con versiones anteriores, consulte el artículo [Conector de MongoDB (heredado)](connector-mongodb-legacy.md).


## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde una base de datos de MongoDB a cualquier almacén de datos receptor compatible, o viceversa. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de MongoDB admite hasta la **versión 4.2**.


## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-mongodb-using-ui"></a>Creación de un servicio vinculado en MongoDB mediante la interfaz de usuario

Siga estos pasos para crear un servicio vinculado en MongoDB en la interfaz de usuario de Azure Portal.

1. Vaya a la pestaña Administrar del área de trabajo de Azure Data Factory o Synapse y seleccione Servicios vinculados; luego haga clic en Nuevo:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Creación de un servicio vinculado con la interfaz de usuario de Azure Data Factory":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Cree un nuevo servicio vinculado con la interfaz de usuario de Azure Synapse.":::

2. Busque MongoDB y seleccione el conector de MongoDB.

    :::image type="content" source="media/connector-mongodb/mongodb-connector.png" alt-text="Seleccione el conector de MongoDB.":::    

1. Configure los detalles del servicio, pruebe la conexión y cree el nuevo servicio vinculado.

    :::image type="content" source="media/connector-mongodb/configure-mongodb-linked-service.png" alt-text="Configure un servicio vinculado en MongoDB.":::

## <a name="connector-configuration-details"></a>Detalles de configuración del conector

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector MongoDB.


## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de MongoDB:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type |La propiedad type debe establecerse en: **MongoDbV2** |Sí |
| connectionString |Especifique la cadena de conexión de MongoDB, por ejemplo `mongodb://[username:password@]host[:port][/[database][?options]]`. Consulte el [manual de MongoDB sobre cadenas de conexión](https://docs.mongodb.com/manual/reference/connection-string/) para más información. <br/><br /> También puede poner una cadena de conexión en Azure Key Vault. Consulte el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md) para obtener información detallada. |Sí |
| database | Nombre de la base de datos a la que desea acceder. | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Obtenga más información en la sección [Requisitos previos](#prerequisites). Si no se especifica, se usará Azure Integration Runtime. |No |

**Ejemplo**:

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Para ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Conjuntos de datos y servicios vinculados](concepts-datasets-linked-services.md). Las siguientes propiedades son compatibles con el conjunto de datos de MongoDB:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **MongoDbV2Collection** | Sí |
| collectionName |Nombre de la colección en la base de datos de MongoDB. |Sí |

**Ejemplo**:

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen y receptor de MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como origen

Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **MongoDbV2Source** | Sí |
| filter | Especifica el filtro de selección mediante operadores de consulta. Para que se devuelvan todos los documentos de una colección, omita este parámetro o pase un documento vacío ({}). | No |
| cursorMethods.project | Especifica los campos a devolver en los documentos para la proyección. Para devolver todos los campos en los documentos coincidentes, omita este parámetro. | No |
| cursorMethods.sort | Especifica el orden en que la consulta devuelve los documentos coincidentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods.limit | Especifica el número máximo de documentos que devuelve el servidor. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No |
| cursorMethods.skip | Especifica el número de documentos que se omitirán y desde donde empieza MongoDB a devolver resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Especifica el número de documentos a devolver en cada lote de la respuesta de la instancia de MongoDB. En la mayoría de los casos, modificar el tamaño del lote no afectará al usuario ni a la aplicación. Cosmos DB limita el tamaño de cada lote a 40 MB como máximo, que es la suma de los números de batchSize del tamaño de los documentos, por lo que debe reducir este valor si el tamaño del documento es mayor. | No<br/>(el valor predeterminado es **100**) |

>[!TIP]
>El servicio admite el consumo de documentos BSON en **modo strict**. Asegúrese de que la consulta de filtro está en modo strict en lugar de en modo Shell. Para obtener una descripción más detallada, vea el [manual de MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

### <a name="mongodb-as-sink"></a>MongoDB como receptor

La sección **sink** de la actividad de copia admite las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** del receptor de la actividad de copia se debe establecer en **MongoDbV2Sink**. |Sí |
| writeBehavior |Describe cómo escribir datos en MongoDB. Valores permitidos: **insert** y **upsert**.<br/><br/>El comportamiento de **upsert** consiste en reemplazar el documento si ya existe un documento con el mismo `_id`; en caso contrario, inserta el documento.<br /><br />**Nota**: El servicio genera automáticamente un `_id` para un documento si no se especifica un `_id` en el documento original o mediante la asignación de columnas. Esto significa que debe asegurarse de que, para que **upsert** funcione según lo esperado, el documento tenga un identificador. |No<br />(el valor predeterminado es **insert**) |
| writeBatchSize | La propiedad **writeBatchSize** controla el tamaño de los documentos que se escribirán en cada lote. Puede intentar aumentar el valor de **writeBatchSize** para mejorar el rendimiento y reducir el valor si el documento tiene un tamaño grande. |No<br />(el valor predeterminado es **10 000**) |
| writeBatchTimeout | Tiempo que se concede a la operación de inserción por lotes para que finalice antes de que se agote el tiempo de espera. El valor permitido es TimeSpan. | No<br/>(El valor predeterminado es **00:30:00** [30 minutos]). |

>[!TIP]
>Para importar documentos JSON tal cual, vea la sección [Importar o exportar documentos JSON](#import-and-export-json-documents); para copiar de datos con formato tabular, vea [Asignación de esquemas](#schema-mapping).

**Ejemplo**

```json
"activities":[
    {
        "name": "CopyToMongoDB",
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
                "type": "MongoDbV2Sink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importación y exportación de documentos JSON

Puede usar este conector de MongoDB para hacer fácilmente lo siguiente:

* Copiar documentos entre dos colecciones de MongoDB tal cual.
* Importar documentos JSON desde varios orígenes a MongoDB, incluidos Azure Cosmos DB, Azure Blob Storage, Azure Data Lake Store y otros almacenes basados en archivos compatibles.
* Exportar documentos JSON de una colección de MongoDB a varios almacenes basados en archivos.

Para lograr esa copia independiente del esquema, omita la sección “structure” (estructura, también denominada *schema*) en el conjunto de datos y la asignación de esquemas en la actividad de copia.


## <a name="schema-mapping"></a>Asignación de esquemas

Para copiar datos desde MongoDB en un receptor tabular o invertido, consulte la [asignación de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).


## <a name="next-steps"></a>Pasos siguientes
Para obtener una lista de almacenes de datos que la actividad de copia admite como orígenes y receptores, vea [Almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
