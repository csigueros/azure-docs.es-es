---
title: Transformación de datos mediante la actividad Pig en Hadoop
description: Aprenda cómo puede usar la actividad de Pig para ejecutar scripts de Pig en un clúster de HDInsight suyo propio o a petición con Azure Data Factory o Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c353b0c92a767adb2196a6268658abeecaed774d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806104"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory-or-synapse-analytics"></a>Transformación de datos mediante la actividad Pig de Hadoop en Azure Data Factory o Synapse Analytics

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-pig-activity.md)
> * [Versión actual](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La actividad Pig de HDInsight en una [canalización](concepts-pipelines-activities.md) de Data Factory ejecuta consultas de Pig en su [propio](compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight o en uno [a petición](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en el artículo sobre [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

Para obtener más información, vea la introducción a [Azure Data Factory](introduction.md) o [Synapse Analytics](../synapse-analytics/overview-what-is.md) y siga el [tutorial de transformación de datos](tutorial-transform-data-spark-powershell.md) antes de leer este artículo. 

## <a name="syntax"></a>Sintaxis

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Propiedad            | Descripción                              | Obligatorio |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nombre de la actividad                     | Sí      |
| description         | Texto que describe para qué se usa la actividad. | No       |
| type                | Para la actividad de Hive, el tipo de actividad es HDinsightPig | Sí      |
| linkedServiceName   | Referencia al clúster de HDInsight registrado como servicio vinculado. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos). | Sí      |
| scriptLinkedService | Referencia a un servicio vinculado de Azure Storage que se utiliza para almacenar el script de Pig que se va a ejecutar. En este caso solo se admiten servicios vinculados a **[Azure Blob Storage](./connector-azure-blob-storage.md)** y **[ADLS Gen2](./connector-azure-data-lake-storage.md)** . Si no se especifica este servicio vinculado, se usará el servicio vinculado de Azure Storage definido en el servicio vinculado de HDInsight. | No       |
| scriptPath          | Proporcione la ruta de acceso al archivo de script almacenado en Azure Storage al que hace referencia scriptLinkedService. El nombre del archivo distingue mayúsculas de minúsculas. | No       |
| getDebugInfo        | Especifica si se copian los archivos de registro en el almacenamiento de Azure Storage que usa el clúster de HDInsight o que está especificado por scriptLinkedService. Valores permitidos: Ninguno, Siempre o Error. Valor predeterminado: Ninguno. | No       |
| argumentos           | Especifica una matriz de argumentos para un trabajo de Hadoop. Los argumentos se pasan a cada tarea como argumentos de la línea de comandos. | No       |
| defines             | Especifique los parámetros como pares de clave y valor para referencia en el script de Pig. | No       |

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad de U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad de Hive](transform-data-using-hadoop-hive.md)
* [Actividad de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad personalizada de .NET](transform-data-using-dotnet-custom-activity.md)
* [Actividad de ejecución por lotes de ML Studio (clásico)](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)