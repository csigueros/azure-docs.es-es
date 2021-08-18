---
title: Uso de Apache Kafka MirrorMaker en Azure Event Hubs | Microsoft Docs
description: En este artículo se proporciona información acerca de cómo usar Kafka MirrorMaker para crear el reflejo de un clúster de Kafka en Azure Event Hubs.
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: beb48fc613ace6dd953c3fe773f09d5fb4c4c822
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286419"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Uso de Apache Kafka MirrorMaker con Event Hubs

En este ejemplo se muestra cómo crear un reflejo de un agente de Kafka en Azure Event Hub mediante Kafka MirrorMaker. Si hospeda Apache Kafka en Kubernetes mediante el operador CNCF Strimzi, puede consultar el tutorial de [esta entrada de blog](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/) para obtener información sobre cómo configurar Kafka con Strimzi y MirrorMaker 2. 

   ![Kafka MirrorMaker con Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker).

> [!NOTE]
> Este artículo contiene referencias al término *lista blanca*, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Creación de un espacio de nombres de Event Hubs
> * Clonación del proyecto de ejemplo
> * Configuración de un clúster de Kafka
> * Configurar de Kafka MirrorMaker
> * Ejecución de Kafka MirrorMaker

## <a name="introduction"></a>Introducción
En este tutorial se muestra la manera en que un centro de eventos y Kafka MirrorMaker se pueden integrar en una canalización de Kafka existente en Azure al crear un "reflejo" del flujo de entrada de Kafka en el servicio Event Hubs, lo que permite la integración de secuencias de Apache Kafka mediante el uso de varios [patrones de federación](event-hubs-federation-overview.md). 

Un punto de conexión de Kafka en Azure Event Hubs permite conectarse a Azure Event Hubs mediante el protocolo Kafka (es decir, clientes de Kafka). Al realizar cambios mínimos en una aplicación de Kafka, puede conectarse a Azure Event Hubs y disfrutar de las ventajas del ecosistema de Azure. Event Hubs es actualmente compatible con el protocolo de Apache Kafka, versión 1.0 y posteriores.

Puede usar MirrorMaker 1 de Apache Kafka unidireccionalmente desde Apache Kafka a Event Hubs. MirrorMaker 2 se puede usar en ambas direcciones, pero los conectores [`MirrorCheckpointConnector` y `MirrorHeartbeatConnector` que se pueden configurar en MirrorMaker 2](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0) deben estar configurados para apuntar al agente de Apache Kafka, y no Event Hubs. En este tutorial se muestra la configuración de MirrorMaker 1.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de disponer de los siguientes elementos:

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

Se requiere un espacio de nombres de Event Hubs para enviar y recibir de cualquier servicio de Event Hubs. Consulte [Creación de un centro de eventos](event-hubs-create.md) para obtener instrucciones sobre cómo crear un espacio de nombres y un centro de eventos. Asegúrese de copiar la cadena de conexión de Event Hubs para su uso posterior.

## <a name="clone-the-example-project"></a>Clonación del proyecto de ejemplo

Ahora que tiene una cadena de conexión de Event Hubs, clone el repositorio de Azure Event Hubs para Kafka y vaya a la subcarpeta `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configuración de un clúster de Kafka

Utilice la [guía de inicio rápido de Kafka](https://kafka.apache.org/quickstart) para configurar un clúster con la configuración deseada (o use un clúster de Kafka existente).

## <a name="configure-kafka-mirrormaker"></a>Configurar de Kafka MirrorMaker

Kafka MirrorMaker permite la "creación de reflejos" de un flujo de datos. Dados clústeres de Kafka de origen y destino, MirrorMaker garantiza que los mensajes enviados al clúster de origen se reciban tanto en el clúster de origen como en el clúster de destino. En este ejemplo se muestra cómo crear el reflejo de un clúster de Kafka de origen con el centro de eventos de destino. Este escenario puede utilizarse para enviar datos de una canalización de Kafka existente a Event Hubs sin interrumpir el flujo de datos. 

Para obtener información más detallada sobre Kafka MirrorMaker, consulte la [guía de creación de reflejo de Kafka para MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Para configurar Kafka MirrorMaker, asígnele un clúster de Kafka como su consumidor u origen, y un centro de evento como su productor o destino.

#### <a name="consumer-configuration"></a>Configuración del consumidor

Actualice el archivo de configuración del consumidor `source-kafka.config`, que indica a MirrorMaker las propiedades del clúster de origen de Kafka.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuración del productor

Ahora, actualice el archivo de configuración del productor `mirror-eventhub.config`, que indica a MirrorMaker que envíe los datos duplicados (o "reflejados") al servicio de Event Hubs. En concreto, cambie `bootstrap.servers` y `sasl.jaas.config` para que apunten a su punto de conexión de Kafka para Event Hubs. El servicio de Event Hubs requiere una comunicación segura (SASL), que se consigue estableciendo las tres últimas propiedades en la configuración siguiente: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Reemplace `{YOUR.EVENTHUBS.CONNECTION.STRING}` por la cadena de conexión para el espacio de nombres de Event Hubs. Para obtener instrucciones sobre cómo obtener la cadena de conexión, consulte [Obtención de una cadena de conexión de Event Hubs](event-hubs-get-connection-string.md). A continuación se muestra un ejemplo de configuración: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Ejecución de Kafka MirrorMaker

Ejecute el script de Kafka MirrorMaker desde el directorio raíz de Kafka con los archivos de configuración recientemente actualizados. Asegúrese de copiar los archivos de configuración al directorio raíz de Kafka, o de actualizar sus rutas de acceso en el siguiente comando.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para comprobar que los eventos llegan al centro de eventos, consulte las estadísticas de entrada en [Azure Portal](https://azure.microsoft.com/features/azure-portal/) o ejecute un consumidor en el centro de eventos.

Con MirrorMaker en ejecución, los eventos que se envían al clúster de Kafka de origen son recibidos por el clúster de Kafka y el centro de eventos reflejado. Al usar MirrorMaker y un punto de conexión de Kafka para Event Hubs, puede migrar una canalización de Kafka existente al servicio de Azure Event Hubs administrado sin cambiar el clúster existente ni interrumpir ningún flujo de datos en curso.

## <a name="samples"></a>Ejemplos
Consulte los siguientes ejemplos en GitHub:

- [Código de ejemplo de este tutorial en GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Kafka MirrorMaker para Azure Event Hubs que se ejecuta en una instancia de contenedor de Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de Event Hubs para Kafka, consulte los artículos siguientes:  

- [Conexión de Apache Spark a un centro de eventos](event-hubs-kafka-spark-tutorial.md)
- [Conexión de Apache Flink a un centro de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integración de Kafka Connect con un centro de eventos](event-hubs-kafka-connect-tutorial.md)
- [Exploración de ejemplos en nuestro GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conexión de Akka Streams a un centro de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guía del desarrollador de Apache Kafka para Azure Event Hubs](apache-kafka-developer-guide.md)