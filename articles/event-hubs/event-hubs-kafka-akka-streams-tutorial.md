---
title: 'Uso de Akka Streams para Apache Kafka: Azure Event Hubs | Microsoft Docs'
description: En este artículo se proporciona información sobre cómo conectar Akka Streams a un centro de eventos de Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 500a287fcf481a8ef81ce1bb22df503e8757201d
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286455"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Uso de Akka Streams con Event Hubs para Apache Kafka

En este tutorial se muestra cómo conectar Akka Streams mediante la compatibilidad de Event Hubs con Apache Kafka sin cambiar los clientes del protocolo o ejecutar sus propios clústeres. 

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Creación de un espacio de nombres de Event Hubs
> * Clonación del proyecto de ejemplo
> * Ejecutar el productor de Akka Streams 
> * Ejecutar el consumidor de Akka Streams

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java).

## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* Lea el artículo [Event Hubs para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Suscripción a Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.
* [Kit de desarrollo de Java (JDK) 1.8+](/azure/developer/java/fundamentals/java-support-on-azure)
    * En Ubuntu, ejecute `apt-get install default-jdk` para instalar el JDK.
    * Asegúrese de establecer la variable de entorno JAVA_HOME para que apunte a la carpeta donde está instalado el JDK.
* [Descargue](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) un archivo binario de Maven
    * En Ubuntu, puede ejecutar `apt-get install maven` para instalar Maven.
* [Git](https://www.git-scm.com/downloads)
    * En Ubuntu, puede ejecutar `sudo apt-get install git` para instalar Git.

## <a name="create-an-event-hubs-namespace"></a>Creación de un espacio de nombres de Event Hubs

Se requiere un espacio de nombres de Event Hubs para enviar o recibir de cualquier servicio de Event Hubs. Consulte [Creación de un centro de eventos](event-hubs-create.md) para obtener más información detallada. Asegúrese de copiar la cadena de conexión de Event Hubs para su uso posterior.

## <a name="clone-the-example-project"></a>Clonación del proyecto de ejemplo

Ahora que tiene una cadena de conexión de Event Hubs, clone el repositorio de Azure Event Hubs para Kafka y vaya a la subcarpeta `akka`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Ejecutar el productor de Akka Streams

Con el ejemplo del productor de Akka Streams proporcionado, envíe mensajes al servicio Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Proporcionar un punto de conexión de Kafka para Event Hubs

#### <a name="producer-applicationconf"></a>application.conf del productor

Actualice los valores `bootstrap.servers` y `sasl.jaas.config` en `producer/src/main/resources/application.conf` para dirigir el productor al punto de conexión de Kafka para Event Hubs con la autenticación correcta.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

> [!IMPORTANT]
> Reemplace `{YOUR.EVENTHUBS.CONNECTION.STRING}` por la cadena de conexión para el espacio de nombres de Event Hubs. Para obtener instrucciones sobre cómo obtener la cadena de conexión, consulte [Obtención de una cadena de conexión de Event Hubs](event-hubs-get-connection-string.md). A continuación se muestra un ejemplo de configuración: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-producer-from-the-command-line"></a>Ejecución del productor desde la línea de comandos

Para ejecutar el productor desde la línea de comandos, genere los JAR y luego ejecútelo desde Maven (o genere los JAR con Maven, luego ejecútelo en Java al agregar los JAR de Kafka necesarios al parámetro classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

El productor ahora comienza a enviar eventos al centro de eventos en el tema `test` e imprimir los eventos a stdout.

## <a name="run-akka-streams-consumer"></a>Ejecutar el consumidor de Akka Streams

Con el ejemplo de consumidor proporcionado, reciba mensajes desde el centro de eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Proporcionar un punto de conexión de Kafka para Event Hubs

#### <a name="consumer-applicationconf"></a>application.conf del consumidor

Actualice los valores `bootstrap.servers` y `sasl.jaas.config` en `consumer/src/main/resources/application.conf` para dirigir el consumidor al punto de conexión de Kafka para Event Hubs con la autenticación correcta.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

> [!IMPORTANT]
> Reemplace `{YOUR.EVENTHUBS.CONNECTION.STRING}` por la cadena de conexión para el espacio de nombres de Event Hubs. Para obtener instrucciones sobre cómo obtener la cadena de conexión, consulte [Obtención de una cadena de conexión de Event Hubs](event-hubs-get-connection-string.md). A continuación se muestra un ejemplo de configuración: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Ejecución del consumidor desde la línea de comandos

Para ejecutar el consumidor desde la línea de comandos, genere los JAR y luego ejecútelo desde Maven (o genere los JAR con Maven, luego ejecútelo en Java al agregar los JAR de Kafka necesarios al parámetro classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Si el centro de eventos tiene eventos (por ejemplo, si el productor también está ejecutándose), el consumidor comienza a recibir eventos del tema `test`. 

Consulte la [Guía de Kafka de Akka Streams](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) para más información sobre Akka Streams.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de Event Hubs para Kafka, consulte los artículos siguientes:  

- [Reflejo de un agente de Kafka en un centro de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conexión de Apache Spark a un centro de eventos](event-hubs-kafka-spark-tutorial.md)
- [Conexión de Apache Flink a un centro de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integración de Kafka Connect con un centro de eventos](event-hubs-kafka-connect-tutorial.md)
- [Exploración de ejemplos en nuestro GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Guía del desarrollador de Apache Kafka para Azure Event Hubs](apache-kafka-developer-guide.md)