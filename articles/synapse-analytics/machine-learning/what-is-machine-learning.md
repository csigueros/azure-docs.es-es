---
title: Aprendizaje automático en Azure Synapse Analytics
description: Información general de las funcionalidades de aprendizaje automático en Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 10/01/2021
author: nelgson
ms.author: negust
ms.openlocfilehash: e092cf94e5126b5bfa7bd42cc2f362f6d7a9da96
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402105"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Funcionalidades de Machine Learning en Azure Synapse Analytics

Azure Synapse Analytics ofrece distintas funcionalidades de aprendizaje automático. En este artículo se proporciona información general sobre cómo se puede aplicar el aprendizaje automático en el contexto de Azure Synapse.

En esta información general se describen las distintas funcionalidades de Synapse relacionadas con el aprendizaje automático, desde la perspectiva del proceso de ciencia de datos.

Es posible que esté familiarizado con el aspecto de un proceso típico de ciencia de datos. Se trata de un proceso conocido, que siguen la mayoría de los proyectos de aprendizaje automático.

En un nivel superior, el proceso contiene los siguientes pasos:
* Comprensión empresarial (no se trata en este artículo)
* Adquisición y comprensión de los datos
* Modelado
* Implementación y puntuación de modelos

En este artículo se describen las funcionalidades de aprendizaje automático de Azure Synapse en diferentes motores de análisis, desde una perspectiva de proceso de ciencia de datos. Se resumen las funcionalidades de Azure Synapse que pueden ayudar para cada paso del proceso de ciencia de datos.

## <a name="data-acquisition-and-understanding"></a>Adquisición y comprensión de los datos

La mayoría de los proyectos de aprendizaje automático implican pasos bien establecidos y uno de estos pasos es acceder a los datos y comprenderlos.

### <a name="data-source-and-pipelines"></a>Origen de datos y canalizaciones

Gracias a [Azure Data Factory](../../data-factory/introduction.md), una parte integrada de forma nativa de Azure Synapse, hay un conjunto eficaz de herramientas disponibles para las canalizaciones de ingesta de datos y de orquestación de datos. Esto le permite crear fácilmente canalizaciones de datos para acceder a los datos y transformarlos a un formato que se pueda consumir para el aprendizaje automático. [Obtenga más información sobre las canalizaciones de datos](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) en Synapse. 

### <a name="data-preparation-and-explorationvisualization"></a>Preparación de datos y su exploración o visualización

Una parte importante del proceso de aprendizaje automático es comprender los datos mediante la exploración y las visualizaciones.

En función de dónde se almacenen los datos, Synapse ofrece un conjunto diferente de herramientas para explorar y prepararlos para el análisis y el aprendizaje automático. Una de las maneras más rápidas de empezar a trabajar con la exploración de datos es usar Apache Spark o grupos de SQL sin servidor directamente sobre los datos del lago de datos.

* [Apache Spark para Azure Synapse](../spark/apache-spark-overview.md) ofrece funcionalidades para transformar, preparar y explorar los datos a escala. Estos grupos de Spark ofrecen herramientas como PySpark/Python, Scala y .NET para el procesamiento de datos a escala. La experiencia de exploración de datos se puede mejorar con eficaces bibliotecas de visualización para ayudar a comprender mejor los datos. [Obtenga más información sobre cómo explorar y visualizar datos en Synapse con Spark](../get-started-analyze-spark.md).

* Los [grupos de SQL sin servidor](../sql/on-demand-workspace-overview.md) ofrecen una manera de explorar los datos mediante TSQL directamente sobre el lago de datos. Los grupos de SQL sin servidor también ofrecen algunas visualizaciones integradas en Synapse Studio. [Aprenda a explorar datos con grupos de SQL sin servidor](../get-started-analyze-sql-on-demand.md).

## <a name="modeling"></a>Modelado

En Azure Synapse, el entrenamiento de los modelos de Machine Learning se puede realizar en los grupos de Apache Spark con herramientas como PySpark/Python, Scala o .NET.

### <a name="train-models-on-spark-pools-with-mllib"></a>Entrenamiento de modelos en grupos de Spark con MLlib

Los modelos de Machine Learning se pueden entrenar con la ayuda de varios algoritmos y bibliotecas. [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) ofrece algoritmos de aprendizaje automático escalables que pueden ayudar a resolver la mayoría de los problemas clásicos de aprendizaje automático. Para ver un tutorial sobre cómo entrenar un modelo mediante MLlib en Synapse, consulte [Compilación de una aplicación de aprendizaje automático con Apache Spark MLlib y Azure Synapse Analytics](../spark/apache-spark-machine-learning-mllib-notebook.md).

Además de MLlib, también se pueden usar bibliotecas populares, como [Scikit Learn](https://scikit-learn.org/stable/), para desarrollar modelos. Para más información sobre cómo instalar bibliotecas en grupos de Synapse Spark, consulte [Administración de bibliotecas para Apache Spark en Azure Synapse Analytics](../spark/apache-spark-azure-portal-add-libraries.md).

### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Entrenamiento de modelos con aprendizaje automático automatizado de Azure Machine Learning

Otra manera de entrenar modelos de Machine Learning, que no requiere estar muy familiarizado con el aprendizaje automático, es usar ML automatizado. [ML automatizado](../../machine-learning/concept-automated-ml.md) es una característica que entrena automáticamente un conjunto de modelos de Machine Learning y permite al usuario seleccionar el mejor modelo en función de métricas específicas. Gracias a una perfecta integración con Azure Machine Learning de los cuadernos de Azure Synapse, los usuarios pueden usar fácilmente ML automatizado en Synapse con la autenticación de paso a través de Azure Active Directory.  Esto significa que solo tiene que apuntar a su área de trabajo de Azure Machine Learning y no es necesario que escriba ninguna credencial. El tutorial [Entrenamiento de un modelo en Python mediante el aprendizaje automático automatizado](../spark/apache-spark-azure-machine-learning-tutorial.md) describe cómo entrenar modelos mediante ML automatizado de Azure Machine Learning en grupos de Spark de Synapse.

## <a name="model-deployment-and-scoring"></a>Implementación y puntuación de modelos

Los modelos entrenados tanto en Azure Synapse como fuera de Azure Synapse se pueden usar fácilmente para la puntuación por lotes. Actualmente en Synapse, hay dos maneras en las que puede ejecutar la puntuación por lotes.

* Puede usar la [función TSQL PREDICT](../sql-data-warehouse/sql-data-warehouse-predict.md) en grupos de Synapse SQL para ejecutar sus predicciones donde residen los datos. Esta función eficaz y escalable le permite enriquecer sus datos sin moverlos fuera de su almacenamiento de datos. Se introdujo una nueva [experiencia guiada de modelo de Machine Learning en Synapse Studio](./tutorial-sql-pool-model-scoring-wizard.md), donde puede implementar un modelo de ONNX desde el registro de modelos de Azure Machine Learning en grupos de Synapse SQL para la puntuación por lotes con PREDICT.

* Otra opción para los modelos de Machine Learning de puntuación por lotes en Azure Synapse es aprovechar los grupos de Apache Spark para Azure Synapse. En función de las bibliotecas usadas para entrenar los modelos, puede usar una experiencia de código para ejecutar la puntuación por lotes.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure Synapse Analytics](../get-started.md)
* [Creación de un área de trabajo](../get-started-create-workspace.md)
* [Inicio rápido: Creación de un servicio vinculado de Azure Machine Learning en Synapse](quickstart-integrate-azure-machine-learning.md)
* [Tutorial: Asistente para puntuación de modelos de Machine Learning: grupo de SQL dedicado](tutorial-sql-pool-model-scoring-wizard.md)