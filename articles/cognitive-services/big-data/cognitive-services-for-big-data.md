---
title: Cognitive Services para macrodatos
description: Vea cómo aprovechar Azure Cognitive Services en grandes conjuntos de datos con Python, Java y Scala. Con Cognitive Services para macrodatos, puede integrar los modelos inteligentes de mejora continua directamente en cálculos de Apache Spark y SQL.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 10/28/2021
ms.author: marhamil
ms.openlocfilehash: 71b89cc6b54ff99864bdd0f15ebcee472a36cd70
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434491"
---
# <a name="azure-cognitive-services-for-big-data"></a>Azure Cognitive Services para macrodatos

![Azure Cognitive Services para macrodatos](media/cognitive-services-big-data-overview.svg)

Azure Cognitive Services para macrodatos permite a los usuarios canalizar terabytes de datos mediante Cognitive Services con [Apache Spark&trade;](/dotnet/spark/what-is-spark). Con Cognitive Services para macrodatos, es fácil crear aplicaciones inteligentes a gran escala con cualquier almacén de datos.

Con Cognitive Services para macrodatos, puede integrar los modelos inteligentes de mejora continua directamente en cálculos de Apache Spark y SQL. Estas herramientas liberan a los desarrolladores de las tareas de red de bajo nivel, de modo que puedan centrarse en la creación de aplicaciones distribuidas inteligentes.

## <a name="features-and-benefits"></a>Características y ventajas

Cognitive Services para macrodatos puede usar servicios de cualquier región del mundo, así como [Cognitive Services en contenedores](../cognitive-services-container-support.md). Los contenedores admiten implementaciones de conectividad baja o sin conectividad con respuestas de latencia muy baja. Cognitive Services en contenedores se puede ejecutar de forma local, bien directamente en los nodos de trabajo del clúster de Spark o en un orquestador externo, como Kubernetes.

## <a name="supported-services"></a>Servicios admitidos

[Cognitive Services](../index.yml), con acceso a través de las API y los SDK, ayuda a los desarrolladores a crear aplicaciones inteligentes sin tener conocimientos de inteligencia artificial o de ciencia de datos. Con Cognitive Services puede hacer que las aplicaciones vean, escuchen, hablen, comprendan y razonen. Para usar Cognitive Services, la aplicación debe enviar datos al servicio a través de la red. Una vez recibidos, el servicio envía a cambio una respuesta inteligente. Los siguientes servicios están disponibles para las cargas de trabajo de macrodatos:

### <a name="vision"></a>Visión

|Nombre de servicio|Descripción del servicio|
|:-----------|:------------------|
|[Computer Vision](../computer-vision/index.yml "Computer Vision")| El servicio Computer Vision proporciona acceso a algoritmos avanzados para procesar imágenes y devolver información. |
|[Face](../face/index.yml "Caras")| El servicio Face proporciona acceso a algoritmos faciales avanzados, lo que permite la detección y el reconocimiento de atributos faciales. |

### <a name="speech"></a>Voz

|Nombre de servicio|Descripción del servicio|
|:-----------|:------------------|
|[Servicio Voz](../speech-service/index.yml "Servicio de voz")|El servicio de voz proporciona acceso a características como reconocimiento de voz, síntesis de voz, traducción de voz y comprobación e identificación del hablante.|

### <a name="decision"></a>Decisión

|Nombre de servicio|Descripción del servicio|
|:-----------|:------------------|
|[Anomaly Detector](../anomaly-detector/index.yml "Anomaly Detector") | El servicio Anomaly Detector (versión preliminar) permite supervisar y detectar anomalías en los datos de serie temporal.|

### <a name="language"></a>Idioma

|Nombre de servicio|Descripción del servicio|
|:-----------|:------------------|
|[Servicio de lenguaje](../language-service/index.yml "Servicio de lenguaje")| El servicio Language ofrece procesamiento de lenguaje natural en texto sin formato para el análisis de opiniones, la extracción de frases clave y la detección del idioma.|

### <a name="search"></a>Search

|Nombre de servicio|Descripción del servicio|
|:-----------|:------------------|
|[Bing Image Search](/azure/cognitive-services/bing-image-search "Bing Image Search")|El servicio Bing Image Search devuelve una lista de imágenes cuya relevancia se ha determinado para la consulta del usuario.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Lenguajes de programación admitidos en Cognitive Services para macrodatos

Cognitive Services para macrodatos se basa en Apache Spark. Apache Spark es una biblioteca de computación distribuida que admite Java, Scala, Python, R y muchos otros lenguajes. Actualmente se admiten estos lenguajes.

### <a name="python"></a>Python

Se proporciona una API de PySpark en el espacio de nombres `mmlspark.cognitive` de [Microsoft ML para Apache Spark](https://aka.ms/spark). Para más información, consulte la [API para desarrolladores de Python](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Para ver cómo se usa, consulte los [ejemplos de Python](samples-python.md).

### <a name="scala-and-java"></a>Scala y Java

Se proporciona una API de Spark basada en Scala y Java en el espacio de nombres `com.microsoft.ml.spark.cognitive` de [Microsoft ML para Apache Spark](https://aka.ms/spark). Para más información, consulte la [API para desarrolladores de Scala](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). Para ver cómo se usa, consulte los [ejemplos de Scala](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Plataformas y conectores compatibles

Cognitive Services para macrodatos requiere Apache Spark. Hay varias plataformas Apache Spark que admiten Cognitive Services para macrodatos.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) es una plataforma de análisis basada en Apache Spark optimizada para la plataforma de servicios en la nube de Microsoft Azure. Proporciona una configuración de un solo clic, flujos de trabajo optimizados y un área de trabajo interactiva que permite la colaboración entre científicos de datos, ingenieros de datos y analistas de negocios.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) es el almacenamiento de datos empresarial que utiliza procesamiento paralelo masivo. Con Synapse Analytics, puede ejecutar rápidamente consultas complejas en petabytes de datos. Azure Synapse Analytics proporciona grupos de Spark administrados para ejecutar trabajos de Spark con una interfaz de Jupyter Notebook intuitiva.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Azure Kubernetes Service (AKS)](../../aks/index.yml) organiza los contenedores de Docker y las aplicaciones distribuidas a escalas masivas. AKS es una oferta de Kubernetes administrada que simplifica el uso de Kubernetes en Azure. Kubernetes puede permitir el control pormenorizado de la escala, la latencia y la red de Cognitive Services. Sin embargo, se recomienda usar Azure Databricks o Azure Synapse Analytics si no está familiarizado con Apache Spark.

### <a name="data-connectors"></a>Conectores de datos

Una vez que tenga un clúster de Spark, el paso siguiente es conectarse a los datos. Apache Spark cuenta con una amplia colección de conectores de base de datos. Estos conectores permiten que las aplicaciones trabajen con grandes conjuntos de datos, con independencia de dónde estén almacenados. Para más información sobre las bases de datos y los conectores admitidos, consulte la [lista de orígenes de datos admitidos para Azure Databricks](/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Conceptos

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/) es un motor de análisis unificado para el procesamiento de datos a gran escala. Su marco de procesamiento paralelo aumenta el rendimiento de las aplicaciones de macrodatos y análisis. Spark puede funcionar como un sistema de procesamiento por lotes y por secuencias, sin cambiar el código de aplicación principal.

La base de Spark es la trama de datos: una colección tabular de datos distribuidos entre los nodos de trabajo de Apache Spark. Una trama de datos de Spark es como una tabla de una base de datos relacional o una trama de datos en R o Python, pero con una escala ilimitada. Las tramas de datos se pueden construir a partir de muchos orígenes, por ejemplo, archivos de datos estructurados, tablas de Hive o bases de datos externas. Una vez que los datos están en una trama de datos de Spark, puede:

   - Realizar cálculos de tipo SQL, como tablas de combinación y filtro.
   - Aplicar funciones a grandes conjuntos de datos mediante el paralelismo de estilo de MapReduce.
   - Aplicar Machine Learning distribuido con Machine Learning de Microsoft para Apache Spark.
   - Usar Cognitive Services para macrodatos con el fin de enriquecer los datos con servicios inteligentes listos para su uso.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning para Apache Spark (MMLSpark)

[Microsoft Machine Learning para Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) es una biblioteca de aprendizaje automático (ML) distribuida y de código abierto que se basa en Apache Spark. En este paquete se incluye Cognitive Services para macrodatos. Además, MMLSpark contiene otras diversas herramientas de aprendizaje automático para Apache Spark, como LightGBM, Vowpal Wabbit, OpenCV, LIME, etc. Con MMLSpark, puede crear modelos analíticos y predictivos eficaces desde cualquier origen de datos de Spark.

### <a name="http-on-spark"></a>HTTP en Spark

Cognitive Services para macrodatos es un ejemplo de integración de servicios web inteligentes con macrodatos. Los servicios web impulsan muchas aplicaciones de todo el mundo, y la mayoría de los servicios se comunican a través del Protocolo de transferencia de hipertexto (HTTP). Para trabajar con servicios web *arbitrarios* a gran escala, se proporciona HTTP en Spark. Con HTTP en Spark, puede pasar terabytes de datos a través de cualquier servicio web. Esta tecnología se usa en segundo plano para impulsar Cognitive Services para macrodatos.

## <a name="developer-samples"></a>Ejemplos para desarrolladores

- [Receta: Mantenimiento predictivo](recipes/anomaly-detection.md)
- [Receta: Exploración de arte inteligente](recipes/art-explorer.md)

## <a name="blog-posts"></a>Publicaciones de blog

- [Más información sobre cómo funciona Cognitive Services en Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Aprendizaje profundo y Computer Vision en Spark para salvar a los leopardos blancos](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Podcast de Microsoft Research: MMLSpark: impulsar AI for Good con Mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [Notas del producto Academic: microservicios inteligentes a gran escala](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Seminarios web y vídeos

- [Azure Cognitive Services en Spark: Clústeres con servicios inteligentes integrados](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Discurso inaugural de Spark Summit: AI for Good escalable](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cognitive Services para macrodatos en Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Presentación de Lightning Talk sobre microservicios inteligentes a gran escala](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Cognitive Services para macrodatos](getting-started.md)
- [Ejemplos de Python sencillos](samples-python.md)
- [Ejemplos de Scala sencillos](samples-scala.md)