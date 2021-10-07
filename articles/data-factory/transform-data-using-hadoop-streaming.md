---
title: Transformación de datos mediante la actividad Streaming de Hadoop
description: Obtenga información sobre cómo la actividad de Hadoop Streaming en canalizaciones de Azure Data Factory o Synapse Analytics para transformar datos mediante la ejecución de programas de Hadoop Streaming en un clúster de Hadoop.
titleSuffix: Azure Data Factory & Azure Synapse
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: b1767a38cb7661ab074ad579d22f0512e5994eac
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806015"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory-or-synapse-analytics"></a>Transformación de datos mediante la actividad de Hadoop Streaming en Azure Data Factory o Synapse Analytics
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Versión actual](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La actividad de Hadoop Streaming en una [canalización](concepts-pipelines-activities.md) de Azure Data Factory o Synapse Analytics ejecuta programas de Hadoop Streaming en [su propio](compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight o en un clúster [a petición](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en el artículo sobre [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

Para obtener más información, vea los artículos de introducción a [Azure Data Factory](introduction.md) y [Synapse Analytics](../synapse-analytics/overview-what-is.md) y siga el [tutorial de transformación de datos](tutorial-transform-data-spark-powershell.md) antes de leer este artículo. 

## <a name="json-sample"></a>Ejemplo de JSON
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Detalles de la sintaxis

| Propiedad          | Descripción                              | Obligatorio |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nombre de la actividad                     | Sí      |
| description       | Texto que describe para qué se usa la actividad. | No       |
| type              | En Hadoop Streaming Activity, el tipo de actividad es HDInsightStreaming | Sí      |
| linkedServiceName | Referencia al clúster de HDInsight registrado como servicio vinculado. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos). | Sí      |
| mapper            | Especifica el nombre del archivo ejecutable del asignador | Sí      |
| reducer           | Especifica el nombre del archivo ejecutable del reductor | Sí      |
| combiner          | Especifica el nombre del archivo ejecutable del combinador | No       |
| fileLinkedService | Referencia a un servicio vinculado de Azure Storage que se usa para almacenar los programas Asignador, Combinador y Reductor que se van a ejecutar. En este caso solo se admiten servicios vinculados a **[Azure Blob Storage](./connector-azure-blob-storage.md)** y **[ADLS Gen2](./connector-azure-data-lake-storage.md)** . Si no se especifica este servicio vinculado, se usará el servicio vinculado de Azure Storage definido en el servicio vinculado de HDInsight. | No       |
| filePath          | Proporcione una matriz de ruta de acceso a los programas Asignador, Combinador y Reductor almacenados en el almacenamiento de Azure Storage al que fileLinkedService hace referencia. La ruta de acceso distingue mayúsculas de minúsculas. | Sí      |
| input             | Especifica la ruta de acceso de WASB al archivo de entrada para el asignador. | Sí      |
| output            | Especifica la ruta de acceso de WASB al archivo de salida para el reductor. | Sí      |
| getDebugInfo      | Especifica si se copian los archivos de registro en el almacenamiento de Azure Storage que usa el clúster de HDInsight o que está especificado por scriptLinkedService. Valores permitidos: Ninguno, Siempre o Error. Valor predeterminado: Ninguno. | No       |
| argumentos         | Especifica una matriz de argumentos para un trabajo de Hadoop. Los argumentos se pasan a cada tarea como argumentos de la línea de comandos. | No       |
| defines           | Especifique parámetros como pares clave-valor para hacer referencia en el script de Hive. | No       | 

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad de U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad de Hive](transform-data-using-hadoop-hive.md)
* [Actividad de Pig](transform-data-using-hadoop-pig.md)
* [Actividad de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad personalizada de .NET](transform-data-using-dotnet-custom-activity.md)
* [Actividad de ejecución por lotes de ML Studio (clásico)](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)