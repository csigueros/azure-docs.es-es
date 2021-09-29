---
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 850333ff1d0a2668dcd0553f2d03562fb2c046a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624427"
---
Azure Data Factory admite las siguientes actividades de transformación que se pueden agregar a las canalizaciones tanto individualmente como encadenadas a otra actividad.

| Actividad de transformación de datos | Entorno de procesos |
|:--- |:--- |
| [Hive](../data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [de Hadoop](../data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](../data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../data-factory-spark.md) | HDInsight [Hadoop] |
| [Actividades de ML Studio (clásico): ejecución de lotes y recurso de actualización](../data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Procedimiento almacenado](../data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics o SQL Server |
| [U-SQL de análisis con Data Lake](../data-factory-usql-activity.md) |Análisis con Azure Data Lake |
| [DotNet](../data-factory-use-custom-activities.md) |HDInsight [Hadoop] o Azure Batch |

> [!NOTE]
> Puede usar la actividad MapReduce para ejecutar programas Spark en su clúster de HDInsight Spark. Consulte [Invocar programas Spark desde Azure Data Factory](../data-factory-spark.md) para obtener más información.
> Puede crear una actividad personalizada para ejecutar scripts de R en su clúster de HDInsight con R instalado. Consulte [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)(Ejecutar script de R con Data Factory de Azure).
> 
> 

