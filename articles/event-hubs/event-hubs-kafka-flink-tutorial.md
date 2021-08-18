---
title: 'Uso de Apache Flink para Apache Kafka: Azure Event Hubs | Microsoft Docs'
description: En este artículo se proporciona información sobre cómo conectar Apache Flink a un centro de eventos de Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 84dd1f735ae041449bba474fd1147c7084bd5f0e
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286437"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Uso de Apache Flink con Azure Event Hubs para Apache Kafka
En este tutorial se muestra cómo conectar Apache Flink a un centro de eventos sin cambiar los clientes del protocolo o ejecutar sus propios clústeres. Para más información sobre la compatibilidad de Event Hubs con el protocolo de consumidor de Apache Kafka, consulte [Event Hubs para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).


En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Creación de un espacio de nombres de Event Hubs
> * Clonación del proyecto de ejemplo
> * Ejecutar el productor Flink 
> * Ejecutar el consumidor Flink

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* Lea el artículo [Event Hubs para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Suscripción a Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.
* [Kit de desarrollo de Java (JDK) 1.7+](/azure/developer/java/fundamentals/java-support-on-azure)
    * En Ubuntu, ejecute `apt-get install default-jdk` para instalar el JDK.
    * Asegúrese de establecer la variable de entorno JAVA_HOME para que apunte a la carpeta donde está instalado el JDK.
* [Descargue](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) un archivo binario de Maven
    * En Ubuntu, puede ejecutar `apt-get install maven` para instalar Maven.
* [Git](https://www.git-scm.com/downloads)
    * En Ubuntu, puede ejecutar `sudo apt-get install git` para instalar Git.

## <a name="create-an-event-hubs-namespace"></a>Creación de un espacio de nombres de Event Hubs

Se requiere un espacio de nombres de Event Hubs para enviar o recibir de cualquier servicio de Event Hubs. Vea [Creación de un centro de eventos](event-hubs-create.md) para obtener instrucciones sobre cómo crear un espacio de nombres y un centro de eventos. Asegúrese de copiar la cadena de conexión de Event Hubs para su uso posterior.

## <a name="clone-the-example-project"></a>Clonación del proyecto de ejemplo

Ahora que tiene una cadena de conexión de Event Hubs, clone el repositorio de Azure Event Hubs para Kafka y vaya a la subcarpeta `flink`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Ejecutar el productor Flink

Con el ejemplo de productor de Flink proporcionado, envíe mensajes al servicio Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Proporcionar un punto de conexión de Kafka para Event Hubs

#### <a name="producerconfig"></a>Producer.config

Actualice los valores `bootstrap.servers` y `sasl.jaas.config` en `producer/src/main/resources/producer.config` para dirigir el productor al punto de conexión de Kafka para Event Hubs con la autenticación correcta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Reemplace `{YOUR.EVENTHUBS.CONNECTION.STRING}` por la cadena de conexión para el espacio de nombres de Event Hubs. Para obtener instrucciones sobre cómo obtener la cadena de conexión, consulte [Obtención de una cadena de conexión de Event Hubs](event-hubs-get-connection-string.md). A continuación se muestra un ejemplo de configuración: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

### <a name="run-producer-from-the-command-line"></a>Ejecución del productor desde la línea de comandos

Para ejecutar el productor desde la línea de comandos, genere los JAR y luego ejecútelo desde Maven (o genere los JAR con Maven, luego ejecútelo en Java al agregar los JAR de Kafka necesarios al parámetro classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

El productor ahora comenzará a enviar eventos al centro de eventos en el tema `test` e imprimir los eventos en stdout.

## <a name="run-flink-consumer"></a>Ejecutar el consumidor Flink

Con el ejemplo de consumidor proporcionado, reciba mensajes desde el centro de eventos. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Proporcionar un punto de conexión de Kafka para Event Hubs

#### <a name="consumerconfig"></a>consumer.config

Actualice los valores `bootstrap.servers` y `sasl.jaas.config` en `consumer/src/main/resources/consumer.config` para dirigir el consumidor al punto de conexión de Kafka para Event Hubs con la autenticación correcta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Reemplace `{YOUR.EVENTHUBS.CONNECTION.STRING}` por la cadena de conexión para el espacio de nombres de Event Hubs. Para obtener instrucciones sobre cómo obtener la cadena de conexión, consulte [Obtención de una cadena de conexión de Event Hubs](event-hubs-get-connection-string.md). A continuación se muestra un ejemplo de configuración: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Ejecución del consumidor desde la línea de comandos

Para ejecutar el consumidor desde la línea de comandos, genere los JAR y luego ejecútelo desde Maven (o genere los JAR con Maven, luego ejecútelo en Java al agregar los JAR de Kafka necesarios al parámetro classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Si el centro de eventos tiene eventos (por ejemplo, si el productor también se está ejecutando), el consumidor comienza a recibir eventos del tema `test`.

Revise la [guía del conector Kafka de Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) para más información acerca de cómo conectar Flink a Kafka.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de Event Hubs para Kafka, consulte los artículos siguientes:  

- [Reflejo de un agente de Kafka en un centro de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conexión de Apache Spark a un centro de eventos](event-hubs-kafka-spark-tutorial.md)
- [Integración de Kafka Connect con un centro de eventos](event-hubs-kafka-connect-tutorial.md)
- [Exploración de ejemplos en nuestro GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conexión de Akka Streams a un centro de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guía del desarrollador de Apache Kafka para Azure Event Hubs](apache-kafka-developer-guide.md)