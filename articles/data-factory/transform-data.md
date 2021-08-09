---
title: Transformación de datos
description: Transformación de datos o procesamiento de datos en Azure Data Factory con Hadoop, Azure Machine Learning Studio (clásico) o Azure Data Lake Analytics.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 44fc791fd10517ee52fc2e05ea292b889bd95e11
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110496859"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar datos en Azure Data Factory

> [!div class="op_single_selector"]
> * [Asignación de Data Flow](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [de Hadoop](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (clásico)](transform-data-using-machine-learning.md) 
> * [Procedimiento almacenado](transform-data-using-stored-procedure.md)
> * [U-SQL de análisis con Data Lake](transform-data-using-data-lake-analytics.md)
> * [Cuaderno de Synapse](../synapse-analytics/synapse-notebook-activity.md)
> * [Notebook de Databricks](transform-data-databricks-notebook.md)
> * [Jar en Databricks](transform-data-databricks-jar.md)
> * [Python en Databricks](transform-data-databricks-python.md)
> * [.NET personalizado](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Información general
En este artículo se explican las actividades de transformación de datos de Azure Data Factory que puede usar para transformar y procesar los datos sin procesar en predicciones e información a escala. Una actividad de transformación se ejecuta en un entorno informático, como Azure Databricks o Azure HDInsight. Proporciona vínculos a artículos con información detallada sobre cada actividad de transformación.

Data Factory admite las siguientes actividades de transformación de datos que se pueden agregar a las [canalizaciones](concepts-pipelines-activities.md) tanto individualmente como encadenadas a otra actividad.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Transformación nativa en Azure Data Factory con flujos de datos

### <a name="mapping-data-flows"></a>Asignación de flujos de datos

La asignación de flujos de datos es una transformación de datos diseñada visualmente en Azure Data Factory. Los flujos de datos permiten a los ingenieros de datos desarrollar una lógica de transformación de datos gráfica sin necesidad de escribir código. Los flujos de datos resultantes se ejecutan como actividades en las canalizaciones de Azure Data Factory que usan clústeres de Spark de escalabilidad horizontal. Las actividades de flujo de datos pueden ponerse en marcha mediante las funcionalidades de programación, control, flujo y supervisión existentes en Data Factory. Para más información, consulte [Asignación de flujos de datos](concepts-data-flow-overview.md).

### <a name="data-wrangling"></a>Limpieza y transformación de datos

Power Query en Azure Data Factory permite la limpieza y transformación de datos a escala de nube, lo que permite preparar los datos sin código a escala de nube de forma iterativa. La limpieza y transformación de datos se integra con [Power Query Online](/power-query/) y permite que estén disponibles las funciones de Power Query M para la limpieza y transformación de datos a escala de nube a través de la ejecución de Spark. Para más información, consulte [Limpieza y transformación de datos en ADF](wrangling-overview.md).

## <a name="external-transformations"></a>Transformaciones externas

Opcionalmente, puede codificar manualmente las transformaciones y administrar el entorno de proceso externo.

### <a name="hdinsight-hive-activity"></a>Actividad de HDInsight Hive
La actividad de Hive de HDInsight en una canalización de Data Factory ejecuta consultas de Hive en su propio clúster de HDInsight o en uno a petición basado en Windows/Linux. Vea el artículo [Actividad de Hive](transform-data-using-hadoop-hive.md) para más información sobre esta actividad. 

### <a name="hdinsight-pig-activity"></a>Actividad de HDInsight Pig
La actividad de Pig de HDInsight en una canalización de Data Factory ejecuta consultas de Pig en su propio clúster de HDInsight o en uno a petición basado en Windows/Linux. Vea el artículo [Actividad de Pig](transform-data-using-hadoop-pig.md) para más información sobre esta actividad. 

### <a name="hdinsight-mapreduce-activity"></a>Actividad de MapReduce de HDInsight
La actividad MapReduce de HDInsight en una canalización de Data Factory ejecuta programas de MapReduce en su propio clúster de HDInsight o en uno basado en Windows/Linux a petición. Vea el artículo [Actividad de MapReduce](transform-data-using-hadoop-map-reduce.md) para más información sobre esta actividad.

### <a name="hdinsight-streaming-activity"></a>Actividad de HDInsight Streaming
La actividad de streaming de HDInsight en una canalización de Data Factory ejecuta programas de streaming de Hadoop en su propio clúster de HDInsight o en uno basado en Windows/Linux a petición. Vea [Actividad de HDInsight Streaming](transform-data-using-hadoop-streaming.md) para obtener información sobre esta actividad.

### <a name="hdinsight-spark-activity"></a>Actividad de HDInsight Spark
La actividad de Spark de HDInsight en una canalización de Data Factory ejecuta consultas de Spark en su propio clúster de HDInsight. Consulte [Invoke Spark programs from Azure Data Factory](transform-data-using-spark.md) (Invocar programas Spark desde Data Factory de Azure) para obtener información detallada. 

### <a name="azure-machine-learning-studio-classic-activities"></a>Actividades de Estudio de Azure Machine Learning (clásico)
Azure Data Factory permite crear fácilmente canalizaciones que usan un servicio web de Azure Machine Learning Studio (clásico) publicado para realizar análisis predictivos. Mediante la [actividad de ejecución de lotes](transform-data-using-machine-learning.md) en una canalización de Azure Data Factory, puede invocar un servicio web de Studio (clásico) para realizar predicciones sobre los datos del lote.

Con el tiempo, los modelos predictivos de los experimentos de puntuación de Studio (clásico) se tienen que volver a entrenar con nuevos conjuntos de datos de entrada. Después de terminar con el nuevo entrenamiento, tendrá que actualizar el servicio web de puntuación con el modelo de Machine Learning que volvió a entrenar. Puede usar la [actividad de recursos de actualización](update-machine-learning-models.md) para actualizar el servicio web con el modelo recién entrenado.  

Consulte [Uso de actividades de Azure Machine Learning Studio (clásico)](transform-data-using-machine-learning.md) para más información sobre estas actividades de Studio (clásico). 

### <a name="stored-procedure-activity"></a>Actividad de procedimiento almacenado
Puede usar la actividad de procedimiento almacenado de SQL Server en una canalización de Data Factory para invocar un procedimiento almacenado en uno de los siguientes almacenes de datos: Azure SQL Database, Azure Synapse Analytics y base de datos de SQL Server en una empresa o una máquina virtual de Azure. Vea el artículo [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md) para más información.  

### <a name="data-lake-analytics-u-sql-activity"></a>Actividad de U-SQL de Data Lake Analytics
La actividad de U-SQL de Data Lake Analytics ejecuta un script de U-SQL en un clúster de Azure Data Lake Analytics. Vea el artículo [Actividad de U-SQL de Data Analytics](transform-data-using-data-lake-analytics.md) para más información. 

### <a name="synapse-notebook-activity"></a>Actividad de cuaderno de Synapse 

La actividad de cuaderno de Azure Synapse de una canalización de Synapse ejecuta un cuaderno de Synapse en el área de trabajo de Azure Synapse. Consulte [Transformación de datos mediante la ejecución de un cuaderno de Synapse](../synapse-analytics/synapse-notebook-activity.md).

### <a name="databricks-notebook-activity"></a>Actividad Notebook de Databricks

La actividad Notebook de Azure Databricks en una canalización de Data Factory ejecuta un bloc de notas de Databricks en el área de trabajo de Azure Databricks. Azure Databricks es una plataforma administrada para ejecutar Apache Spark. Consulte [Transformación de datos mediante la ejecución de blocs de notas de Databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Actividad de Jar en Databricks

La actividad de Jar en Azure Databricks en una canalización de Data Factory ejecuta un archivo Jar de Spark en el clúster de Azure Databricks. Azure Databricks es una plataforma administrada para ejecutar Apache Spark. Consulte [Transformación de datos mediante la ejecución de una actividad de Jar en Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Actividad de Python en Databricks

La actividad de Python en Azure Databricks en una canalización de Data Factory ejecuta un archivo de Python en el clúster de Azure Databricks. Azure Databricks es una plataforma administrada para ejecutar Apache Spark. Consulte [Transformación de datos mediante la ejecución de una actividad de Python en Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Actividad personalizada
Si necesita transformar datos de algún modo no compatible con Data Factory, puede crear una actividad personalizada con su propia lógica de procesamiento de datos y usarla en la canalización. Puede configurar una actividad de .NET personalizada para ejecutarse mediante un servicio Azure Batch o un clúster de Azure HDInsight. Consulte el artículo [Utilizar actividades personalizadas](transform-data-using-dotnet-custom-activity.md) para obtener más información. 

Puede crear una actividad personalizada para ejecutar scripts de R en su clúster de HDInsight con R instalado. Consulte [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)(Ejecutar script de R con Data Factory de Azure). 

### <a name="compute-environments"></a>Entornos de proceso
Deberá crear un servicio vinculado para el entorno de proceso y después usar el servicio vinculado al definir una actividad de transformación. La Factoría de datos admite dos tipos de entornos de proceso. 

- **A petición**: en este caso, el entorno informático es completamente administrado por Data Factory. El servicio Factoría de datos lo crea automáticamente antes de que se envíe un trabajo para procesar los datos y que se quite cuando finalice el trabajo. Los usuarios pueden configurar y controlar la configuración granular del entorno de proceso a petición para la ejecución del trabajo, la administración del clúster y las acciones de arranque. 
- **Traiga el suyo propio**: en este caso, puede registrar su propio entorno informático (por ejemplo, clúster de HDInsight) como servicio vinculado en la Factoría de datos. El usuario administra el entorno de procesos y el servicio Factoría de datos lo usa para ejecutar las actividades. 

Vea el artículo [Servicios vinculados de procesos](compute-linked-services.md) para obtener información sobre los servicios vinculados de proceso compatibles con Data Factory. 

## <a name="next-steps"></a>Pasos siguientes
Vea el siguiente tutorial para ver un ejemplo del uso de una actividad de transformación: [Tutorial: Transformación de datos mediante Spark](tutorial-transform-data-spark-powershell.md)
