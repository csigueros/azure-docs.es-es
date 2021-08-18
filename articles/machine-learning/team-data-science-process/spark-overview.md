---
title: 'Ciencia de datos con Spark en Azure HDInsight: proceso de ciencia de datos en equipos'
description: El kit de herramientas MLlib de Spark ofrece importantes funciones de modelado de aprendizaje automático para el entorno distribuido de HDInsight.
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
ms.openlocfilehash: f0d7bb2ef0a214b5e4b551afe7f0189847ac44ca
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112285209"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Información general sobre la ciencia de los datos con Spark en HDInsight de Azure

En este conjunto de temas se muestra cómo utilizar Spark en HDInsight para completar tareas comunes de ciencia de datos como la ingesta de datos, el diseño de características, el modelado y la evaluación de modelos. Los datos que se utilizan son un ejemplo del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York en 2013 Los modelos creados incluyen regresión logística y lineal, bosques aleatorios y árboles impulsados por gradiente: En los temas también se muestra cómo almacenar estos modelos en el Azure Blob Storage (WASB) y cómo puntuar y evaluar su rendimiento predictivo. En los temas más avanzados se describe cómo se pueden entrenar los modelos mediante validación cruzada y barrido de hiperparámetros. En este tema introductorio también se hace referencia al tema en el que se describe cómo configurar un clúster de Spark que se necesita para completar los pasos de los tutoriales proporcionados.

## <a name="spark-and-mllib"></a>Spark y MLlib
[Spark](https://spark.apache.org/) es una plataforma de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las capacidades de cálculo distribuido en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. [MLlib](https://spark.apache.org/mllib/) es la biblioteca de aprendizaje automático escalable de Spark que ofrece funcionalidades de modelado algorítmico en este entorno distribuido.

## <a name="hdinsight-spark"></a>HDInsight Spark
[Spark en HDInsight](../../hdinsight/spark/apache-spark-overview.md) es la oferta de Spark de código abierto hospedada por Azure. También incluye compatibilidad con **cuadernos de PySpark para Jupyter** en el clúster de Spark, que pueden ejecutar consultas interactivas de Spark SQL para transformar, filtrar y visualizar los datos almacenados en blobs de Azure (WASB). PySpark es la API de Python para Spark. Los fragmentos de código que proporcionan las soluciones y muestran los trazados relevantes para visualizar los datos aquí se ejecutan en cuadernos de Jupyter instalados en los clústeres de Spark. En estos temas, los pasos de modelado también contienen código que muestra cómo entrenar, evaluar, guardar y usar cada tipo de modelo.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Configuración: Clústeres de Spark y cuadernos de Jupyter
Los pasos de instalación y el código proporcionado en este tutorial son para HDInsight Spark 1.6. Sin embargo, Jupyter Notebooks se proporciona para clústeres de HDInsight Spark 1.6 y Spark 2.0. Se proporciona una descripción de los cuadernos y de los vínculos a estos en el archivo [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) del repositorio de GitHub que los contiene. No obstante, este código y los cuadernos vinculados son genéricos y deberían funcionar en cualquier clúster de Spark. Los pasos de configuración y administración del clúster pueden ser ligeramente diferentes de los que se muestran aquí si no está usando Spark en HDInsight. Para mayor comodidad, estos son los vínculos a los cuadernos de Jupyter Notebook para Spark 1.6 (que se van a ejecutar en el kernel pySpark del servidor de Jupyter Notebook) y Spark 2.0 (que se van a ejecutar en el kernel pySpark3 del servidor de Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Cuadernos de Spark 1.6
Estos cuadernos están indicados para ejecutarse en el kernel pySpark del servidor de cuadernos de Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): ofrece información sobre cómo realizar el modelado, la puntuación y la exploración de datos con diversos algoritmos.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): incluye temas del cuaderno n.º 1 y el desarrollo de modelos mediante el ajuste de hiperparámetros y la validación cruzada.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): muestra cómo poner en funcionamiento un modelo guardado con Python en clústeres de HDInsight.

### <a name="spark-20-notebooks"></a>Cuadernos de Spark 2.0
Estos cuadernos están indicados para ejecutarse en el kernel pySpark3 del servidor de cuadernos de Jupyter.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): En este archivo se ofrece información sobre cómo realizar el modelado, la puntuación y la exploración de datos en clústeres de Spark 2.0 con el conjunto de datos de carreras y tarifas de taxi de Nueva York que se describe [aquí](#the-nyc-2013-taxi-data). Este cuaderno puede ser un buen punto de partida para explorar rápidamente el código proporcionado para Spark 2.0. Para obtener un análisis más pormenorizado de los datos del cuaderno sobre taxis en Nueva York, vea el siguiente cuaderno de esta lista. En las notas que encontrará después de esta lista se establece una comparación de estos cuadernos.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): En este archivo se muestra cómo realizar la limpieza y transformación de datos (operaciones de dataframe y Spark SQL), la exploración, el modelado y la puntuación con el conjunto de datos de carreras y tarifas de taxi de Nueva York descrito [aquí](#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): En este archivo se muestra cómo realizar la limpieza y transformación de datos (operaciones de dataframe y Spark SQL), la exploración, el modelado y la puntuación con el conocido conjunto de datos de salidas puntuales de líneas aéreas de 2011 y 2012. Se ha integrado el conjunto de datos de las líneas aéreas con los datos climatológicos del aeropuerto (por ejemplo, velocidad del viento, temperatura, altitud, etc.), antes de realizar el modelado, con el fin de poder incluir estas características climatológicas en el modelo.

<!-- -->

> [!NOTE]
> El conjunto de datos de las líneas aéreas se agregó a los cuadernos de Spark 2.0 para ilustrar mejor el uso de algoritmos de clasificación. Consulte los siguientes vínculos para obtener información sobre el conjunto de datos de salidas puntuales de las líneas aéreas y sobre el de datos climatológicos:
> 
> - Datos de salida en hora de líneas aéreas: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Datos meteorológicos de aeropuertos: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Los cuadernos de Spark 2.0 sobre los conjuntos de datos de los taxis de Nueva York y de retrasos en los vuelos pueden tardar 10 minutos o más en ejecutarse (dependiendo del tamaño del clúster de HDI). El primer cuaderno de la lista anterior muestra muchos aspectos de la exploración de datos, la visualización y el entrenamiento del modelo de ML en un cuaderno que tarda menos tiempo en ejecutarse con conjuntos de datos de NYC de bajo muestreo, al que se han unido previamente los archivos de taxi y tarifas: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Este cuaderno tarda menos tiempo en finalizar (2-3 minutos) y puede ser un buen punto de partida para explorar rápidamente el código proporcionado para Spark 2.0.

<!-- -->

Para obtener instrucciones sobre la operacionalización de un modelo de Spark 2.0 y el consumo de modelo para puntuación, consulte el [documento de Spark 1.6 sobre consumo](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) para obtener un ejemplo que muestre los pasos necesarios. Para utilizar este ejemplo en Spark 2.0, reemplace el archivo de código Python por [este archivo](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Prerrequisitos

Los procedimientos siguientes están relacionados con Spark 1.6. En la versión Spark 2.0, use los cuadernos descritos anteriormente, con sus correspondientes vínculos.

1. Debe tener una suscripción de Azure. Si aún no tiene una, consulte [Get Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)(Obtener una evaluación gratuita de Azure).

2. Para completar este tutorial se necesita un clúster de Spark 1.6. Para crear uno, consulte las instrucciones proporcionadas en [Introducción: creación de clústeres de Apache Spark en HDInsight para Linux y ejecución de consultas interactivas mediante Spark SQL (versión preliminar)](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). En el menú **Seleccionar tipo de clúster** se especifica el tipo de clúster y la versión.

![Configuración del inicio de sesión del clúster](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Si quiere leer un tema que muestre cómo utilizar Scala en lugar de Python para completar las tareas de un proceso de ciencia de datos de un extremo a otro, consulte [Ciencia de datos mediante Scala con Spark de Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../hdinsight/includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Datos de taxis de Nueva York de 2013
Los datos de carreras de taxi de Nueva York son aproximadamente 20 GB de archivos comprimidos de valores separados por comas (CSV) (~48 GB sin comprimir), que incluyen más de 173 millones de carreras individuales y las tarifas pagadas por cada carrera. El registro de cada carrera incluye la hora y la ubicación de recogida y llegada, el número de licencia del taxista anonimizado y el número de placa (identificador único del taxi). Los datos cubren todos los viajes del año 2013 y se proporcionan en los dos conjuntos de datos siguientes para cada mes:

1. Los archivos CSV 'trip_data' contienen información detallada de las carreras, como el número de pasajeros, los puntos de recogida y destino, la duración de las carreras y la longitud del recorrido. Estos son algunos registros de ejemplo:

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Los archivos CSV 'trip_fare' contienen información detallada de la tarifa que se paga en cada carrera, como el tipo de pago, el importe de la tarifa, los suplementos e impuestos, las propinas y peajes, y el importe total pagado. Estos son algunos registros de ejemplo:

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Hemos tomado una muestra del 0,1 % de estos archivos y los hemos combinado en archivos CSV trip\_data and trip\_fare en un único conjunto de datos que se usará como conjunto de datos de entrada en este tutorial. La clave única para unir trip\_data and trip\_fare se compone de los campos: medallion, hack\_licence and pickup\_datetime. Cada registro del conjunto de datos contiene los siguientes atributos que representan una carrera de taxi de Nueva York:

| Campo | Breve descripción |
| --- | --- |
| medallion |Licencias de taxi anónimas (identificador único del taxi) |
| hack_license |Número de licencia anónima de taxi londinense |
| vendor_id |Identificador del proveedor del taxi |
| rate_code |Categoría de tarifa de taxi de Nueva York |
| store_and_fwd_flag |Indicador de “guardar y reenviar” |
| pickup_datetime |Fecha y hora de subida |
| dropoff_datetime |Fecha y hora de bajada |
| pickup_hour |Hora de recogida |
| pickup_week |Semana del año de subida |
| weekday |Día de la semana (intervalo de 1 a 7) |
| passenger_count |Número de pasajeros en una carrera de taxi |
| trip_time_in_secs |Tiempo de la carrera, en segundos |
| trip_distance |Distancia recorrida en la carrera, en millas |
| pickup_longitude |Longitud del punto de subida |
| pickup_latitude |Latitud del punto de subida |
| dropoff_longitude |Longitud del punto de bajada |
| dropoff_latitude |Latitud del punto de bajada |
| direct_distance |Distancia directa entre las ubicaciones de subida y bajada |
| payment_type |Tipo de pago (efectivo, tarjeta de crédito, etc.) |
| fare_amount |Importe de la tarifa en |
| surcharge |Suplemento |
| mta_tax |Impuesto de transporte en metro MTA |
| tip_amount |Importe de la propina |
| tolls_amount |Importe de los peajes |
| total_amount |Importe total |
| tipped |Con propina (0 o 1 para No o Sí) |
| tip_class |Clase de propina (0: 0 $, 1: 0-5 $, 2: 6-10 $, 3: 11-20 $, 4: > 20 $) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Ejecución del código desde un Notebook de Jupyter en el clúster de Spark
Puede iniciar Jupyter Notebook desde el portal de Azure. Busque el clúster de Spark en el panel y haga clic en él para entrar en la página de administración del clúster. Para abrir el cuaderno asociado al clúster Spark, haga clic en **Paneles de clúster** -> **Jupyter Notebook**.

![Paneles de clúster](./media/spark-overview/spark-jupyter-on-portal.png)

También puede ir a ***`https://CLUSTERNAME.azurehdinsight.net/jupyter`*** para tener acceso a las instancias de Jupyter Notebook. Reemplace la parte CLUSTERNAME de esta dirección URL por el nombre de su propio clúster. Necesitará la contraseña de su cuenta de administrador para acceder a los Notebooks.

![Examinar cuadernos de Jupyter Notebook](./media/spark-overview/spark-jupyter-notebook.png)

Seleccione PySpark para ver un directorio con algunos ejemplos de cuadernos preempaquetados que utilizan la API PySpark. Los cuadernos que contienen los ejemplos de código de este conjunto de temas de Spark están disponibles en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

Puede cargar los cuadernos directamente desde [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) en el servidor de Jupyter Notebook, en su clúster de Spark. En la página principal de su instancia de Jupyter, haga clic en el botón **Cargar** de la parte derecha de la pantalla. Se abre un explorador de archivos. Pegue aquí la dirección URL de GitHub (contenido sin procesar) del cuaderno y haga clic en **Abrir**.

Verá el nombre de archivo en la lista de archivos de Jupyter de nuevo con un botón **Cargar** . Haga clic en este botón **Cargar** . Ahora ya ha importado el cuaderno. Repita estos pasos para cargar los otros cuadernos de este tutorial.

> [!TIP]
> Puede hacer clic con el botón derecho en los vínculos del explorador y seleccionar **Copiar vínculo** para obtener la dirección URL del contenido sin procesar de GitHub. Puede pegar esta dirección URL en el cuadro de diálogo del explorador de carga de archivos de Jupyter.
> 
> 

Ahora puede:

* Ver el código haciendo clic en el cuaderno.
* Ejecutar cada celda presionando **MAYÚS+ENTRAR**.
* Ejecutar todo el cuaderno haciendo clic en **Celda** -> **Run**.
* Usar la visualización automática de consultas.

> [!TIP]
> El kernel de PySpark visualiza automáticamente la salida de las consultas de SQL (HiveQL). Tiene la opción de seleccionar entre diferentes tipos de visualizaciones (tabla, circular, línea, área o barra) mediante los botones del menú **Tipo** del cuaderno:
>
>

![Curva ROC de regresión logística para el enfoque genérico](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>¿Qué sigue?
Ahora que ya ha configurado un clúster de HDInsight Spark y ha cargado los cuadernos de Jupyter Notebook, está listo para trabajar con los temas correspondientes a estos tres cuadernos de PySpark, que muestran cómo explorar los datos y crear y utilizar los modelos. El cuaderno de exploración y modelado de datos avanzado muestra cómo incluir la validación cruzada, el barrido de los hiperparámetros y la evaluación de modelos.

**Exploración y modelado de datos con Spark:** Explore el conjunto de datos y cree, puntúe y evalúe modelos de aprendizaje automático con la ayuda del tema [Creación de modelos de clasificación y regresión para datos con el kit de herramientas de MLib de Spark](spark-data-exploration-modeling.md).

**Consumo de modelos:** Para saber cómo puntuar los modelos de clasificación y regresión creados en este tema, consulte [Puntuación y evaluación de modelos de aprendizaje automático creados con Spark](spark-model-consumption.md).

**Validación cruzada y barrido de hiperparámetros**: Consulte [Exploración y modelado avanzados de datos con Spark](spark-advanced-data-exploration-modeling.md) para saber cómo se pueden entrenar modelos con el barrido de hiperparámetros y la validación cruzada.