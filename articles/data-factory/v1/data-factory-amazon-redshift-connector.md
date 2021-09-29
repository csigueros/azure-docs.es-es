---
title: Movimiento de datos de Amazon Redshift mediante Azure Data Factory
description: Obtenga información sobre cómo mover datos de Amazon Redshift mediante la actividad de copia de Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 22370c0df5b11935ae107c065ec66fe98b355cd6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653350"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Movimiento de datos de Amazon Redshift mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-amazon-redshift-connector.md)
> * [Versión 2 (versión actual)](../connector-amazon-redshift.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte [Conector Amazon Redshift en V2](../connector-amazon-redshift.md).

En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos de Amazon Redshift. El artículo se basa en la información general sobre el movimiento de datos con la actividad de copia que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Data Factory, actualmente, solo permite mover datos de Amazon Redshift a un [almacén de datos de receptor compatible](data-factory-data-movement-activities.md#supported-data-stores-and-formats). No se admite el movimiento de datos de otros almacenes de datos a Amazon Redshift.

> [!TIP]
> Para obtener el mejor rendimiento al copiar grandes cantidades de datos de Amazon Redshift, considere usar el comando **UNLOAD** integrado de Redshift a través de Amazon Simple Storage Service (Amazon S3). Para más información, consulte [Uso de UNLOAD para copiar datos de Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Prerrequisitos
* Si mueve datos a un almacén de datos local, instale [Data Management Gateway](data-factory-data-management-gateway.md) en una máquina local. Conceda acceso para una puerta de enlace al clúster de Amazon Redshift mediante el uso de la dirección IP de la máquina local. Para obtener instrucciones, consulte [Authorize access to the cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) (Autorización para acceder al clúster).
* Para mover datos a un almacén de datos de Azure, consulte los [rangos de direcciones IP de Compute y de SQL que los centros de datos de Microsoft Azure utilizan](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia para mover los datos desde un origen de Amazon Redshift mediante el uso de diferentes herramientas y API.

La manera más fácil de crear una canalización es usar el Asistente para copia de Azure Data Factory. Para un tutorial rápido sobre cómo crear una canalización con el Asistente para copia, consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md).

También puede crear una canalización mediante Visual Studio, Azure PowerShell u otras herramientas. También puede utilizar plantillas de Azure Resource Manager, la API de .NET o la API de REST para crear la canalización. Para instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia, consulte el [tutorial de la actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree servicios vinculados para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree conjuntos de datos con el fin de representar los datos de entrada y salida para la operación de copia.
3. Cree una canalización con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida.

Cuando se usa el Asistente para copia, se crean automáticamente definiciones de JSON para estas entidades de Data Factory. Al usar herramientas o API (excepto la API de .NET), se definen las entidades de Data Factory con el formato JSON. En el Ejemplo de JSON: Copiar datos de Amazon Redshift a Azure Blob Storage se muestran las definiciones de JSON para las entidades de Data Factory que se usan para copiar datos de un almacén de datos de Amazon Redshift.

En las secciones siguientes se describen las propiedades JSON que se usan para definir las entidades de Data Factory para Amazon Redshift.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

En la tabla siguiente se proporcionan descripciones de los elementos JSON específicos de un servicio vinculado de Amazon Redshift.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| **type** |Esta propiedad debe establecerse en **AmazonRedshift**. |Sí |
| **servidor** |La dirección IP o nombre de host del servidor de Amazon Redshift. |Sí |
| **port** |El número del puerto TCP que el servidor de Amazon Redshift utiliza para escuchar las conexiones del cliente. |No (el valor predeterminado es 5439) |
| **database** |El nombre de la base de datos de Amazon Redshift. |Sí |
| **username** |El nombre del usuario que tiene acceso a la base de datos. |Sí |
| **password** |Contraseña para la cuenta de usuario. |Sí |

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Para una lista de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [creación de conjuntos de datos](data-factory-create-datasets.md) . Las secciones **structure**, **availability** y **policy** son similares para todos los tipos de conjuntos de datos. Entre los ejemplos de tipos de conjuntos de datos se incluyen Azure SQL, Azure Blob Storage y Azure Table Storage.

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén. La sección **typeProperties** de un conjunto de datos de tipo **RelationalTable**, que incluye el conjunto de datos de Amazon Redshift, tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| **tableName** |El nombre de la tabla en la base de datos de Amazon Redshift a la que hace referencia el servicio vinculado. |No (si se especifica la propiedad **query** de una actividad de copia de tipo **RelationalSource**) |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Para obtener una lista de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades **name**, **description**, tabla **inputs**, tabla **outputs** y **policy** están disponibles para todos los tipos de actividades. Las propiedades que están disponibles en la sección **typeProperties** varían según cada tipo de actividad. Para la actividad de copia, las propiedades varían en función de los tipos de orígenes y receptores de datos.

Para la actividad de copia, cuando el origen es del tipo **AmazonRedshiftSource**, en la sección **typeProperties** están disponibles las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| **consulta** | Use la consulta personalizada para leer los datos. |No (si se especifica la propiedad **tableName** de un conjunto de datos) |
| **redshiftUnloadSettings** | Contiene el grupo de propiedades cuando se usa el comando **UNLOAD** de Redshift. | No |
| **s3LinkedServiceName** | Amazon S3 que se usa como almacenamiento provisional. El servicio vinculado se especifica mediante un nombre de Azure Data Factory del tipo **AwsAccessKey**. | Necesario si se usa la propiedad **redshiftUnloadSettings** |
| **bucketName** | Indica el depósito de Amazon S3 que se usa para almacenar los datos provisionales. Si no se proporciona esta propiedad, la actividad de copia genera automáticamente un depósito. | Necesario si se usa la propiedad **redshiftUnloadSettings** |

Como alternativa, puede utilizar el tipo **RelationalSource**, que incluye Amazon Redshift, con la siguiente propiedad en la sección **typeProperties**. Tenga en cuenta que este tipo de origen no es compatible con el comando **UNLOAD** de Redshift.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| **consulta** |Use la consulta personalizada para leer los datos. | No (si se especifica la propiedad **tableName** de un conjunto de datos) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Uso de UNLOAD para copiar datos de Amazon Redshift

El comando [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) de Amazon Redshift descarga los resultados de una consulta a uno o varios archivos en Amazon S3. Amazon recomienda este comando para copiar conjuntos de datos grandes de Redshift.

**Ejemplo: Copia de datos de Amazon Redshift a Azure Synapse Analytics**

Este ejemplo copia datos de Amazon Redshift a Azure Synapse Analytics. En el ejemplo se utiliza el comando **UNLOAD** de Redshift, datos de copia almacenados provisionalmente y Microsoft PolyBase.

En este ejemplo de caso de uso, la actividad de copia descarga primero los datos de Amazon Redshift a Amazon S3 tal como está configurado en la opción **redshiftUnloadSettings**. Luego, los datos se copian de Amazon S3 a Azure Blob Storage tal como se especifica en la opción **stagingSettings**. Por último, PolyBase carga los datos en Azure Synapse Analytics. Todos los formatos provisionales están controlados por la actividad de copia.

:::image type="content" source="media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png" alt-text="Copia del flujo de trabajo de Amazon Redshift a Azure Synapse Analytics":::

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Ejemplo JSON: Copia de datos de Amazon Redshift a Azure Blob Storage
En este ejemplo, se muestra cómo copiar datos de una base de datos de Amazon Redshift a Azure Blob Storage. Los datos se pueden copiar directamente a cualquier [receptor compatible](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia.

El ejemplo consta de las siguientes entidades de factoría de datos:

* Un servicio vinculado de tipo [AmazonRedshift](#linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](#dataset-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa las propiedades [RelationalSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

El ejemplo copia cada hora los datos de un resultado de consulta de Amazon Redshift a un blob de Azure. Las propiedades JSON que se usan en el ejemplo se describen en las secciones que aparecen después de las definiciones de entidad.

**Servicio vinculado de Amazon Redshift**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Servicio vinculado de almacenamiento de blobs de Azure**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de datos de entrada de Amazon Redshift**

Si la propiedad **external** está establecida en "true", se informa al servicio Data Factory de que el conjunto de datos es externo a la factoría de datos. Este valor de propiedad indica que el conjunto de datos no lo crea ninguna actividad de la factoría de datos. Establezca la propiedad en true en un conjunto de datos de entrada no generado por una actividad en la canalización.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de datos de salida de blob de Azure**

Para escribir los datos en un blob nuevo cada hora, la propiedad **frequency** se establece en "Hora" y la propiedad **interval**, en 1. La propiedad **folderPath** para el blob se evalúa dinámicamente. El valor de propiedad se basa en la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Actividad de copia en una canalización con un origen de Azure Redshift (de tipo RelationalSource) y un receptor de Azure Blob**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida. La canalización está programada para ejecutarse cada hora. En la definición JSON de la canalización, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos que se van a copiar de la última hora.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Asignación de tipos para Amazon Redshift
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas del tipo de origen al tipo de receptor. Los tipos se convierten mediante un enfoque de dos pasos:

1. Conversión de un tipo de origen nativo a un tipo .NET
2. Conversión de un tipo .NET a un tipo de receptor nativo

Las asignaciones siguientes se usan cuando la actividad de copia convierte los datos de un tipo Amazon Redshift a un tipo .NET:

| Tipo Amazon Redshift | Tipo de .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| bigint |Int64 |
| DECIMAL |Decimal |
| real |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| timestamp |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para información sobre cómo asignar columnas en el conjunto de datos de origen a columnas en el conjunto de datos de receptor, consulte [Asignación de columnas de conjunto de datos en Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Lecturas repetibles desde orígenes relacionales
Cuando se copian datos desde un almacén de datos relacionales, se debe tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la **directiva** de reintentos para un conjunto de datos a fin de que se vuelva a ejecutar un segmento cuando se produce un error. Asegúrese de que se lean los mismos datos, sin importar cuántas veces se vuelva a ejecutar el segmento. Asegúrese también de que se lean los mismos datos, independientemente de cómo se vuelva a ejecutar el segmento. Para más información, consulte [Lecturas repetibles desde orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Obtenga información sobre los factores clave que afectan el rendimiento de la actividad de copia y las formas de optimizar el rendimiento en la [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Pasos siguientes
Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para ver instrucciones paso a paso sobre cómo crear una canalización con la actividad de copia.
