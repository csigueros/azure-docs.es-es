---
title: Introducción a Cognitive Services para macrodatos
description: Configure la canalización de MMLSpark con Cognitive Services en Azure Databricks y ejecute un ejemplo.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 9c9780a51954978935dad164c9d8c5b5a1535826
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447338"
---
# <a name="getting-started"></a>Introducción

El primer paso para crear una canalización para los datos es preparar el entorno. Una vez que el entorno esté listo, puede ejecutar un ejemplo de forma rápida y sencilla.

En este artículo, se realizarán estos pasos para ayudarlo a comenzar:

1. [Cree un recurso de Cognitive Services](#create-a-cognitive-services-resource)
1. [Creación de un clúster de Apache Spark](#create-an-apache-spark-cluster)
1. [Prueba de un ejemplo](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Creación de un recurso de Cognitive Services

Para usar Cognitive Services para macrodatos, primero se debe crear una instancia de Cognitive Services para el flujo de trabajo. Hay dos tipos principales de servicios cognitivos: los servicios en la nube hospedado en Azure y servicios en contenedores administrados por los usuarios. Se recomienda comenzar con los servicios cognitivos basados en la nube, que son los más sencillos.

### <a name="cloud-services"></a>Servicios en la nube

Los servicios cognitivos basados en la nube son algoritmos inteligentes que se hospedan en Azure. Estos servicios se pueden usar sin necesidad de entrenamiento, solo hace falta una conexión a Internet. Puede [crear un servicio cognitivo en Azure Portal](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) o con la [CLI de Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows).

### <a name="containerized-services-optional"></a>Servicios en contenedores (opcional)

Si la aplicación o la carga de trabajo emplean conjuntos de datos grandes, necesitan redes privadas o no pueden establecer contacto con la nube, es posible que no se pueda establecer comunicación con los servicios en la nube. En esta situación, los servicios cognitivos en contenedores tienen estas ventajas:

* **Conectividad baja**: puede implementar servicios cognitivos en contenedores en cualquier entorno informático, tanto en la nube como fuera de ella. Si la aplicación no puede establecer contacto con la nube, considere la posibilidad de implementar servicios cognitivos en contenedores en la aplicación.

* **Baja latencia**: dado que los servicios en contenedores no necesitan la comunicación de ida y vuelta hacia y desde la nube, las respuestas se devuelven con latencias mucho más bajas.

* **Privacidad y seguridad de los datos**: puede implementar servicios en contenedores en redes privadas, de modo que los datos confidenciales no salgan de la red.

* **Alta escalabilidad**: los servicios en contenedores no tienen "límites de frecuencia" y se ejecutan en equipos administrados por el usuario. Por lo tanto, puede escalar los servicios cognitivos sin fin para administrar cargas de trabajo mucho más grandes.

Siga [esta guía](../cognitive-services-container-support.md?tabs=luis) para crear un servicio cognitivo en contenedores.

## <a name="create-an-apache-spark-cluster"></a>Creación de un clúster de Apache Spark

[Apache Spark&trade;](http://spark.apache.org/) es un marco de trabajo informático distribuido diseñado para el procesamiento de macrodatos. Los usuarios pueden trabajar con Apache Spark en Azure con servicios como Azure Databricks, Azure Synapse Analytics, HDInsight y Azure Kubernetes Services. Para usar Cognitive Services para macrodatos, primero se debe crear un clúster. Si ya tiene un clúster de Spark, pruebe un ejemplo.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks es una plataforma de análisis basada en Apache Spark con una configuración de un solo clic, flujos de trabajo optimizados y un área de trabajo interactiva. Se usa a menudo para la colaboración entre científicos de datos, ingenieros y analistas de negocios. Para usar Cognitive Services para macrodatos en Azure Databricks, siga estos pasos:

1. [Creación de un área de trabajo de Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Creación de un clúster de Spark en Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Instalación de Cognitive Services para macrodatos
    * Creación de una biblioteca en el área de trabajo de Databricks  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Escriba las siguientes coordenadas de Maven:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3`Repositorio:`https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Instalación de la biblioteca en un clúster  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure Synapse Analytics (opcional)

Opcionalmente, puede usar Synapse Analytics para crear un clúster de Spark. Azure Synapse Analytics reúne el almacenamiento de datos empresarial y el análisis de macrodatos. Este servicio ofrece la libertad de consultar los datos como prefiera, ya sea a petición sin servidor o con recursos aprovisionados a escala. Para empezar a usar Azure Synapse Analytics, siga estos pasos:

1. [Cree un área de trabajo de Synapse (versión preliminar)](../../synapse-analytics/quickstart-create-workspace.md).
1. [Cree un grupo de Apache Spark sin servidor (versión preliminar) mediante Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

En Azure Synapse Analytics, Cognitive Services para macrodatos está instalado de forma predeterminada.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Si usa Cognitive Services en contenedores, una opción habitual para implementar Spark junto con contenedores es Azure Kubernetes Service.

Para empezar a usar Azure Kubernetes Service, siga estos pasos:

1. [Implementación de un clúster de Azure Kubernetes Service (AKS) mediante Azure Portal](../../aks/kubernetes-walkthrough-portal.md)
1. [Instalación del gráfico de Helm de Apache Spark 2.4.0](https://hub.helm.sh/charts/microsoft/spark)
1. [Instalación de un contenedor de servicios cognitivos mediante Helm](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Prueba de un ejemplo

Después de configurar el entorno y el clúster de Spark, puede ejecutar un ejemplo corto. En esta sección se muestra cómo usar Cognitive Services para macrodatos en Azure Databricks.

Primero, puede crear un cuaderno en Azure Databricks. En el caso de otros proveedores de clústeres de Spark, use sus cuadernos o Spark Submit.

1. Cree un cuaderno de Databricks; para ello, elija **New notebook** (Nuevo cuaderno) en el menú de **Azure Databricks**.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. En el cuadro de diálogo **Create Notebook** (Crear cuaderno), seleccione **Python** como lenguaje y seleccione el clúster de Spark que creó anteriormente.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Seleccione **Crear**.

1. Pegue este fragmento de código en el cuaderno nuevo.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Obtenga la clave de suscripción del menú **Keys and Endpoint** (Claves y puntos de conexión) del panel Text Analytics en Azure Portal.
1. Reemplace el marcador de posición de la clave de suscripción que aparece en el código del cuaderno de Databricks por su clave de suscripción.
1. Seleccione el símbolo de reproducción (triángulo) situado en la esquina superior derecha de la celda del cuaderno para ejecutar el ejemplo. También puede seleccionar **Run All** (Ejecutar todo) en la parte superior del cuaderno para ejecutar todas las celdas. Las respuestas se muestran debajo de la celda de una tabla.

### <a name="expected-results"></a>Resultados esperados

| text                                      |   opinión |
|:------------------------------------------|------------:|
| I am so happy today, its sunny!           |   0.978959  |
| I am frustrated by this rush hour traffic |   0.0237956 |
| The cognitive services on spark aint bad  |   0.888896  |

## <a name="next-steps"></a>Pasos siguientes

- [Pequeños ejemplos de Python](samples-python.md)
- [Pequeños ejemplos de Scala](samples-scala.md)
- [Receta: Mantenimiento predictivo](recipes/anomaly-detection.md)
- [Receta: Exploración de arte inteligente](recipes/art-explorer.md)
