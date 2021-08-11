---
title: 'Tutorial: Producer API y Consumer API de Apache Kafka en Azure HDInsight'
description: Aprenda a usar Producer y Consumer API de Apache Kafka con Kafka en HDInsight. En este tutorial, aprenderá a usar estas API con Kafka en HDInsight desde una aplicación de Java.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 35115f56690d4ea0f8a70bb5c3099735101a9a29
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287447"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutorial: Uso de Producer API y Consumer API de Apache Kafka

Aprenda a usar Producer y Consumer API de Apache Kafka con Kafka en HDInsight.

Producer API de Kafka permite a las aplicaciones enviar flujos de datos al clúster de Kafka. Consumer API de Kafka permite a las aplicaciones leer flujos de datos del clúster.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Prerrequisitos
> * Comprendiendo el código
> * Compilar e implementar la aplicación
> * Ejecutar la aplicación en el clúster

Para más información acerca de las API, consulte la documentación de Apache relativa a [Producer API](https://kafka.apache.org/documentation/#producerapi) y a [Consumer API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Prerrequisitos

* Apache Kafka en un clúster de HDInsight. Para obtener información sobre cómo crear el clúster, consulte [Inicio con Apache Kafka en HDInsight](apache-kafka-get-started.md).
* [Java Developer Kit (JDK) versión 8](/azure/developer/java/fundamentals/java-support-on-azure), o un equivalente, como OpenJDK.
* [Apache Maven](https://maven.apache.org/download.cgi) correctamente [instalado](https://maven.apache.org/install.html) según Apache.  Maven es un sistema de compilación de proyectos de Java.
* Un cliente SSH como Putty. Para más información, consulte [Conexión a través de SSH con HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Comprendiendo el código

La aplicación de ejemplo se encuentra en [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), en el subdirectorio `Producer-Consumer`. Si usa un clúster de Kafka habilitado para **Enterprise Security Package (ESP)** , debería usar la versión de la aplicación ubicada en el subdirectorio `DomainJoined-Producer-Consumer`.

Básicamente, la aplicación consta de cuatro archivos:
* `pom.xml`: este archivo define las dependencias del proyecto, la versión de Java y los métodos de empaquetado.
* `Producer.java`: este archivo envía oraciones aleatorias a Kafka mediante Producer API.
* `Consumer.java`: este archivo usa Consumer API para leer datos de Kafka y emitirlos en STDOUT.
* `AdminClientWrapper.java`: Este archivo usa la API de administración para crear, describir y eliminar temas de Kafka.
* `Run.java`: la interfaz de línea de comandos que se usa para ejecutar el código de Producer API y Consumer API.

### <a name="pomxml"></a>Pom.xml

Esto es lo más importante que hay que saber del archivo `pom.xml`:

* Dependencias: este proyecto utiliza Producer API y Consumer API de Kafka, ambas proporcionadas por el paquete `kafka-clients`. El siguiente código XML define esta dependencia:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    La entrada `${kafka.version}` se declara en la sección `<properties>..</properties>` de `pom.xml` y está configurada para la versión Kafka del clúster de HDInsight.

* Complementos: los complementos de Maven proporcionan varias funcionalidades. En este proyecto, se utilizan los siguientes complementos:

    * `maven-compiler-plugin`: se utiliza para establecer que el proyecto utiliza la versión 8 de Java. Esta es la versión de Java que utiliza HDInsight 3.6.
    * `maven-shade-plugin`: se utiliza para generar un archivo uber-jar que contiene esta aplicación, así como todas las dependencias. También se usa para establecer el punto de entrada de la aplicación, con el fin de que pueda ejecutar directamente el archivo Jar sin tener que especificar la clase principal.

### <a name="producerjava"></a>Producer.java

El productor se comunica con los hosts del agente de Kafka (nodos de trabajo) y envía datos a un tema de Kafka. El siguiente fragmento de código es del archivo [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) del [repositorio de GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) y muestra cómo establecer las propiedades del productor. En el caso de los clústeres con seguridad empresarial habilitada, se debe agregar una propiedad adicional "properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");"

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

El consumidor se comunica con los hosts del agente de Kafka (nodos de trabajo) y lee los registros en un bucle. El siguiente fragmento de código del archivo [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) establece las propiedades del consumidor. En el caso de los clústeres con seguridad empresarial habilitada, se debe agregar una propiedad adicional "properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");"

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

En este código, el consumidor está configurado para leer desde el principio del tema (`auto.offset.reset` está establecido en `earliest`).

### <a name="runjava"></a>Run.java

El archivo [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) proporciona una interfaz de línea de comandos que ejecuta el código del productor o del consumidor. Debe proporcionar la información del host del agente de Kafka como parámetro. También puede incluir un valor del identificador de grupo. Dicho valor lo utiliza el proceso de consumidor. Si crea varias instancias de consumidor con el mismo identificador de grupo, equilibrarán la carga de la lectura del tema.

## <a name="build-and-deploy-the-example"></a>Compilación e implementación del ejemplo

### <a name="use-pre-built-jar-files"></a>Uso de archivos JAR pregenerados

Descargue los archivos jar del [ejemplo Get de Kafka de introducción de Azure](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). Si el clúster está habilitado para **Enterprise Security Package (ESP)** , use el archivo kafka-producer-consumer-esp.jar. Use el siguiente comando para copiar los archivos JAR al clúster.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>Compilación de los archivos JAR a partir del código

Si desea omitir este paso, puede descargar archivos jar creados previamente desde el subdirectorio `Prebuilt-Jars`. Descargue el archivo kafka-producer-consumer.jar. Si el clúster está habilitado para **Enterprise Security Package (ESP)** , use el archivo kafka-producer-consumer-esp.jar. Ejecute el paso 3 para copiar el archivo jar en el clúster de HDInsight.

1. Descarga y extracción de los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Establezca el directorio actual en la ubicación del directorio `hdinsight-kafka-java-get-started\Producer-Consumer`. Si usa un clúster de Kafka habilitado para **Enterprise Security Package (ESP)** , debería establecer la ubicación en el subdirectorio `DomainJoined-Producer-Consumer`. Utilice el siguiente comando para compilar la aplicación:

    ```cmd
    mvn clean package
    ```

    Este comando crea un directorio denominado `target`, que contiene un archivo denominado `kafka-producer-consumer-1.0-SNAPSHOT.jar`. En el caso de los clústeres ESP, el archivo será `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. Reemplace `sshuser` por el usuario de SSH del clúster y `CLUSTERNAME` por el nombre de su clúster. Escriba el siguiente comando para copiar el archivo `kafka-producer-consumer-1.0-SNAPSHOT.jar` en el clúster de HDInsight. Cuando se le solicite, escriba la contraseña del usuario SSH.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> Ejecutar el ejemplo

1. Reemplace `sshuser` por el usuario de SSH del clúster y `CLUSTERNAME` por el nombre de su clúster. Abra una conexión de SSH con el clúster, para lo que debe escribir el siguiente comando. Si se le solicita, escriba la contraseña de la cuenta de usuario de SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para obtener los hosts del agente de Kafka, sustituya los valores de `<clustername>` y `<password>` en el comando siguiente y ejecútelo. Mantenga el mismo uso de mayúsculas y minúsculas para `<clustername>` que se muestra en Azure Portal. Reemplace `<password>` por la contraseña de inicio de sesión del clúster y, a continuación, ejecute:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Este comando requiere acceso a Ambari. Si el clúster se encuentra detrás de un grupo de seguridad de red, ejecute este comando desde una máquina que pueda acceder a Ambari.

1. Cree un tema de Kafka, `myTest`, para lo que debe usar el siguiente comando:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Para ejecutar el productor y escribir datos en el tema, utilice el siguiente comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Cuando finalice el productor, use el comando siguiente para leer desde el tema:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    Se muestran los registros leídos, junto con un número de registros.

1. Use __Ctrl + C__ para salir del consumidor.

### <a name="multiple-consumers"></a>Varios consumidores

Los consumidores de Kafka usan un grupo de consumidores al leer los registros. Usar el mismo grupo con varios consumidores deriva en lecturas de un tema con equilibrio de carga. Cada consumidor del grupo recibe una parte de los registros.

La aplicación del consumidor acepta un parámetro que se usa como identificador del grupo. Por ejemplo, el comando siguiente inicia un consumidor mediante un identificador de grupo `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Use __Ctrl + C__ para salir del consumidor.

Para ver este proceso en acción, use el siguiente comando:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Este comando usa `tmux` para dividir el terminal en dos columnas. Se inicia un consumidor en cada una de ellas, con el mismo valor de identificador de grupo. Una vez que los consumidores terminan de leer, fíjese en que cada uno de ellos lee solo una parte de los registros. Use __Ctrl + C__ dos veces para salir de `tmux`.

El consumo por clientes del mismo grupo se controla mediante las particiones del tema. En este ejemplo de código, el tema `test` creado anteriormente tiene ocho particiones. Si inicia ocho consumidores, cada uno de ellos lee los registros de una única partición del tema.

> [!IMPORTANT]  
> No puede haber más instancias de consumidor en un grupo de consumidores que particiones. En este ejemplo, un grupo de consumidores puede contener hasta ocho, ya que es el número de particiones del tema. O bien, puede tener varios grupos de consumidores, los cuales no tengan más de ocho consumidores cada uno.

Los registros almacenados en Kafka se almacenan en el orden en el que se reciben dentro de una partición. Para lograr la entrega ordenada de registros *dentro de una partición*, cree un grupo de consumidores donde el número de instancias de consumidor coincida con el número de particiones. Para lograr la entrega ordenada de registros *dentro del tema*, cree un grupo de consumidores con solo una instancia de consumidor.

## <a name="common-issues-faced"></a>Problemas comunes a los que se enfrenta

1. **No se puede crear el tema** Si el clúster está habilitado para Enterprise Security Pack, use los [archivos JAR pregenerados para el productor y el consumidor](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar). El archivo JAR de ESP se puede crear a partir del código del subdirectorio [`DomainJoined-Producer-Consumer`](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer). Las propiedades del productor y el consumidor tienen una propiedad adicional `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` para los clústeres habilitados para ESP.

2. **Error en los clústeres habilitados para ESP**: si se produce un error en las operaciones de producción y consumo, y se usa un clúster habilitado para ESP, compruebe que el usuario `kafka` existe en todas las directivas de Ranger. Si no está presente, agréguelo a todas las directivas de Ranger.

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar los recursos creados por este tutorial puede eliminar el grupo de recursos. Al eliminar el grupo de recursos, también se elimina el clúster de HDInsight asociado y otros recursos asociados al grupo.

Para quitar el grupo de recursos mediante Azure Portal:

1. En Azure Portal, expanda el menú en el lado izquierdo para abrir el menú de servicios y elija __Grupos de recursos__ para mostrar la lista de sus grupos de recursos.
2. Busque el grupo de recursos que desea eliminar y haga clic con el botón derecho en __Más__ (...) en el lado derecho de la lista.
3. Seleccione __Eliminar grupo de recursos__ y confirme la elección.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a usar Producer y Consumer API de Apache Kafka con Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka:

* [Uso del proxy REST de Kafka](rest-proxy.md)
* [Análisis de registros de Apache Kafka](apache-kafka-log-analytics-operations-management.md)