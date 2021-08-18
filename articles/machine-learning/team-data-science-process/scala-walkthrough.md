---
title: 'Ciencia de datos con Scala y Spark en Azure: Proceso de ciencia de datos en equipo'
description: Este artículo muestra cómo utilizar Scala para tareas de aprendizaje automático supervisado con los paquetes MLlib escalable y ML de Spark en un clúster de Spark de HDInsight de Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1e038b270398de45245aa9f7c88bc87c2f0e6d2d
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112285326"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Ciencia de datos mediante Scala y Spark en Azure
Este artículo muestra cómo utilizar Scala para tareas de aprendizaje automático supervisado con los paquetes MLlib escalable y ML de Spark en un clúster de Spark en HDInsight de Azure. Además, se explican cuáles son las tareas que constituyen el [proceso de ciencia de datos](./index.yml): exploración e ingesta de datos, visualización, ingeniería de características, modelado y consumo de modelos. Los modelos en el artículo incluyen regresión logística y lineal, bosques aleatorios y árboles incrementados de degradado (GBTs), además de dos tareas habituales de aprendizaje automático supervisado:

* Problema de regresión: predicción de propinas (en dólares) por una carrera de taxi
* Clasificación binaria: predicción de si se dará propina o no (1/0) en una carrera de taxi

Para llevar a cabo el proceso de modelado hay que realizar entrenamientos y evaluaciones en conjuntos de datos de pruebas y métricas de precisión pertinentes. En este artículo se describe cómo almacenar estos modelos en el Almacenamiento de blobs de Azure, además de puntuar y evaluar su rendimiento predictivo. También se tratan temas más avanzados sobre cómo optimizar modelos mediante validación cruzada y barridos de hiperparámetros. Los datos que se utilizan son un ejemplo del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York en 2013 disponible en GitHub.

[Scala](https://www.scala-lang.org/)es un lenguaje basado en la Máquina virtual Java que integra los conceptos del lenguaje funcional y la programación orientada a objetos. Se trata de un lenguaje escalable apropiado para efectuar el procesamiento distribuido en la nube y que se ejecuta en los clústeres de Spark de Azure.

[Spark](https://spark.apache.org/) es una plataforma de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las capacidades de cálculo distribuido en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. El paquete [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) proporciona un conjunto uniforme de API de alto nivel creadas a partir de tramas de datos que ayudan a crear y ajustar canalizaciones prácticas de aprendizaje automático. [MLlib](https://spark.apache.org/mllib/) es la biblioteca de aprendizaje automático escalable de Spark que ofrece funcionalidades de modelado en este entorno distribuido.

[Spark en HDInsight](../../hdinsight/spark/apache-spark-overview.md) es la oferta de Spark de código abierto hospedada por Azure. Asimismo, incluye compatibilidad con cuadernos de Scala de Jupyter Notebook en el clúster de Spark, y permite ejecutar consultas interactivas de Spark SQL para transformar, filtrar y visualizar los datos almacenados en Almacenamiento de blobs de Azure. Los fragmentos de código de Scala en este artículo proporcionan las soluciones y muestran los trazados pertinentes para visualizar los datos que se ejecutan en cuadernos de Jupyter Notebook instalados en los clústeres de Spark. En estos temas, los pasos de modelado también contienen código que muestra cómo entrenar, evaluar, guardar y usar cada tipo de modelo.

Los pasos de instalación y el código de este artículo están diseñados para Spark 1.6 con HDInsight de Azure 3.4. Sin embargo, este código y el de [Jupyter Notebook de Scala](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) son genéricos y deberían funcionar en cualquier clúster de Spark. Los pasos de configuración y administración del clúster pueden ser ligeramente diferentes de los que se muestran aquí si no está usando Spark en HDInsight.

> [!NOTE]
> Si quiere leer un tema que muestre cómo utilizar Python en lugar de Scala para completar las tareas de un proceso de ciencia de datos de un extremo a otro, consulte [Información general sobre la ciencia de los datos con Spark en HDInsight de Azure](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* Debe tener una suscripción de Azure. Si aún no tiene una, [consiga una evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Necesita un clúster de Spark 1.6 con HDInsight de Azure 3.4 para completar los procedimientos siguientes. Para crear un clúster, consulte las instrucciones proporcionadas en [Introducción: creación de clústeres Apache Spark en HDInsight para Linux y ejecución de consultas interactivas mediante Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Establezca el tipo de clúster y la versión en el menú **Seleccionar tipo de clúster** .

![Configuración de tipo de clúster de HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../hdinsight/includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Para ver una descripción de los datos de taxis de Nueva York y las instrucciones para ejecutar código desde un cuaderno de Jupyter Notebook en el clúster de Spark, consulte las secciones apropiadas de [Información general sobre la ciencia de los datos con Spark en HDInsight de Azure](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Ejecución del código de Scala desde un cuaderno de Jupyter Notebook del clúster Spark
Puede iniciar un cuaderno de Jupyter Notebook desde el Portal de Azure. Busque el clúster de Spark en el panel y haga clic en él para entrar en la página de administración del clúster. Después, haga clic en **Paneles de clúster** y, después, en **Jupyter Notebook** para abrir el cuaderno asociado al clúster de Spark.

![Panel del clúster y cuadernos de Jupyter Notebook](./media/scala-walkthrough/spark-jupyter-on-portal.png)

También puede ir a https://&lt;nombreDeClúster&gt;.azurehdinsight.net/jupyter para acceder a los cuadernos de Jupyter Notebook. Reemplace *clustername* por el nombre del clúster. Necesitará la contraseña de su cuenta de administrador para acceder a los cuadernos de Jupyter Notebook.

![Vaya a Jupyter Notebook mediante el nombre de clúster](./media/scala-walkthrough/spark-jupyter-notebook.png)

Seleccione **Scala** para ver un directorio con algunos ejemplos de cuadernos preempaquetados que utilizan la API PySpark. La exploración de modelado y puntuación mediante el cuaderno de Scala.ipynb con muestras de código de este conjunto de temas Spark está disponible en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Puede cargar el cuaderno directamente desde GitHub en el servidor de Jupyter Notebook del clúster de Spark. En la página principal de Jupyter, haga clic en el botón **Upload** (Cargar). En el Explorador de archivos, pegue la URL de GitHub (contenido sin procesar) del cuaderno Scala y haga clic en **Open**(Abrir). El cuaderno de Scala está disponible en la siguiente dirección URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Instalación: contextos preestablecidos de Spark y Hive, instrucciones mágicas de Spark y bibliotecas de Spark
### <a name="preset-spark-and-hive-contexts"></a>Contextos preestablecidos de Spark y Hive

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

Los kernels de Spark que se proporcionan con cuadernos de Jupyter Notebook tienen contextos preestablecidos. No es necesario establecer explícitamente los contextos de Spark o Hive antes de empezar a trabajar con la aplicación que se esté desarrollando. Los contextos preestablecidos son:

* `sc` para SparkContext
* `sqlContext` para HiveContext

### <a name="spark-magics"></a>Instrucciones mágicas de Spark
El kernel de Spark proporciona algunas instrucciones "mágicas" predefinidas, que son comandos especiales a los que pueden llamarse con `%%`. Dos de estos comandos se utilizan en los siguientes ejemplos de código.

* `%%local` especifica que el código de las líneas siguientes se ejecutará localmente. El código debe estar en el lenguaje Scala.
* `%%sql -o <variable name>` ejecuta una consulta de Hive en `sqlContext`. Si se pasa el parámetro `-o`, el resultado de la consulta se conserva en el contexto de Scala `%%local` como trama de datos de Spark.

Para más información sobre los kernels de cuadernos de Jupyter Notebook y las "instrucciones mágicas" predefinidas que llama con `%%` (por ejemplo, `%%local`), vea [Kernels disponibles para cuadernos de Jupyter con clústeres de Apache Spark en HDInsight Linux](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importación de bibliotecas
Importe las bibliotecas de Spark, MLlib y otras que necesite con el siguiente código.

```scala
# IMPORT SPARK AND JAVA LIBRARIES
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.functions._
import java.text.SimpleDateFormat
import java.util.Calendar
import sqlContext.implicits._
import org.apache.spark.sql.Row

# IMPORT SPARK SQL FUNCTIONS
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
import org.apache.spark.sql.functions.rand

# IMPORT SPARK ML FUNCTIONS
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

# IMPORT SPARK MLLIB FUNCTIONS
import org.apache.spark.mllib.linalg.{Vector, Vectors}
import org.apache.spark.mllib.util.MLUtils
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

# SPECIFY SQLCONTEXT
val sqlContext = new SQLContext(sc)
```

## <a name="data-ingestion"></a>Ingesta de datos
El primer paso en el proceso de ciencia de datos es introducir los datos que desea analizar. Se traen los datos de orígenes o sistemas externos en los que residan a su entorno de exploración y modelado de datos. En este artículo, los datos introducidos representan conjuntamente un 0,1 % del archivo de carreras y tarifas de taxi (almacenado como un archivo .tsv). El entorno de exploración y modelado de datos es Spark. Esta sección contiene el código para completar una serie de tareas:

1. Establecer rutas de acceso a directorios para almacenar datos y modelos.
2. Leer el conjunto de datos de entrada (almacenado como un archivo .tsv).
3. Definir un esquema para los datos y limpiarlos.
4. Crear una trama de datos limpia y almacenarla en la memoria caché.
5. Registrar los datos como una tabla temporal en SQLContext.
6. Consultar la tabla e importar los resultados en una trama de datos.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Establecer rutas de acceso de directorio para las ubicaciones de almacenamiento en el Almacenamiento de blobs de Azure.
Spark puede leer y escribir en el Almacenamiento de blobs de Azure. Puede usar Spark para procesar cualquiera de los datos existentes y almacenar los resultados en el Almacenamiento de blobs.

Para guardar modelos o archivos en el Almacenamiento de blobs, debe especificar la ruta de acceso de forma adecuada. Se puede hacer referencia al contenedor predeterminado asociado al clúster de Spark con un ruta que comience con `wasb:///`. Haga referencia a otras ubicaciones mediante `wasb://`.

El siguiente ejemplo de código especifica la ubicación de los datos de entrada que se leerán y la ruta de acceso al Almacenamiento de blobs conectado al clúster de Spark donde se guardará el modelo.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importación de datos, creación de un conjunto de datos distribuidos resistentes (RDD) y definición de tramas de datos según el esquema

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
val sqlContext = new SQLContext(sc)
val taxi_schema = StructType(
    Array(
        StructField("medallion", StringType, true),
        StructField("hack_license", StringType, true),
        StructField("vendor_id", StringType, true),
        StructField("rate_code", DoubleType, true),
        StructField("store_and_fwd_flag", StringType, true),
        StructField("pickup_datetime", StringType, true),
        StructField("dropoff_datetime", StringType, true),
        StructField("pickup_hour", DoubleType, true),
        StructField("pickup_week", DoubleType, true),
        StructField("weekday", DoubleType, true),
        StructField("passenger_count", DoubleType, true),
        StructField("trip_time_in_secs", DoubleType, true),
        StructField("trip_distance", DoubleType, true),
        StructField("pickup_longitude", DoubleType, true),
        StructField("pickup_latitude", DoubleType, true),
        StructField("dropoff_longitude", DoubleType, true),
        StructField("dropoff_latitude", DoubleType, true),
        StructField("direct_distance", StringType, true),
        StructField("payment_type", StringType, true),
        StructField("fare_amount", DoubleType, true),
        StructField("surcharge", DoubleType, true),
        StructField("mta_tax", DoubleType, true),
        StructField("tip_amount", DoubleType, true),
        StructField("tolls_amount", DoubleType, true),
        StructField("total_amount", DoubleType, true),
        StructField("tipped", DoubleType, true),
        StructField("tip_class", DoubleType, true)
        )
    )

# CAST VARIABLES ACCORDING TO THE SCHEMA
val taxi_temp = (taxi_train_file.map(_.split("\t"))
                        .filter((r) => r(0) != "medallion")
                         .map(p => Row(p(0), p(1), p(2),
                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


# CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

# CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Salida:**

Tiempo de ejecución de la celda: 8 segundos.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Consulta de la tabla e importación de resultados en una trama de datos
Ahora, consulte la tabla para obtener datos de tarifas, pasajeros y propinas, filtrar la información dañada y no relevante, e imprimir varias filas.

```scala
# QUERY THE DATA
val sqlStatement = """
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train
    WHERE passenger_count > 0 AND passenger_count < 7
    AND fare_amount > 0 AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 AND tip_amount < 25
"""
val sqlResultsDF = sqlContext.sql(sqlStatement)

# SHOW ONLY THE TOP THREE ROWS
sqlResultsDF.show(3)
```

**Salida:**

| fare_amount | passenger_count | tip_amount | tipped |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Visualización y exploración de datos
Una vez incorporados los datos en Spark, el siguiente paso del proceso de la ciencia de los datos es conocer mejor los datos mediante la exploración y la visualización. En esta sección se examinan los datos de taxi mediante consultas SQL. Después, se importan los resultados en una trama de datos para trazar las variables de destino y las posibles características para inspeccionarlas de manera visual mediante la característica de visualización automática de Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Uso de instrucciones mágicas de SQL y locales para trazar datos
De forma predeterminada, el resultado de cualquier fragmento de código que se ejecuta desde un cuaderno de Jupyter Notebook está disponible en el contexto de la sesión que se conserva en los nodos de trabajo. Si quiere guardar una carrera en los nodos de trabajo de cada cálculo y todos los datos que necesita para dichos cálculos están disponibles de forma local en el nodo del servidor de Jupyter (el nodo principal), puede utilizar la instrucción mágica `%%local` para ejecutar el fragmento de código en el servidor de Jupyter.

* **Instrucciones mágicas SQL** (`%%sql`). El kernel de Spark en HDInsight admite consultas sencillas de HiveQL en línea en SQLContext. El argumento (`-o VARIABLE_NAME`) conserva la salida de la consulta SQL como una trama de datos de Pandas en el servidor de Jupyter. Este valor significa que el resultado estará disponible en el modo local.
* `%%local` **Instrucciones mágicas** Las instrucciones mágicas `%%local` se utilizan para ejecutar código de forma local en el servidor de Jupyter, que es el nodo principal del clúster de HDInsight. Normalmente, se utilizan juntas las instrucciones mágicas `%%local` y `%%sql` con el parámetro `-o`. El parámetro `-o` conservaría la salida de la consulta SQL localmente y luego la instrucción mágica `%%local` desencadenaría el siguiente conjunto de fragmento de código para ejecutarse localmente en la salida de las consultas SQL que se conserva localmente.

### <a name="query-the-data-by-using-sql"></a>Consulta de datos mediante SQL
Esta consulta recupera carreras de taxi por importe de la tarifa, número de pasajeros y propina.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

En el código siguiente, la instrucción mágica `%%local` crea una trama de datos local, sqlResults. Puede utilizar sqlResults para el trazado mediante matplotlib.

> [!TIP]
> La instrucción mágica local se emplea varias veces en este tutorial. Si la cantidad de datos es elevada, realice un muestreo para crear una trama de datos que pueda caber en la memoria local.
> 
> 

### <a name="plot-the-data"></a>Trazado de datos
Se pueden trazar datos con código Python cuando la trama de datos esté en el contexto local como trama de datos de Pandas.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 Tras ejecutar el código, el kernel de Spark visualiza automáticamente el resultado de las consultas SQL (HiveQL). Puede elegir entre varios tipos de visualizaciones:

* Tabla
* Gráfico circular
* Línea
* Área
* Barras

Este es el código para trazar los datos:

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# PLOT TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.show()
```

**Salida:**

![Histograma del importe de la propina](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Importe de las propinas por número de pasajeros](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Importe de las propinas por importe de la tarifa](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Creación y transformación de características, y preparación de datos para su entrada en funciones de modelado
Para utilizar las funciones de modelado de árbol en SparkML y MLlib, el destino y las características se deben preparar mediante diferentes técnicas, como discretización, indexación, codificación "one-hot" y vectorización. Estos son los procedimientos que seguir en esta sección:

1. Creación de una nueva característica mediante la **discretización** de horas en ciclos de tráfico.
2. Aplicación de **indexación y codificación "one-hot"** a características categóricas.
3. **Muestreo y división de conjuntos de datos** en fracciones de entrenamiento y prueba.
4. **Especificación de variables de entrenamiento y características**, y creación de tramas de datos o datos distribuidos resistentes (RDD) de punto con etiqueta de entrada para entrenamientos y pruebas mediante indexación o codificación "one-hot".
5. **Categorización y vectorización automáticas de características y destinos** con el objetivo de utilizarlas como entradas para los modelos de aprendizaje automático.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Creación de una nueva característica mediante la discretización de horas en cubos de tiempo de tráfico
Este código muestra cómo crear una nueva característica mediante la discretización de horas en ciclos de tráfico y cómo almacenar en caché la trama de datos resultante en memoria. Cuando se usan repetidamente conjuntos de datos distribuidos resistentes (RDD) y tramas de datos, el almacenamiento en caché mejora los tiempos de ejecución. Por lo tanto, almacenaremos en caché los RDD y las tramas de datos en varias fases en los procedimientos siguientes.

```scala
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
val sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train
"""
val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexación y codificación "one-hot" de características categóricas
Las funciones de modelado y predicción de MLlib requieren características con datos de entrada categóricos indexados o codificados antes de usarlos. Esta sección muestra cómo indexar o codificar las características categóricas para su entrada en las funciones de modelado.

Dependiendo del modelo, deberá indexarlo o codificarlo de maneras diferentes. Por ejemplo, los modelos de regresión logística y lineal requieren una codificación "one-hot". Asimismo, una función con tres categorías puede ampliarse en tres columnas de característica. Cada columna contiene 0 o 1 según la categoría de una observación. MLlib proporciona la función [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para realizar la codificación "one-hot". Este codificador asigna una columna de índices de etiqueta a una columna de vectores binarios, con un solo valor uno como máximo. Esta codificación permite aplicar algoritmos que esperan características con valores numéricos, como la regresión logística, a características categóricas.

Aquí solo transformamos cuatro variables para mostrar los ejemplos, que son cadenas de caracteres. También se pueden indexar otras variables como variables categóricas (por ejemplo, el día de la semana) que se representan mediante valores numéricos.

Para la indexación usamos `StringIndexer()`, y para la codificación "one-hot", funciones `OneHotEncoder()` de MLlib. Este es el código para indexar y codificar características categóricas:

```scala
# CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# INDEX AND ENCODE VENDOR_ID
val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
val encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
val indexed = stringIndexer.transform(encoded1)
val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
val encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
val indexed = stringIndexer.transform(encoded2)
val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
val encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
val indexed = stringIndexer.transform(encoded3)
val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
val encodedFinal = encoder.transform(indexed)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Salida:**

Tiempo de ejecución de la celda: 4 segundos.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Muestreo y división de conjuntos de datos en fracciones de entrenamiento y prueba
Este código crea una muestra aleatoria de los datos (en este ejemplo, el 25 %). Aunque no es necesario para este ejemplo debido al tamaño del conjunto de datos, el artículo describe cómo realizar la muestra para que sepa cómo hacerlo cuando lo necesite. Cuando las muestras son grandes, esto puede ahorrar mucho tiempo al entrenar modelos. Después, se divide la muestra en una parte de entrenamiento (en este ejemplo, el 75 %) y una parte de pruebas (25 %) para el modelado de clasificación y regresión.

Se agrega un número aleatorio (entre 0 y 1) a cada fila (en una columna llamada "rand") que puede utilizarse para seleccionar subconjuntos de validación cruzada durante el entrenamiento.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
val samplingFraction = 0.25;
val trainingFraction = 0.75;
val testingFraction = (1-trainingFraction);
val seed = 1234;
val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
val sampledDFcount = encodedFinalSampledTmp.count().toInt

val generateRandomDouble = udf(() => {
    scala.util.Random.nextDouble
})

# ADD A RANDOM NUMBER FOR CROSS-VALIDATION
val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

# SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
# INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
val trainData = splits(0)
val testData = splits(1)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Salida:**

Tiempo de ejecución de la celda: 2 segundos.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Especificación de variables de entrenamiento y características, y creación de tramas de datos o RDD de punto con etiqueta de entrada para entrenamientos y pruebas mediante indexación o codificación "one-hot"
Esta sección contiene código que muestra cómo indexar datos de texto categóricos como un tipo de datos de punto con etiqueta, y codificarlos para poder usarlos para entrenar y probar la regresión logística de MLlib y otros modelos de clasificación. Los objetos de punto con etiqueta son conjuntos de datos distribuidos resistentes (RDD) con el formato de datos de entrada necesario para la mayoría de los algoritmos de aprendizaje automático de MLlib. Un [punto con etiqueta](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) es un vector local, denso o disperso, asociado con una etiqueta o respuesta.

En este código, puede especificar la variable de destino (dependiente) y las características que se va a usar para entrenar modelos. Después, cree tramas de datos o RDD de punto con etiqueta de entrada para entrenamientos y pruebas mediante indexación o codificación "one-hot".

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

# CREATE INDEXED LABELED POINT RDD OBJECTS
val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

# CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
val indexedTESTbinaryDF = indexedTESTbinary.toDF()
val indexedTRAINregDF = indexedTRAINreg.toDF()
val indexedTESTregDF = indexedTESTreg.toDF()

# CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
val OneHotTRAIN = assemblerOneHot.transform(trainData)
val OneHotTEST = assemblerOneHot.transform(testData)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Salida:**

Tiempo de ejecución de la celda: 4 segundos.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Categorización y vectorización automáticas de características y destinos con el objetivo de utilizarlas como entradas para los modelos de aprendizaje automático
Utilice SparkML para categorizar correctamente las características y el destino para las funciones de modelado de árbol. El código realiza dos tareas:

* Crea un destino binario para la clasificación mediante la asignación de un valor de 0 o 1 a cada punto de datos entre 0 y 1 con un valor de umbral de 0,5.
* Categoriza automáticamente las características. Si el número de valores numéricos distintos de cualquiera de las características es inferior a 32, se categoriza la característica.

Este es el código para estas dos tareas.

```scala
# CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
# CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINregDF)
val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Clasificación binaria: predicción de si se debe dar propina
En esta sección, crearemos tres tipos de modelos de clasificación binaria para predecir si se debe pagar propina:

* Un **modelo de regresión logística** con la función `LogisticRegression()` del aprendizaje automático de Spark
* Un **modelo de clasificación de bosque aleatorio** con la función `RandomForestClassifier()` del aprendizaje automático de Spark
* Un **modelo de clasificación de árboles impulsados por gradientes** con la función `GradientBoostedTrees()` de MLlib

### <a name="create-a-logistic-regression-model"></a>Crear un modelo de regresión logística
Ahora, cree un modelo de regresión logística utilizando la función `LogisticRegression()` del aprendizaje automático de Spark. El código de creación del modelo se genera en varios pasos:

1. **Entrenamiento de datos del modelo** con un conjunto de parámetros.
2. **Evaluación del modelo** en un conjunto de datos de prueba con métricas.
3. **Guardado del modelo** en Almacenamiento de blobs para utilizarse en el futuro.
4. **Puntuación del modelo** con los datos de prueba.
5. **Trazado de los resultados** con curvas de característica operativa del receptor (ROC).

Este es el código para estos procedimientos:

```scala
# CREATE A LOGISTIC REGRESSION MODEL
val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
val lrModel = lr.fit(OneHotTRAIN)

# PREDICT ON THE TEST DATA SET
val predictions = lrModel.transform(OneHotTEST)

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)

# SAVE THE MODEL
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LogisticRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);
```

Cargue, puntúe y guarde los resultados.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON THE TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
predictions.registerTempTable("testResults")

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC RESULTS
println("ROC on test data = " + ROC)
```

**Salida:**

ROC de los datos de prueba = 0,9827381497557599

Use Python en las tramas de datos locales de Pandas para trazar la curva de ROC.

```scala
# QUERY THE RESULTS
%%sql -q -o sqlResults
SELECT tipped, probability from testResults


# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
predictions_pddf = sqlResults[["tipped","probFloat"]]

# PREDICT THE ROC CURVE
# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
prob = predictions_pddf["probFloat"]
fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT THE ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**Salida:**

![Curva de ROC sobre si se dará propina o no](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Creación de un modelo de clasificación de bosque aleatorio
Ahora, cree un modelo de clasificación de bosque aleatorio mediante la función `RandomForestClassifier()` del aprendizaje automático de Spark y evalúe el modelo con los datos de prueba.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE THE RANDOM FOREST CLASSIFIER MODEL
val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

# FIT THE MODEL
val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

# EVALUATE THE MODEL
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(predictions)
println("F1 score on test data: " + Test_f1Score);

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)
```

**Salida:**

ROC de los datos de prueba = 0,9847103571552683

### <a name="create-a-gbt-classification-model"></a>Creación de un modelo de clasificación GBT
Ahora, cree un modelo de clasificación GBT mediante la función `GradientBoostedTrees()` de MLlib y evalúe el modelo con los datos de prueba.

```scala
# TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE GBT CLASSIFICATION MODEL
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 20
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

# TRAIN THE MODEL
val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

# SAVE THE MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "GBT_Classification__"
val filename = modelDir.concat(modelName).concat(datestamp)
gbtModel.save(sc, filename);

# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
val labelAndPreds = indexedTESTbinary.map { point =>
  val prediction = gbtModel.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
println("Test Error = " + testErr)

# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
val metrics = new MulticlassMetrics(labelAndPreds)
println(s"Precision: ${metrics.precision}")
println(s"Recall: ${metrics.recall}")
println(s"F1 Score: ${metrics.fMeasure}")

val metrics = new BinaryClassificationMetrics(labelAndPreds)
println(s"Area under PR curve: ${metrics.areaUnderPR}")
println(s"Area under ROC curve: ${metrics.areaUnderROC}")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC METRIC
println(s"Area under ROC curve: ${metrics.areaUnderROC}")
```

**Salida:**

Área bajo la curva de ROC = 0,9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Modelo de regresión: predicción del importe de la propina
En esta sección, se crean dos tipos de modelos de regresión para predecir el importe de la propina:

* Un **modelo de regresión lineal regularizada** con la función `LinearRegression()` del aprendizaje automático de Spark. Debe guardar el modelo y evaluarlo con datos de prueba.
* Un **modelo de regresión de árboles impulsados por gradientes** con la función `GBTRegressor()` del aprendizaje automático de Spark.

### <a name="create-a-regularized-linear-regression-model"></a>Creación de un modelo d regresión lineal regularizada

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

# FIT THE MODEL BY USING DATA FRAMES
val lrModel = lr.fit(OneHotTRAIN)
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")

# SAVE THE MODEL IN AZURE BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LinearRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);

# PRINT THE COEFFICIENTS
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = lrModel.transform(OneHotTEST)

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```


**Salida:**

Tiempo de ejecución de la celda: 13 segundos.

```scala
# LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
predictions.registerTempTable("testResults")

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("R-sqr on test data = " + r2)
```

**Salida:**

R-sqr de los datos de prueba = 0,5960320470835743

A continuación, consulta los resultados de pruebas como una trama de datos y usa AutoVizWidget y matplotlib para visualizarlos.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

Este código crea una trama de datos local a partir de los resultados de la consulta y traza los datos. La instrucción mágica `%%local` crea una trama de datos local, `sqlResults`, que puede usarse para trazar la información con matplotlib.

> [!NOTE]
> Esta instrucción mágica de Spark se emplea varias veces en este artículo. Si la cantidad de datos es grande, realice un muestreo para crear una trama de datos que pueda caber en la memoria local.
> 
> 

Creación de trazados con matplotlib de Python.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
sqlResults
%matplotlib inline
import numpy as np

# PLOT THE RESULTS
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 8])
plt.show(ax)
```

**Salida:**

![Importe de la propina: real frente a predicción](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Creación de un modelo de regresión GBT
Cree un modelo de clasificación de bosque aleatorio mediante la función `GBTRegressor()` del aprendizaje automático de Spark y evalúe el modelo con los datos de prueba.

Los [árboles de potenciación del gradiente](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT) son conjuntos de árboles de decisión. Los GBT entrenan árboles de decisión de forma iterativa para minimizar una función de pérdida y se pueden usar para la clasificación y regresión. Permiten controlar características categóricas, no requieren ajustar la escala de las características y pueden capturar errores de alineación e interacciones de las características. También se pueden usar en una configuración de clasificación multiclase.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# TRAIN A GBT REGRESSION MODEL
val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

# MAKE PREDICTIONS
val predictions = gbtModel.transform(indexedTESTwithCatFeat)

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(predictions)


# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("Test R-sqr is: " + Test_R2);
```

**Salida:**

R-sqr de prueba = 0,7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Utilidades avanzadas de modelado para la optimización
En esta sección, utilizará herramientas de aprendizaje automático que los desarrolladores usan con frecuencia para la optimización de modelos. En concreto, pueden optimizarse los modelos de aprendizaje automático de tres maneras distintas mediante barrido de parámetros y validación cruzada:

* Dividir los datos en conjuntos de entrenamiento y validación, y optimizar el modelo mediante barrido de hiperparámetros en un conjunto de entrenamiento y mediante evaluación en un conjunto de validación (regresión lineal)
* Optimizar el modelo mediante validación cruzada y barrido de hiperparámetros con la función CrossValidator de SparkML (clasificación binaria)
* Optimizar modelo usando código personalizado de validación cruzada y barrido de parámetros para usar cualquier función de aprendizaje automático y conjunto de parámetros (regresión lineal)

**validación cruzada** es una técnica que evalúa la calidad de la generalización que realizará un modelo entrenado con un conjunto conocido de datos para predecir las características de conjuntos de datos con los que no se haya entrenado. La idea general tras esta técnica es que se entrena un modelo con un conjunto conocido de datos y después se prueba la precisión de sus predicciones con un conjunto de datos independiente. En este caso, se usa una implementación habitual en la que se divide un conjunto de datos en *k* iteraciones y después se entrena el modelo como round-robin en todas las iteraciones menos una.

**optimización de los hiperparámetros** es el problema de elegir un conjunto de hiperparámetros para un algoritmo de aprendizaje, normalmente con el fin de optimizar una medida del rendimiento del algoritmo con un conjunto de datos independiente. Un hiperparámetro es un valor que debe especificar fuera del procedimiento de entrenamiento del modelo. Las suposiciones que se hagan sobre estos hiperparámetros pueden afectar a la flexibilidad y la precisión de los modelos. Los árboles de decisión tienen hiperparámetros, como la profundidad que desee y el número de hojas del árbol. Debe establecer un término de penalización para las clasificaciones incorrectas en las máquinas de vectores de soporte (SVM).

Una forma habitual de realizar la optimización de hiperparámetros es una búsqueda de cuadrícula, también denominada **barrido de parámetros**. En una búsqueda de cuadrícula, se lleva a cabo una búsqueda exhaustiva en los valores de un subconjunto concreto del espacio de hiperparámetros para un algoritmo de aprendizaje. La validación cruzada puede proporcionar una métrica de rendimiento para ordenar los resultados óptimos generados por el algoritmo de búsqueda de cuadrícula. Si utiliza el barrido de hiperparámetros de validación cruzada, puede limitar problemas, como el sobreajuste de un modelo de datos de entrenamiento. De este modo, el modelo sigue aplicándose a un conjunto general de datos del que se extrajeron los datos de entrenamiento.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimización del modelo de regresión lineal mediante barrido de hiperparámetros
Ahora, divida los datos en conjuntos de entrenamiento y validación, optimice el modelo mediante barrido de hiperparámetros en un conjunto de entrenamiento y evalúe en un conjunto de validación (regresión lineal).

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# RENAME `tip_amount` AS A LABEL
val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
OneHotTRAINLabeled.cache()
OneHotTESTLabeled.cache()

# DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

# DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
val trainPct = 0.75
val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = trainValidationSplit.fit(OneHotTRAINLabeled)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

println("Test R-sqr is: " + Test_R2);
```

**Salida:**

R-sqr de prueba = 0,6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimización del modelo de clasificación binaria mediante barrido de hiperparámetros y validación cruzada
En esta sección se muestra cómo optimizar el modelo de clasificación binaria mediante barrido de hiperparámetros y validación cruzada. Esto utiliza la función `CrossValidator` del aprendizaje automático de Spark.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
indexedTRAINwithCatFeatBinTargetRF.cache()
indexedTESTwithCatFeatBinTargetRF.cache()

# DEFINE THE ESTIMATOR FUNCTION
val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

# SPECIFY THE NUMBER OF FOLDS
val numFolds = 3

# DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

# COMPUTE THE TEST F1 SCORE
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Salida:**

Tiempo de ejecución de la celda: 33 segundos.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimización del modelo de regresión lineal mediante código personalizado de validación cruzada y barrido de parámetros
A continuación, optimice el modelo usando código personalizado e identifique los mejores parámetros del modelo mediante el criterio de mayor precisión. Después, cree el modelo final, evalúelo con los datos de prueba y guárdelo en Almacenamiento de blobs. Por último, cargue el modelo, puntúe los datos de prueba y evalúe su precisión.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

val nFolds = 3
val numModels = paramGrid.size
val numParamsinGrid = 2

# SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

var maxDepth = -1
var numTrees = -1
var param = ""
var paramval = -1
var validateLB = -1.0
var validateUB = -1.0
val h = 1.0 / nFolds;
val RMSE  = Array.fill(numModels)(0.0)

# CREATE K-FOLDS
val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
for (i <- 0 to (nFolds-1)) {
    validateLB = i * h
    validateUB = (i + 1) * h
    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    validationLabPt.cache()
    trainCVLabPt.cache()

    for (nParamSets <- 0 to (numModels-1)) {
        for (nParams <- 0 to (numParamsinGrid-1)) {
            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
            if (param == "maxDepth") {maxDepth = paramval}
            if (param == "numTrees") {numTrees = paramval}
        }
        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=numTrees, maxDepth=maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
        val labelAndPreds = validationLabPt.map { point =>
                                                 val prediction = rfModel.predict(point.features)
                                                  ( prediction, point.label )
                                                }
        val validMetrics = new RegressionMetrics(labelAndPreds)
        val rmse = validMetrics.rootMeanSquaredError
        RMSE(nParamSets) += rmse
    }
    validationLabPt.unpersist();
    trainCVLabPt.unpersist();
}
val minRMSEindex = RMSE.indexOf(RMSE.min)

# GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
var best_maxDepth = -1
var best_numTrees = -1
for (nParams <- 0 to (numParamsinGrid-1)) {
    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
    if (param == "maxDepth") {best_maxDepth = paramval}
    if (param == "numTrees") {best_numTrees = paramval}
}

# CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)

# SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "BestCV_RF_Regression__"
val filename = modelDir.concat(modelName).concat(datestamp)
best_rfModel.save(sc, filename);

# PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = best_rfModel.predict(point.features)
                                        ( prediction, point.label )
                                       }

val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");


# LOAD THE MODEL
val savedRFModel = RandomForestModel.load(sc, filename)

val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = savedRFModel.predict(point.features)
                                        ( prediction, point.label )
                                       }
# TEST THE MODEL
val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2
```

**Salida:**

Tiempo de ejecución de la celda: 61 segundos.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Uso automático en Scala de los modelos de aprendizaje automático creados en Spark
Para ver una introducción de los temas que lo guiarán por las tareas que componen el proceso de ciencia de datos en Azure, consulte [Proceso de ciencia de los datos en equipos (TDSP)](./index.yml).

[Tutoriales del proceso de ciencia de datos en equipos](walkthroughs.md) describe otros tutoriales de extremo a extremo que muestran los pasos en el proceso de ciencia de datos de equipo en escenarios específicos. En los tutoriales también se muestra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una aplicación inteligente.

[Puntuación de modelos de aprendizaje automático creados con Spark](spark-model-consumption.md) muestra cómo utilizar código de Scala para cargar y puntuar automáticamente nuevos conjuntos de datos con modelos de aprendizaje automático creados en Spark y guardarlos en Almacenamiento de blobs de Azure. Se pueden seguir las instrucciones de ese artículo y, simplemente, reemplazar el código de Python por el de Scala que se indica aquí para permitir el uso automatizado.