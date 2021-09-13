---
title: 'Transformación de datos mediante Hadoop Streaming Activity: Azure'
description: Aprenda cómo puede usar Hadoop Streaming Activity en Azure Data Factory para transformar datos mediante la ejecución de programas de Hadoop Streaming en un clúster de HDInsight propio o a petición.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b94591d95d756c01239c889ea54461f2ec025587
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396680"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformación de datos mediante Hadoop Streaming Activity en Azure Data Factory
> [!div class="op_single_selector" title1="Actividades de transformación"]
> * [Actividad de Hive](data-factory-hive-activity.md) 
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución por lotes de ML Studio (clásico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad de actualización de recurso de ML Studio (clásico)](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte el artículo acerca de la [transformación de datos mediante la actividad de streaming de Hadoop en Data Factory](../transform-data-using-hadoop-streaming.md).


Puede usar la actividad HDInsightStreamingActivity para invocar un trabajo de streaming de Hadoop desde una canalización de Azure Data Factory. El siguiente fragmento de código JSON muestra la sintaxis para usar HDInsightStreamingActivity en un archivo JSON de canalización. 

La actividad de streaming de HDInsight en una [canalización](data-factory-create-pipelines.md) de Data Factory ejecuta programas de streaming de Hadoop en un clúster de HDInsight basado en Windows/Linux [propio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o en uno [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

> [!NOTE] 
> Si no está familiarizado con Azure Data Factory, lea [Introducción a Azure Data Factory](data-factory-introduction.md) y realice el tutorial de [compilación de la primera canalización de datos](data-factory-build-your-first-pipeline.md) antes de leer este artículo. 

## <a name="json-sample"></a>Ejemplo de JSON
El clúster de HDInsight se rellena automáticamente con los programas de ejemplo (wc.exe y cat.exe) y los datos (davinci.txt). De forma predeterminada, el nombre del contenedor usado por el clúster de HDInsight es el nombre del propio clúster. Por ejemplo, si el nombre del clúster es myhdicluster, el nombre del contenedor de blobs asociado sería myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Tenga en cuenta los siguientes puntos:

1. Establezca **linkedServiceName** en el nombre del servicio vinculado que apunta al clúster de HDInsight en el que se va a ejecutar el trabajo de MapReduce de streaming.
2. Establezca el tipo de la actividad en **HDInsightStreaming**.
3. Para la propiedad **mapper**, especifique el nombre del archivo ejecutable del asignador. En el ejemplo, cat.exe es el ejecutable del asignador.
4. Para la propiedad **reducer** , especifique el nombre del ejecutable del reductor. En el ejemplo, wc.exe es el ejecutable del reductor.
5. Para la propiedad **input** type, especifique el archivo de entrada (incluida la ubicación) para el asignador. En el ejemplo: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample es el contenedor de blobs, example/data/Gutenberg es la carpeta y davinci.txt es el blob.
6. Para la propiedad **output** type, especifique el archivo de salida (incluida la ubicación) para el reductor. La salida del trabajo de streaming de Hadoop se escribirá en la ubicación especificada para esta propiedad.
7. En la sección **filePaths**, especifique las rutas de acceso para los archivos ejecutables de asignador y reductor. En el ejemplo: "adfsample/example/apps/wc.exe", adfsample es el contenedor de blobs, example/apps es la carpeta y wc.exe es el ejecutable.
8. Para la propiedad **fileLinkedService** , especifique el servicio vinculado de Azure Storage que representa el almacenamiento de Azure que contiene los archivos especificados en la sección filePaths.
9. Para la propiedad **arguments**, especifique los argumentos para el trabajo de streaming.
10. La propiedad **getDebugInfo** es un elemento opcional. Cuando se establece en Failure, los registros solo se descargan en caso de error. Cuando se establece en Always, los registros se descargan siempre, sea cual sea el estado de ejecución.

> [!NOTE]
> Como se muestra en el ejemplo, deberá especificar un conjunto de datos de salida para la actividad de streaming de Hadoop en la propiedad **outputs** . Este conjunto de datos es simplemente un conjunto de datos ficticio que es necesario para la programación de la canalización. No es necesario especificar ningún conjunto de datos de entrada para la actividad de la propiedad **inputs** .  
> 
> 

## <a name="example"></a>Ejemplo
La canalización de este tutorial ejecuta el programa de asignación/reducción de transmisión por secuencias del recuento de palabras en el clúster de HDInsight de Azure. 

### <a name="linked-services"></a>Servicios vinculados
#### <a name="azure-storage-linked-service"></a>Servicio vinculado de Azure Storage
En primer lugar, cree un servicio vinculado para vincular Azure Storage usado por el clúster de HDInsight de Azure con Azure Data Factory. Si copia/pega el código siguiente, no olvide reemplazar el nombre y la clave de la cuenta por el nombre y la clave de su Azure Storage. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Servicio vinculado de HDInsight de Azure
A continuación, cree un servicio vinculado para vincular el clúster de HDInsight de Azure con la Factoría de datos de Azure. Si copia/pegar el código siguiente, reemplace el nombre del clúster de HDInsight por el nombre de su clúster de HDInsight y cambie los valores de nombre de usuario y contraseña. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Conjuntos de datos
#### <a name="output-dataset"></a>Conjunto de datos de salida
La canalización de este ejemplo no toma ninguna entrada. Deberá especificar un conjunto de datos de salida para la actividad de streaming de HDInsight. Este conjunto de datos es simplemente un conjunto de datos ficticio que es necesario para la programación de la canalización. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Canalización
La canalización de este ejemplo tiene solo una actividad de tipo: **HDInsightStreaming**. 

El clúster de HDInsight se rellena automáticamente con los programas de ejemplo (wc.exe y cat.exe) y los datos (davinci.txt). De forma predeterminada, el nombre del contenedor usado por el clúster de HDInsight es el nombre del propio clúster. Por ejemplo, si el nombre del clúster es myhdicluster, el nombre del contenedor de blobs asociado sería myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Consulte también
* [Actividad de Hive](data-factory-hive-activity.md)
* [Actividad de Pig](data-factory-pig-activity.md)
* [Actividad MapReduce](data-factory-map-reduce.md)
* [Invocar programas Spark](data-factory-spark.md)
* [Invocar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

