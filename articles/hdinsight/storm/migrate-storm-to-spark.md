---
title: Migración de Apache Storm para Azure HDInsight 3.6 a Apache Spark para HDInsight 4.0
description: Las diferencias y el flujo de migración de cargas de trabajo de Apache Storm a Spark Streaming o Spark Structured Streaming.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: c4f4156f80fac0c9e5eaae360aa937544d88aa9e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656804"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migración de Apache Storm para Azure HDInsight 3.6 a Apache Spark para HDInsight 4.0

En este documento se explica cómo migrar cargas de trabajo de Apache Storm para HDInsight 3.6 a HDInsight 4.0. Como HDInsight 4.0 no admite el tipo de clúster de Apache Storm, tendrá que migrar a otra plataforma de datos de streaming. Dos opciones adecuadas son Apache Spark Streaming y Spark Structured Streaming. En este documento se describen las diferencias entre estas plataformas y también se recomienda un flujo de trabajo para migrar cargas de trabajo de Apache Storm.

## <a name="storm-migration-paths-in-hdinsight"></a>Rutas de migración de Storm en HDInsight

Si quiere migrar desde Apache Storm en HDInsight 3.6 tiene varias opciones:

* Spark Streaming en HDInsight 4.0
* Spark Structured Streaming en HDInsight 4.0
* Azure Stream Analytics

En este documento se proporciona una guía para migrar de Apache Storm a Spark Streaming y Spark Structured Streaming.

:::image type="content" source="./media/migrate-storm-to-spark/storm-migration-path.png" alt-text="Ruta de migración de Storm para HDInsight" border="false":::

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Comparación entre Apache Storm, Spark Streaming y Spark Structured Streaming

Apache Storm puede proporcionar diferentes niveles de procesamiento de mensajes garantizado. Por ejemplo, una aplicación básica de Storm puede garantizar un procesamiento una vez al menos y [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) puede garantizar exactamente el procesamiento exactamente una vez. Spark Streaming y Spark Structured Streaming garantizan que todos los eventos de entrada se procesan exactamente una vez, incluso si se produce un error del nodo. Storm tiene un modelo que procesa cada evento, y también puede usar el modelo de microlotes con Trident. Spark Streaming y Spark Structured Streaming proporcionan un modelo de procesamiento de microlotes.

|  |Storm |Streaming de Spark | Spark Structured Streaming|
|---|---|---|---|
|**Garantía de procesamiento de eventos**|Al menos una vez <br> Exactamente una vez (Trident) |[Exactamente una vez](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Exactamente una vez](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Modelo de procesamiento**|Tiempo real <br> Microlotes (Trident) |Microlotes |Microlotes |
|**Compatibilidad con la hora del evento**|[Sí](https://storm.apache.org/releases/2.0.0/Windowing.html)|No|[Sí](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Lenguajes**|Java, etc.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark Streaming frente a Spark Structured Streaming

Spark Structured Streaming va a reemplazar a Spark Streaming (DStreams). Structured Streaming seguirá recibiendo mejoras y mantenimiento, mientras que DStreams estará solo en modo de mantenimiento. Structured Streaming no tiene tantas características como DStreams para los orígenes y receptores que admite de forma estándar, así que es aconsejable evaluar los requisitos para elegir la opción de procesamiento de secuencias de Spark adecuada.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Procesamiento de streaming (evento único) frente a procesamiento de microlotes

Storm proporciona un modelo que procesa cada evento único. Esto significa que todos los registros entrantes se procesarán en cuanto lleguen. Las aplicaciones de Spark Streaming deben esperar una fracción de segundo para recopilar cada microlote de eventos antes de enviar ese lote para procesarlo. En cambio, una aplicación controlada por eventos procesa cada evento inmediatamente. La latencia de Spark Streaming suele ser de unos segundos. Las ventajas del enfoque de los microlotes son una mayor eficacia del procesamiento de datos y cálculos agregados más sencillos.

:::image type="content" source="./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png" alt-text="Procesamiento de streaming y de microlotes" border="false":::

## <a name="storm-architecture-and-components"></a>Arquitectura y componentes de Storm

Las topologías de Storm están formadas por varios componentes que se organizan en un grafo acíclico dirigido (DAG). Los datos fluyen entre los componentes del grafo. Cada componente consume uno o varios flujos de datos y, opcionalmente, puede emitir uno o varios flujos.

|Componente |Descripción |
|---|---|
|Spout|Coloca los datos en una topología. emiten uno o varios flujos en la topología.|
|Bolt|Consume secuencias emitidas desde spouts u otros bolts. Además, podrían emitir flujos en la topología. También se encargan de escribir datos en un almacenamiento o servicios externos, como HDFS, Kafka o HBase.|

:::image type="content" source="./media/migrate-storm-to-spark/apache-storm-components.png" alt-text="Interacción de componentes de Storm" border="false":::

Storm consta de los tres demonios siguientes, que mantienen en funcionamiento el clúster de Storm.

|Demonio |Descripción |
|---|---|
|Nimbus|De forma similar a JobTracker de Hadoop, es responsable de distribuir el código en torno al clúster y de asignar tareas a las máquinas y supervisar los errores.|
|Zookeeper|Se usa para la coordinación del clúster.|
|Supervisor|Escucha el trabajo asignado a su máquina e inicia y detiene los procesos de trabajo en función de las directivas de Nimbus. Cada proceso de trabajo ejecuta un subconjunto de una topología. Aquí se ejecuta la lógica de aplicación del usuario (spouts y bolt).|

:::image type="content" source="./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png" alt-text="Demonios de Nimbus, Zookeeper y Supervisor" border="false":::

## <a name="spark-streaming-architecture-and-components"></a>Arquitectura y componentes de Spark Streaming

En los pasos siguientes se resume cómo funcionan juntos los componentes de Spark Streaming (DStreams) y Spark Structured Streaming:

* Cuando se inicia Spark Streaming, el controlador inicia la tarea en el ejecutor.
* El ejecutor recibe una secuencia de un origen de datos de streaming.
* Cuando el ejecutor recibe secuencias de datos, divide la secuencia en bloques y los mantiene en memoria.
* Los bloques de datos se replican en otros ejecutores.
* Los datos procesados se almacenan luego en el almacén de datos de destino.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-to-output.png" alt-text="Ruta de Spark Streaming a la salida" border="false":::

## <a name="spark-streaming-dstream-workflow"></a>Flujo de trabajo de Spark Streaming (DStream)

Cuando transcurre cada intervalo entre lotes, se genera un nuevo diseño que contiene todos los datos de ese intervalo. Los conjuntos continuos de RDD se recopilan en una instancia de DStream. Por ejemplo, si el intervalo entre lotes es de un segundo, el flujo DStream emite un lote cada segundo con un RDD que contiene todos los datos ingeridos durante ese segundo. Al procesar el flujo DStream, el evento de temperatura se muestra en uno de estos lotes. Una aplicación de Spark Streaming procesa los lotes que contienen los eventos y, en última instancia, actúa en los datos almacenados en cada RDD.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-batches.png" alt-text="Lotes de procesamiento de Spark Streaming" border="false":::

Para más información sobre las distintas transformaciones disponibles con Spark Streaming, consulte [Transformaciones en DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Spark Structured Streaming

Spark Structured Streaming representa una secuencia de datos como una tabla sin límite de profundidad. La tabla sigue creciendo conforme llegan datos nuevos. Esta tabla de entrada se procesa continuamente mediante una consulta de larga duración y los resultados se envían a una tabla de salida.

En Structured Streaming, los datos llegan al sistema, se ingieran de inmediato y se colocan en una tabla de entrada. Se escriben consultas (mediante las API de DataFrame y Dataset) que realizan operaciones en esta tabla de entrada.

La salida de la consulta produce una *tabla de resultados*, que contiene los resultados de la consulta. Puede extraer datos de la tabla de resultados para un almacén de datos externo, como una base de datos relacional.

El intervalo del desencadenador controla el momento en el que se procesan los datos desde la tabla de entrada. De manera predeterminada, el valor del intervalo del desencadenador es cero, por lo que Structured Streaming intenta procesar los datos en cuanto llegan. En la práctica, esto significa que en cuanto Structured Streaming finaliza el procesamiento de la ejecución de la consulta anterior, inicia otro procesamiento en el que se ejecutan los datos recién recibidos. Puede configurar el desencadenador para que se ejecute en un intervalo, con el fin de que los datos de streaming se procesen en lotes temporales.

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-data-processing.png" alt-text="Procesamiento de datos en Structured Streaming" border="false":::

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-model.png" alt-text="Modelo de programación para Structured Streaming" border="false":::

## <a name="general-migration-flow"></a>Flujo de migración general

El flujo de migración recomendado de Storm a Spark supone la siguiente arquitectura inicial:

* Kafka se usa como origen de datos de streaming
* Kafka y Storm se implementan en la misma red virtual
* Los datos procesados por Storm se escriben en un receptor de datos, como Azure Storage o Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/migrate-storm-to-spark/presumed-current-environment.png" alt-text="Diagrama del entorno actual supuesto"  border="false":::

Para migrar la aplicación de Storm a una de las API de Spark Streaming, haga lo siguiente:

1. **Implemente un nuevo clúster.** Implemente un nuevo clúster de Spark para HDInsight 4.0 en la misma red virtual e implemente en él la aplicación de Spark Streaming o Spark Structured Streaming y pruébela a conciencia.

   :::image type="content" source="./media/migrate-storm-to-spark/new-spark-deployment.png" alt-text="Nueva implementación de Spark en HDInsight" border="false":::

1. **Deje de consumir en el antiguo clúster de Storm.** En el clúster de Storm existente, deje de consumir datos del origen de datos de streaming y espere a que los datos terminen de escribirse en el receptor de destino.

   :::image type="content" source="./media/migrate-storm-to-spark/stop-consuming-current-cluster.png" alt-text="Dejar de consumir en el clúster actual" border="false":::

1. **Comience a consumir en el nuevo clúster de Spark.** Inicie el streaming de datos desde un clúster de Spark para HDInsight 4.0 recién implementado. En este momento, el proceso se controla mediante el consumo del desplazamiento de Kafka más reciente.

   :::image type="content" source="./media/migrate-storm-to-spark/start-consuming-new-cluster.png" alt-text="Empezar a consumir en el nuevo clúster" border="false":::

1. **Quite el clúster antiguo según sea necesario.** Una vez que se ha completado el cambio y el funcionamiento es correcto, quite el clúster antiguo de Storm para HDInsight 3.6 según sea necesario.

   :::image type="content" source="./media/migrate-storm-to-spark/remove-old-clusters1.png" alt-text="Quitar los clústeres de HDInsight antiguos según sea necesario" border="false":::

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Storm, Spark Streaming y Spark Structured Streaming, consulte los siguientes documentos:

* [Introducción a Apache Spark Streaming](../spark/apache-spark-streaming-overview.md)
* [Información general acerca de Apache Spark Structured Streaming](../spark/apache-spark-structured-streaming-overview.md)
