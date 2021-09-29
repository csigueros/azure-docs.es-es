---
title: Movimiento de datos desde Amazon Simple Storage Service mediante Data Factory
description: Aprenda a mover datos desde Amazon Simple Storage Service (S3) mediante Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9915f2bb83c8d2f1ca6491d3603ff78c8c3df4d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655702"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Movimiento de datos desde Amazon Simple Storage Service mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Versión 2 (versión actual)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte [Conector Amazon S3 en V2](../connector-amazon-simple-storage-service.md).

En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos desde Amazon Simple Storage Service (S3). Se basa en la información general ofrecida por el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Puede copiar datos desde Amazon S3 en cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores. Actualmente, Data Factory solo admite el movimiento de datos de Amazon S3 a otros almacenes de datos, pero no de otros almacenes de datos a Amazon S3.

## <a name="required-permissions"></a>Permisos necesarios
Para copiar datos de Amazon S3, asegúrese de que se han concedido los siguientes permisos:

* `s3:GetObject` y `s3:GetObjectVersion` para operaciones de objeto de Amazon S3.
* `s3:ListBucket` para operaciones de depósito de Amazon S3. Si usa el Asistente para copia de Data Factory, `s3:ListAllMyBuckets` también es necesario.

Para más información sobre la lista completa de los permisos de Amazon S3, consulte [Specifying Permissions in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) (Especificación de permisos en una directiva).

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva los datos desde un origen de Amazon S3 mediante diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Para ver un tutorial rápido, consulte el [tutorial sobre la creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md).

Puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **Azure PowerShell**, una **plantilla de Azure Resource Manager**, la **API de .NET** y **API REST**. Para instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia, consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Tanto si usa herramientas como API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia.
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida.

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON. Para obtener un ejemplo con definiciones de JSON para entidades de Data Factory que se utilizan para copiar datos desde un almacén de datos de Amazon S3, consulte la sección [Ejemplo de JSON: Copia de datos de Amazon S3 a un blob de Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) de este artículo.

> [!NOTE]
> Para información sobre los formatos de compresión y de archivo compatibles para una actividad de copia, consulte [Formatos de archivo y de compresión admitidos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

En las secciones siguientes se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de Amazon S3.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
Un servicio vinculado vincula un almacén de datos a una factoría de datos. Se crea un servicio vinculado de tipo **AwsAccessKey** para vincular el almacén de datos de Amazon S3 a la factoría de datos. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de Amazon S3 (AwsAccessKey).

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| accessKeyID |Id. de la clave de acceso secreta. |string |Sí |
| secretAccessKey |La propia clave de acceso secreta. |Cadena secreta cifrada |Sí |

>[!NOTE]
>Este conector requiere claves de acceso para que la cuenta de IAM pueda copiar datos de Amazon S3. Aún no se admite la [credencial de seguridad temporal](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html).
>

Este es un ejemplo:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para especificar un conjunto de datos para representar datos de entrada en Azure Blob Storage, establezca la propiedad de tipo del conjunto de datos en **AmazonS3**. Establezca la propiedad **linkedServiceName** del conjunto de datos en el nombre del servicio vinculado Amazon S3. Para ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Creación de conjuntos de datos](data-factory-create-datasets.md). 

Las secciones como structure, availability y policy son similares para todos los tipos de conjunto de datos (como base de datos SQL, blob de Azure y tabla de Azure). La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **AmazonS3** (que incluye el conjunto de datos de Amazon S3) tiene las propiedades siguientes:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| bucketName |Nombre del depósito de S3. |String |Sí |
| key |La clave del objeto S3. |String |No |
| prefix |Prefijo de la clave del objeto S3. Se seleccionan objetos cuyas claves comienzan por este prefijo. Se aplica solo cuando la clave está vacía. |String |No |
| version |La versión del objeto S3 si está habilitado el control de versiones de S3. |String |No |
| format | Se admiten los tipos de formato siguientes: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formato AVRO](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato ORC](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Si desea copiar los archivos tal cual entre almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. | |No |
| compression | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**. Niveles que se admiten: **Optimal** y **Fastest**. Para más información, consulte el artículo sobre [formatos de compresión de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |No |


> [!NOTE]
> **bucketName + key** especifica la ubicación del objeto S3, donde bucket es el contenedor raíz de los objetos S3 y key es la ruta de acceso completa al objeto S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de datos de ejemplo con prefijo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Conjunto de datos de ejemplo (con versión)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Rutas de acceso dinámicas para S3
En el ejemplo anterior, se usan valores fijos para las propiedades **key** y **bucketName** en el conjunto de datos de Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Puede hacer que Data Factory calcule estas propiedades dinámicamente en tiempo de ejecución mediante variables del sistema como SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Y lo mismo puede hacer para la propiedad **prefix** de un conjunto de datos de Amazon S3. Para ver una lista de funciones y variables admitidas, consulte [Azure Data Factory: funciones y variables del sistema](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades. Las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, las propiedades varían en función de los tipos de orígenes y receptores. Si un origen en la actividad de copia es de tipo **FileSystemSource** (lo que incluye Amazon S3), está disponible la propiedad siguiente en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Especifica si se mostrarán objetos S3 de manera recursiva en el directorio. |true/false |No |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Ejemplo de JSON: Copia de datos de Amazon S3 a Azure Blob Storage
En este ejemplo se muestra cómo copiar datos de Amazon S3 a una instancia de Azure Blob Storage. Sin embargo, los datos se pueden copiar directamente en [cualquiera de los receptores compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory.

El ejemplo proporciona definiciones de JSON para las siguientes entidades de Data Factory. Puede utilizar estas definiciones para crear una canalización para copiar datos de Amazon S3 a Blob Storage mediante [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Un servicio vinculado de tipo [AwsAccessKey](#linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [conjunto de datos](data-factory-create-datasets.md) de tipo [AmazonS3](#dataset-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [canalización](data-factory-create-pipelines.md) con actividad de copia que usa [FileSystemSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian datos de Amazon S3 a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

### <a name="amazon-s3-linked-service"></a>Servicio vinculado de Amazon S3

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Servicio vinculado de Azure Storage

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

### <a name="amazon-s3-input-dataset"></a>Conjunto de datos de entrada de Amazon S3

Si se establece **"external": true**, se informa al servicio Data Factory de que el conjunto de datos es externo a Data Factory. Establezca esta propiedad en true en un conjunto de datos de entrada no generado por una actividad en la canalización.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Conjunto de datos de salida de blob de Azure

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Actividad de copia en una canalización con un origen de Amazon S3 y un receptor de blob

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** se establece en **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a otras del conjunto de datos receptor, consulte [Asignación de columnas de conjuntos de datos en Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

* Para aprender sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory y las diversas formas de optimizarlo, consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md).

* Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para instrucciones paso a paso para crear una canalización con una actividad de copia.
