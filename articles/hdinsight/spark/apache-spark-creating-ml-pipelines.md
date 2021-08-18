---
title: 'Creación de una canalización de aprendizaje automático de Apache Spark: Azure HDInsight'
description: Utilice la biblioteca de aprendizaje automático de Apache Spark para crear canalizaciones de datos en Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 07/22/2019
ms.openlocfilehash: 57f84aaa59ed5277a7dbbb6bb8fd484210808dfb
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678167"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Creación de una canalización de aprendizaje automático de Apache Spark

La biblioteca de aprendizaje automático (MLlib) escalable de Apache Spark ofrece funcionalidades de modelado a un entorno distribuido. El paquete de Spark [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) es un conjunto de API de alto nivel basadas en DataFrames. Estas API permiten crear y ajustar prácticas canalizaciones de aprendizaje automático.  *Aprendizaje automático de Spark* hace referencia a esta API basada en la DataFrame de MLlib, no a la antigua API de canalización basada en RDD.

Una canalización de aprendizaje automático (ML) es un flujo de trabajo completo que combina varios algoritmos de aprendizaje automático. Pueden necesitarse muchos pasos para el procesamiento de datos y el aprendizaje a partir de estos, lo que requiere una secuencia de algoritmos. Las canalizaciones definen las fases y el orden de un proceso de aprendizaje automático. En MLlib, las fases de una canalización se representan mediante una secuencia específica de fases de canalización, donde un transformador y un estimador realizan tareas.

Un transformador es un algoritmo que transforma un elemento DataFrame en otro con el método `transform()`. Por ejemplo, un transformador de características puede leer una columna de un elemento DataFrame, asignarla a otra columna y generar un nuevo elemento DataFrame con la columna asignada anexada.

Un estimador es una abstracción de algoritmos de aprendizaje y es responsable del ajuste o el aprendizaje en un conjunto de datos para producir un transformador. Un estimador implementa un método denominado `fit()`, que acepta un elemento DataFrame y produce un elemento DataFrame, que es un transformador.

Cada instancia sin estado de un transformador o un estimador tiene su propio identificador único, que se usa cuando se especifican parámetros. Ambos usan una API uniforme para especificar estos parámetros.

## <a name="pipeline-example"></a>Ejemplo de canalización

Para mostrar un uso práctico de una canalización de ML, en este ejemplo se usa el archivo de datos `HVAC.csv` de ejemplo que está precargado en el almacenamiento predeterminado para el clúster HDInsight (Azure Storage o Data Lake Storage). Para ver el contenido del archivo, navegue hasta el directorio `/HdiSamples/HdiSamples/SensorSampleData/hvac`. `HVAC.csv` contiene un conjunto de horas con las temperaturas objetivo y reales de los sistemas HVAC (*calefacción, ventilación y aire acondicionado*) de varios edificios. El objetivo es entrenar el modelo con los datos y producir una previsión de temperatura para un edificio determinado.

El código siguiente:

1. Se define un elemento `LabeledDocument`, que almacena los objetos `BuildingID`, `SystemInfo` (identificador y antigüedad del sistema) y `label` (1,0 si la temperatura del edificio es demasiado elevada, 0,0 en otro caso).
2. Se crea una función personalizada del analizador `parseDocument` que toma una línea (fila) de datos y determina si la temperatura del edificio es elevada comparando la temperatura objetivo con la temperatura real.
3. Se aplica el analizador al extraer los datos de origen.
4. Se crean datos de aprendizaje.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument


def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)


# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Esta canalización de ejemplo tiene tres fases: `Tokenizer` y `HashingTF` (ambas, transformadores) y `Logistic Regression` (estimador).  Los datos extraídos y analizados en los flujos de DataFrame `training` fluyen a través de la canalización cuando se llama a `pipeline.fit(training)`.

1. En la primera fase, `Tokenizer`, se divide la columna de entrada `SystemInfo` (que consta de los valores de identificador y antigüedad del sistema) en una columna de salida `words`. Esta nueva columna `words` se agrega a DataFrame. 
2. En la segunda fase, `HashingTF`, se convierte la nueva columna `words` en vectores de característica. Esta nueva columna `features` se agrega al elemento DataFrame. Estas dos primeras fases son transformadores. 
3. La tercera fase, `LogisticRegression`, es un estimador, por lo que la canalización llama al método `LogisticRegression.fit()` para generar un modelo `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Para ver las nuevas columnas `words` y `features` agregadas por los transformadores `Tokenizer` y `HashingTF`, además de un ejemplo del estimador `LogisticRegression`, ejecute un método `PipelineModel.transform()` en el elemento DataFrame original. En el código de producción, el siguiente paso sería pasar un elemento DataFrame de prueba para validar el aprendizaje.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

Ahora puede usarse el objeto `model` para realizar predicciones. Para obtener el ejemplo completo de esta aplicación de aprendizaje automático, así como instrucciones detalladas para ejecutarla, consulte [Compilación de aplicaciones de aprendizaje automático de Apache Spark en Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Vea también

* [Ciencia de datos mediante Scala y Apache Spark en Azure](/azure/architecture/data-science-process/scala-walkthrough)
