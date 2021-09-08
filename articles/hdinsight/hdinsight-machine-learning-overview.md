---
title: 'Información general del aprendizaje automático: Azure HDInsight'
description: Información general sobre las opciones de aprendizaje automático de macrodatos para clústeres de Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: a911e468443fe49bb1edc18af3d3412edc8eb8cc
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678223"
---
# <a name="machine-learning-on-hdinsight"></a>Aprendizaje automático en HDInsight

HDInsight permite el aprendizaje automático con macrodatos, lo que proporciona la capacidad de obtener información valiosa de grandes cantidades de datos estructurados y no estructurados (petabytes o incluso exabytes), y en rápido movimiento. Hay varias opciones de aprendizaje automático en HDInsight: SparkML y Apache Spark MLlib, Apache Hive y Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML y MLlib

[HDInsight Spark](spark/apache-spark-overview.md) es una oferta de [Apache Spark](https://spark.apache.org/) hospedada por Azure. Consiste en una plataforma de procesamiento paralelo de datos de código abierto y unificada que admite el procesamiento en memoria para agilizar el análisis de macrodatos. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las capacidades de cálculo distribuido en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. Hay dos bibliotecas escalables de aprendizaje automático que ofrecen las funcionalidades del modelado algorítmico a este entorno distribuido: MLlib y SparkML. MLlib contiene la API original que se basa en RDD. SparkML es un paquete más reciente que proporciona una API de nivel más alto que se basa en DataFrames para construir canalizaciones ML. SparkML aún no admite todas las características de MLlib, pero está reemplazándola como biblioteca de aprendizaje automático estándar de Spark.

La biblioteca Microsoft Machine Learning para Apache Spark es [MMLSpark](https://github.com/Azure/mmlspark). Esta biblioteca se ha diseñado para que los científicos de datos sean más productivos en Spark, aumenten la velocidad de experimentación y aprovechen las técnicas de aprendizaje automático de vanguardia, así como el aprendizaje profundo, en grandes conjuntos de datos. MMLSpark proporciona una capa sobre las API de bajo nivel de SparkML cuando se crean modelos de aprendizaje automático escalables, como las cadenas de indexación, la conversión de datos en un diseño esperado por los algoritmos de aprendizaje automático y el ensamblado de vectores de características. La biblioteca MMLSpark simplifica estas y otras tareas comunes para la creación de modelos en PySpark.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning y Apache Hive

Azure Machine Learning proporciona herramientas para análisis predictivo de modelos y un servicio totalmente administrado que se puede usar para implementar los modelos predictivos como servicios web listos para consumir. Azure Machine Learning proporciona una completa solución de análisis predictivo en la nube que se puede usar para crear modelos predictivos, probarlos, ponerlos en funcionamiento y administrarlos. Seleccione entre una biblioteca de algoritmos de gran tamaño, utilice un estudio basado en web para la compilación de modelos e implemente fácilmente el modelo como un servicio web.

:::image type="content" source="./media/hdinsight-machine-learning-overview/azure-machine-learning.png" alt-text="Información general de Microsoft Azure Machine Learning" border="false":::

Cree características para los datos de un clúster de Hadoop en HDInsight mediante [consultas de Hive](/azure/architecture/data-science-process/create-features-hive). *La ingeniería de características* intenta aumentar la eficacia predictiva de los algoritmos de aprendizaje creando características a partir de los datos sin procesar que facilitan el proceso de aprendizaje. Puede ejecutar consultas de HiveQL desde Azure Machine Learning Studio (clásico) y obtener acceso a los datos procesados en Hive y almacenados en blobs, mediante el uso del [módulo de importación de datos](../machine-learning/classic/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

El [aprendizaje profundo](https://www.microsoft.com/en-us/research/group/dltc/) es una rama del aprendizaje automático que utiliza redes neurales profundas, inspiradas por los procesos biológicos del cerebro humano. Muchos investigadores ven el aprendizaje profundo como un enfoque prometedor para mejorar la inteligencia artificial. Algunos ejemplos de aprendizaje profundo son los intérpretes automáticos de idiomas, los sistemas de reconocimiento de imágenes y el razonamiento automático.

Con el fin de realizar avances en su trabajo de aprendizaje profundo, Microsoft ha desarrollado el kit de herramientas [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) de código abierto, gratuito y fácil de usar. Actualmente, el kit de herramientas lo usa una amplia variedad de productos de Microsoft, compañías de todo el mundo con necesidad de implementar el aprendizaje profundo a escala y estudiantes interesados en los algoritmos y las técnicas más recientes.

## <a name="see-also"></a>Consulte también

### <a name="scenarios"></a>Escenarios

* [Apache Spark con Machine Learning: Uso de Spark en HDInsight para analizar la temperatura de un edificio mediante datos de HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generación de recomendaciones de películas con Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive y Azure Machine Learning](/azure/architecture/data-science-process/create-features-hive)
* [Apache Hive y Azure Machine Learning de un extremo a otro](/azure/architecture/data-science-process/hive-walkthrough)
* [Aprendizaje automático con Apache Spark en HDInsight](/azure/architecture/data-science-process/spark-overview)

### <a name="deep-learning-resources"></a>Recursos de aprendizaje profundo

* [Uso del modelo de aprendizaje profundo de Microsoft Cognitive Toolkit con un clúster de Azure HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Marcos de aprendizaje profundo e inteligencia artificial en Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
