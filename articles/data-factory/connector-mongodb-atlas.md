---
title: Copia de datos con MongoDB Atlas como origen o destino
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo copiar datos desde MongoDB Atlas a almacenes de datos receptores compatibles, o bien desde almacenes de datos de origen compatibles a MongoDB Atlas, a través de una actividad de copia de una canalización de Azure Data Factory.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 06/01/2021
ms.openlocfilehash: 9e42ff971f2ea0a374fe40815ac8cf7fddb29189
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638718"
---
# <a name="copy-data-from-or-to-mongodb-atlas-using-azure-data-factory"></a>Copia de datos con MongoDB Atlas como origen o destino mediante Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde una base de datos de MongoDB Atlas y hacia esa base de datos. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde una base de datos de MongoDB Atlas a cualquier almacén de datos receptor compatible, o viceversa. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de MongoDB Atlas admite hasta la **versión 4.2**.

## <a name="prerequisites"></a>Requisitos previos

Si usa Azure Integration Runtime para realizar las copias, asegúrese de agregar el [IP de Azure Integration Runtime](azure-integration-runtime-ip-addresses.md) de la región efectiva a la lista de acceso de IP de MongoDB Atlas.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de MongoDB Atlas.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de MongoDB Atlas:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type |La propiedad type debe establecerse en: **MongoDbAtlas** |Sí |
| connectionString |Especifique la cadena de conexión de MongoDB Atlas, por ejemplo, `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>`. <br/><br /> También puede poner una cadena de conexión en Azure Key Vault. Consulte el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md) para obtener información detallada. |Sí |
| database | Nombre de la base de datos a la que desea acceder. | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Obtenga más información en la sección [Requisitos previos](#prerequisites). Si no se especifica, se usará Azure Integration Runtime. |No |

**Ejemplo**:

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
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

Para ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Conjuntos de datos y servicios vinculados](concepts-datasets-linked-services.md). Las siguientes propiedades son compatibles con el conjunto de datos de MongoDB Atlas:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **MongoDbAtlasCollection** | Sí |
| collectionName |Nombre de la colección en la base de datos de MongoDB Atlas. |Sí |

**Ejemplo**:

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen y receptor de MongoDB Atlas.

### <a name="mongodb-atlas-as-source"></a>MongoDB Atlas como origen

Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **MongoDbAtlasSource** | Sí |
| filter | Especifica el filtro de selección mediante operadores de consulta. Para que se devuelvan todos los documentos de una colección, omita este parámetro o pase un documento vacío ({}). | No |
| cursorMethods.project | Especifica los campos a devolver en los documentos para la proyección. Para devolver todos los campos en los documentos coincidentes, omita este parámetro. | No |
| cursorMethods.sort | Especifica el orden en que la consulta devuelve los documentos coincidentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods.limit | Especifica el número máximo de documentos que devuelve el servidor. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No |
| cursorMethods.skip | Especifica el número de documentos que se omitirán y desde dónde empieza MongoDB Atlas a devolver resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Especifica el número de documentos que se devolverán en cada lote de la respuesta de la instancia de MongoDB Atlas. En la mayoría de los casos, modificar el tamaño del lote no afectará al usuario ni a la aplicación. Cosmos DB limita el tamaño de cada lote a 40 MB como máximo, que es la suma de los números de batchSize del tamaño de los documentos, por lo que debe reducir este valor si el tamaño del documento es mayor. | No<br/>(el valor predeterminado es **100**) |

>[!TIP]
>ADF admite el consumo de documentos BSON en **modo strict**. Asegúrese de que la consulta de filtro está en modo strict en lugar de en modo Shell. Para obtener una descripción más detallada, vea el [manual de MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
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

### <a name="mongodb-atlas-as-sink"></a>MongoDB Atlas como receptor

La sección **sink** de la actividad de copia admite las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** del receptor de la actividad de copia se debe establecer en **MongoDbAtlasSink**. |Sí |
| writeBehavior |Describe cómo escribir datos en MongoDB Atlas. Valores permitidos: **insert** y **upsert**.<br/><br/>El comportamiento de **upsert** consiste en reemplazar el documento si ya existe un documento con el mismo `_id`; en caso contrario, inserta el documento.<br /><br />**Nota**: Data Factory genera automáticamente un `_id` para un documento si no se especifica un `_id` en el documento original o mediante la asignación de columnas. Esto significa que debe asegurarse de que, para que **upsert** funcione según lo esperado, el documento tenga un identificador. |No<br />(el valor predeterminado es **insert**) |
| writeBatchSize | La propiedad **writeBatchSize** controla el tamaño de los documentos que se escribirán en cada lote. Puede intentar aumentar el valor de **writeBatchSize** para mejorar el rendimiento y reducir el valor si el documento tiene un tamaño grande. |No<br />(el valor predeterminado es **10 000**) |
| writeBatchTimeout | Tiempo que se concede a la operación de inserción por lotes para que finalice antes de que se agote el tiempo de espera. El valor permitido es TimeSpan. | No<br/>(El valor predeterminado es **00:30:00** [30 minutos]). |

>[!TIP]
>Para importar documentos JSON tal cual, vea la sección [Importar o exportar documentos JSON](#import-and-export-json-documents); para copiar de datos con formato tabular, vea [Asignación de esquemas](#schema-mapping).

**Ejemplo**

```json
"activities":[
    {
        "name": "CopyToMongoDBAtlas",
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
                "type": "MongoDbAtlasSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importación y exportación de documentos JSON

Puede usar este conector de MongoDB Atlas para hacer fácilmente lo siguiente:

* Copiar documentos entre dos colecciones de MongoDB Atlas tal cual.
* Importar documentos JSON desde varios orígenes a MongoDB Atlas, incluidos Azure Cosmos DB, Azure Blob Storage, Azure Data Lake Store y otros almacenes basados en archivos compatibles con Azure Data Factory.
* Exportar documentos JSON de una colección de MongoDB Atlas a varios almacenes basados en archivos.

Para lograr esa copia independiente del esquema, omita la sección “structure” (estructura, también denominada *schema*) en el conjunto de datos y la asignación de esquemas en la actividad de copia.


## <a name="schema-mapping"></a>Asignación de esquemas

Para copiar datos desde MongoDB Atlas en un receptor tabular o invertido, consulte la [asignación de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
