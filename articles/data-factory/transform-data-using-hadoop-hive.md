---
title: Transformación de datos mediante la actividad de Hive en Hadoop
description: Aprenda cómo puede usar la actividad de Hive en una canalización de Azure Data Factory o Synapse Analytics para ejecutar consultas de Hive en un clúster de HDInsight propio o a petición.
titleSuffix: Azure Data Factory & Azure Synapse
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 8ae040ee7c90b663b45b10aae4565426e318bcc3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806137"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory-or-synapse-analytics"></a>Transformación de datos mediante la actividad de Hive de Hadoop en Azure Data Factory o Synapse Analytics

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-hive-activity.md)
> * [Versión actual](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La actividad de Hive de HDInsight en una [canalización](concepts-pipelines-activities.md) de Azure Data Factory o Synapse Analytics ejecuta consultas de Hive en [su propio](compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight o en uno [a petición](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en el artículo sobre [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

Si es nuevo en Azure Data Factory o Synapse Analytics, vea los artículos de introducción a [Azure Data Factory](introduction.md) o [Synapse Analytics](../synapse-analytics/overview-what-is.md) y siga el [tutorial de transformación de datos](tutorial-transform-data-spark-powershell.md) antes de leer este artículo. 

## <a name="syntax"></a>Sintaxis

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| Propiedad            | Descripción                                                  | Obligatorio |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Nombre de la actividad                                         | Sí      |
| description         | Texto que describe para qué se usa la actividad.                | No       |
| type                | Para la actividad de Hive, el tipo de actividad es HDinsightHive.        | Sí      |
| linkedServiceName   | Referencia al clúster de HDInsight registrado como servicio vinculado. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos). | Sí      |
| scriptLinkedService | Referencia a un servicio vinculado de Azure Storage que se usa para almacenar el script de Hive que se va a ejecutar. En este caso solo se admiten servicios vinculados a **[Azure Blob Storage](./connector-azure-blob-storage.md)** y **[ADLS Gen2](./connector-azure-data-lake-storage.md)** . Si no se especifica este servicio vinculado, se usará el servicio vinculado de Azure Storage definido en el servicio vinculado de HDInsight.  | No       |
| scriptPath          | Proporcione la ruta de acceso al archivo de script almacenado en Azure Storage al que hace referencia scriptLinkedService. El nombre del archivo distingue mayúsculas de minúsculas. | Sí      |
| getDebugInfo        | Especifica si se copian los archivos de registro en el almacenamiento de Azure Storage que usa el clúster de HDInsight o que está especificado por scriptLinkedService. Valores permitidos: Ninguno, Siempre o Error. Valor predeterminado: Ninguno. | No       |
| argumentos           | Especifica una matriz de argumentos para un trabajo de Hadoop. Los argumentos se pasan a cada tarea como argumentos de la línea de comandos. | No       |
| defines             | Especifique parámetros como pares clave-valor para hacer referencia en el script de Hive. | No       |
| queryTimeout        | Valor de tiempo de espera de consulta (en minutos). Aplicable cuando el clúster de HDInsight está habilitado por Enterprise Security Package. | No       |

>[!NOTE]
>El valor predeterminado de queryTimeout es de 120 minutos. 

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad de U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad de Pig](transform-data-using-hadoop-pig.md)
* [Actividad de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad personalizada de .NET](transform-data-using-dotnet-custom-activity.md)
* [Actividad de ejecución por lotes de ML Studio (clásico)](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)
