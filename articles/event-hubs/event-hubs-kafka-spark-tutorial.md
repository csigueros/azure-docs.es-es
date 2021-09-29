---
title: 'Conexión de la aplicación de Apache Spark: Azure Event Hubs | Microsoft Docs'
description: En este artículo se proporciona información sobre cómo usar Apache Spark con Azure Event Hubs para Kafka.
ms.topic: how-to
ms.date: 09/28/2021
ms.openlocfilehash: 8a68027643bed19db8109e66102f2ab3f9cbe36f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214472"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Conexión de una aplicación de Apache Spark con Azure Event Hubs
Este tutorial le guía a través de la conexión de la aplicación de Spark a Event Hubs para el streaming en tiempo real. Esta integración permite el streaming sin tener que cambiar sus clientes de protocolo o ejecutar sus propios clústeres de Kafka o Zookeeper. Este tutorial necesita Apache Spark v2.4 o posterior y Apache Kafka v2.0 o posterior.

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/).

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Creación de un espacio de nombres de Event Hubs
> * Clonación del proyecto de ejemplo
> * Ejecución de Spark
> * Lectura de Event Hubs para Kafka
> * Escritura de Event Hubs para Kafka

## <a name="prerequisites"></a>Prerrequisitos

Antes de comenzar este tutorial, asegúrese de que dispone de lo siguiente:
-   Suscripción de Azure. En caso de no tener ninguna, [cree una cuenta gratuita](https://azure.microsoft.com/free/).
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> El adaptador Spark-Kafka se actualizó para ser compatible con Kafka v2.0 a partir de Spark v2.4. En versiones anteriores de Spark, el adaptador admitía Kafka v0.10 y versiones posteriores, pero confiaba específicamente en las API de Kafka v0.10. Como Event Hubs para Kafka no admite Kafka v0.10, los adaptadores Spark-Kafka de versiones de Spark anteriores a la v2.4 no se admiten en Event Hubs para ecosistemas de Kafka.


## <a name="create-an-event-hubs-namespace"></a>Creación de un espacio de nombres de Event Hubs
Se requiere un espacio de nombres de Event Hubs para enviar y recibir de cualquier servicio de Event Hubs. Consulte [Creación de un centro de eventos](event-hubs-create.md) para obtener instrucciones sobre cómo crear un espacio de nombres y un centro de eventos. Obtenga la cadena de conexión de Event Hubs y el nombre de dominio completo (FQDN) para su uso posterior. Para obtener instrucciones, consulte [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Obtención de una cadena de conexión de Event Hubs). 

## <a name="clone-the-example-project"></a>Clonación del proyecto de ejemplo
Clone el repositorio de Azure Event Hubs y vaya a la subcarpeta `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Lectura de Event Hubs para Kafka
Con unos pocos cambios de configuración, puede empezar a leer desde Event Hubs para Kafka. Actualice **BOOTSTRAP_SERVERS** y **EH_SASL** con los detalles de su espacio de nombres y puede empezar a transmitir con Event Hubs como lo haría con Kafka. Para ver el código de ejemplo completo, consulte el archivo sparkConsumer.scala en GitHub. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "true")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Escritura de Event Hubs para Kafka
También puede escribir en Event Hubs de la misma manera que escribe en Kafka. No olvide actualizar su configuración para cambiar **BOOTSTRAP_SERVERS** y **EH_SASL** con información de su espacio de nombres de Event Hubs.  Para ver el código de ejemplo completo, consulte el archivo sparkProducer.scala en GitHub. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de Event Hubs y Event Hubs para Kafka, consulte los artículos siguientes:  

- [Reflejo de un agente de Kafka en un centro de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conexión de Apache Flink a un centro de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integración de Kafka Connect con un centro de eventos](event-hubs-kafka-connect-tutorial.md)
- [Exploración de ejemplos en nuestro GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conexión de Akka Streams a un centro de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guía del desarrollador de Apache Kafka para Azure Event Hubs](apache-kafka-developer-guide.md)

