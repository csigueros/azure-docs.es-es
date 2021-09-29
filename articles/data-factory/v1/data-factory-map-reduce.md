---
title: Invocar programa MapReduce desde Azure Data Factory
description: Obtenga información sobre cómo procesar datos mediante la ejecución de programas MapReduce en un clúster de HDInsight de Azure desde una factoría de datos de Azure.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 7d50260a6064a551842e8cab2c4a33189d87b19d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679794"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Invocar programas MapReduce desde la factoría de datos de Azure
> [!div class="op_single_selector" title1="Actividades de transformación"]
> * [Actividad de Hive](data-factory-hive-activity.md) 
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución de lotes de Azure Machine Learning Studio (clásico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad de actualización de recurso de Azure Machine Learning Studio (clásico)](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte el artículo acerca de la [transformación de datos mediante la actividad MapReduce en Data Factory](../transform-data-using-hadoop-map-reduce.md).


La actividad de MapReduce para HDInsight en una [canalización](data-factory-create-pipelines.md) de Data Factory ejecuta programas de MapReduce en un clúster de HDInsight basado en Windows/Linux [propio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o en uno [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

> [!NOTE] 
> Si no está familiarizado con Azure Data Factory, lea [Introducción a Azure Data Factory](data-factory-introduction.md) y realice el tutorial de [compilación de la primera canalización de datos](data-factory-build-your-first-pipeline.md) antes de leer este artículo.  

## <a name="introduction"></a>Introducción
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. En este artículo se describe el uso de la actividad MapReduce de HDInsight.

Consulte [Pig](data-factory-pig-activity.md) y [Hive](data-factory-hive-activity.md) para obtener detalles sobre la ejecución de scripts de Pig/Hive en un clúster de HDInsight basado en Windows/Linux desde una canalización mediante actividades de Pig y Hive para HDInsight. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON para la actividad MapReduce de HDInsight
En la definición de JSON para la actividad de HDInsight: 

1. Establezca el **tipo** de la **actividad** en **HDInsight**.
2. Especifique el nombre de la clase para la propiedad **className**.
3. Especifique la ruta de acceso al archivo JAR, incluido el nombre del archivo para la propiedad **jarFilePath**.
4. Especifique el servicio vinculado que hace referencia al Almacenamiento de blobs de Azure que contiene el archivo JAR para la propiedad **jarLinkedService**.   
5. Especifique cualquier argumento para el programa MapReduce en la sección **arguments**. En tiempo de ejecución, verá unos argumentos adicionales (por ejemplo, mapreduce.job.tags) desde el marco de trabajo MapReduce. Para diferenciar sus argumentos con los argumentos de MapReduce, considere el uso tanto de opción como valor como argumentos tal como se muestra en el siguiente ejemplo (-s, --input, --output etc., son opciones seguidas inmediatamente por sus valores).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   Puede usar la actividad MapReduce de HDInsight para ejecutar cualquier archivo jar de MapReduce en un clúster de HDInsight. En la siguiente definición de JSON de ejemplo de una canalización, la actividad de HDInsight se configura para ejecutar un archivo JAR de Mahout.

## <a name="sample-on-github"></a>Ejemplo en GitHub
Puede descargar un ejemplo para usar la actividad MapReduce de HDInsight desde: [Ejemplos de factoría de datos en GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Ejecutar el programa de recuento de palabras
La canalización de este ejemplo ejecuta el programa de asignación/reducción del recuento de palabras en el clúster de HDInsight de Azure.   

### <a name="linked-services"></a>Servicios vinculados
En primer lugar, cree un servicio vinculado para vincular Azure Storage usado por el clúster de HDInsight de Azure con Azure Data Factory. Si copia/pega el código siguiente, no olvide reemplazar el **nombre de la cuenta** y la **clave de la cuenta** por el nombre y la clave de su instancia de Azure Storage. 

#### <a name="azure-storage-linked-service"></a>Servicio vinculado de Azure Storage

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
A continuación, cree un servicio vinculado para vincular el clúster de HDInsight de Azure con la Factoría de datos de Azure. Si copia/pegar el código siguiente, reemplace el **nombre del clúster de HDInsight** por el nombre de su clúster de HDInsight y cambie los valores de nombre de usuario y contraseña.   

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
La canalización de este ejemplo no toma ninguna entrada. Deberá especificar un conjunto de datos de salida para la actividad MapReduce de HDInsight. Este conjunto de datos es simplemente un conjunto de datos ficticio que es necesario para la programación de la canalización.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Canalización
La canalización de este ejemplo tiene solo una actividad de tipo: HDInsightMapReduce. Algunas de las propiedades importantes en JSON son: 

| Propiedad | Notas |
|:--- |:--- |
| type |El tipo debe establecerse en **HDInsightMapReduce**. |
| className |El nombre de la clase es: **wordcount** |
| jarFilePath |Ruta de acceso al archivo .jar que contiene la clase anterior. Si copia/pega el código siguiente, no olvide cambiar el nombre del clúster. |
| jarLinkedService |Servicio vinculado a Azure Storage que contiene el archivo jar. Este servicio vinculado hace referencia al almacenamiento asociado al clúster de HDInsight. |
| argumentos |El programa de recuento de palabras toma dos argumentos, una entrada y una salida. El archivo de entrada es el archivo davinci.txt. |
| frequency/interval |Los valores de estas propiedades coinciden con el conjunto de datos de salida. |
| linkedServiceName |hace referencia al servicio vinculado a HDInsight creado anteriormente. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Ejecutar programas Spark
Puede usar la actividad MapReduce para ejecutar programas Spark en su clúster de HDInsight Spark. Consulte [Invocar programas Spark desde Azure Data Factory](data-factory-spark.md) para obtener más información.  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: /powershell/resourcemanager/Azurerm.DataFactories/v2.2.0/Azurerm.DataFactories


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Consulte también
* [Actividad de Hive](data-factory-hive-activity.md)
* [Actividad de Pig](data-factory-pig-activity.md)
* [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas Spark](data-factory-spark.md)
* [Invocar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)