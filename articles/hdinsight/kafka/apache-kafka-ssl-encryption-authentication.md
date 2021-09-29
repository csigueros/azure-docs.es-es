---
title: 'Cifrado y autenticación TLS de Apache Kafka: Azure HDInsight'
description: Configure el cifrado TLS para la comunicación entre los clientes y los agentes de Kafka, así como entre estos últimos. Configure la autenticación SSL de clientes.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 0cac23b153940156b3c88a3b71e8900f1b21a695
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742969"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configuración del cifrado y la autenticación TLS para Apache Kafka en Azure HDInsight

En este artículo se muestra cómo configurar el cifrado de Seguridad de la capa de transporte (TLS), conocido anteriormente como cifrado de Capa de sockets seguros (SSL), entre los clientes de Apache Kafka y los agentes de Apache Kafka. También se muestra cómo configurar la autenticación de los clientes (lo que a veces se conoce como TLS bidireccional).

> [!Important]
> Existen dos clientes que se pueden usar para las aplicaciones de Kafka: uno de Java y otro de consola. Solo el cliente de Java `ProducerConsumer.java` puede utilizar TLS para producir y consumir. El cliente de productor de consola `console-producer.sh` no funciona con TLS.

> [!Note]
> El productor de la consola de HDInsight Kafka con la versión 1.1 no admite SSL.

## <a name="apache-kafka-broker-setup"></a>Programa de instalación de Apache Kafka Broker

El programa de instalación del agente de TLS de Kafka usará cuatro máquinas virtuales del clúster de HDInsight de la siguiente manera:

* Nodo principal 0: entidad de certificación (CA)
* nodo de trabajo 0, 1 y 2: agentes

> [!Note] 
> En esta guía se usarán los certificados autofirmados, pero la solución más segura es usar certificados emitidos por entidades de certificación de confianza.

El resumen del proceso de instalación de agente es como sigue:

1. Los siguientes pasos se repiten en cada uno de los tres nodos de trabajo:

    1. Genere un certificado.
    1. Cree una solicitud de firma de certificado.
    1. Envíe la solicitud de firma del certificado a la entidad de certificación (CA).
    1. Inicie sesión en la entidad de certificación y firme la solicitud.
    1. SCP el certificado firmado de vuelta en el nodo de trabajo.
    1. SCP el certificado público de la entidad de certificación para el nodo de trabajo.

1. Una vez que tenga todos los certificados, colóquelos en el almacén de certificados.
1. Vaya a Ambari y cambie las configuraciones.

Utilice las siguientes instrucciones detalladas para completar la instalación del agente:

> [!Important]
> En los siguientes fragmentos de código, wnX es una abreviatura de uno de los tres nodos de trabajo y debe sustituirse por `wn0`, `wn1` o `wn2`, según corresponda. `WorkerNode0_Name` y `HeadNode0_Name` deben sustituirse por los nombres de las máquinas respectivas.

1. Realice la instalación inicial en un nodo principal 0, lo que, para HDInsight, rellenará el rol de la entidad de certificación (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Realice la misma configuración inicial en cada uno de los agentes (nodos de trabajo 0, 1 y 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. En cada uno de los nodos de trabajo, ejecute los pasos siguientes con el siguiente fragmento de código.
    1. Cree un almacén de claves y rellénelo con un nuevo certificado privado.
    1. Cree una solicitud de firma de certificados.
    1. SCP la solicitud de firma de certificado a la entidad de certificación (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. En el equipo de la entidad de certificación, ejecute el siguiente comando para crear los archivos ca-cert y ca-key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Cambie a la máquina de la entidad de certificación y firme todas las solicitudes de firma de certificado recibidas:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envíe de vuelta los certificados firmados a los nodos de trabajo de la entidad de certificación (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. SCP el certificado público de la entidad de certificación para cada nodo de trabajo.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. En cada nodo de trabajo, agregue el certificado público de la entidad de certificación al almacén de claves y truststore. A continuación, agregue el certificado firmado del nodo de trabajo al almacén de claves.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Actualización de la configuración de Kafka para usar TLS y reiniciar los agentes

Ya ha configurado cada agente de Kafka con un almacén de claves y truststore, y ha importado los certificados correctos. A continuación, modifique las propiedades de la configuración de Kafka relacionadas mediante Ambari y luego reinicie los agentes de Kafka.

Realice los pasos siguientes para completar la modificación de la configuración:

1. Inicie sesión en Azure Portal y seleccione el clúster de Apache Kafka de Azure HDInsight.
1. Vaya a la interfaz de usuario de Ambari; para ello, haga clic en **Ambari home** (Inicio de Ambari) en **Cluster dashboards** (Paneles del clúster).
1. En **Kafka Broker** (Agente de Kafka), establezca la propiedad **listeners** en `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. En **Advanced kafka-broker** (Agente de Kafka avanzado), establezca la propiedad **security.inter.broker.protocol** en `SSL`

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png" alt-text="Edición de las propiedades de configuración de SSL de Kafka en Ambari" border="true":::

1. En **Custom kafka-broker** (Agente de Kafka personalizado), establezca la propiedad **ssl.client.auth** en `required`. Este paso solo es necesario si va a configurar la autenticación y el cifrado.

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png" alt-text="Edición de las propiedades de configuración de SSL de Kafka en Ambari" border="true":::

1. Para HDI 3.6, vaya a la interfaz de usuario de Ambari y agregue las siguientes configuraciones en las propiedades **Advanced kafka-env** y **kafka-env template**.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Esta captura de pantalla muestra la interfaz de usuario de configuración de Ambari con estos cambios.

    Para la versión 3.6 de HDI:

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png" alt-text="Edición de la propiedad kafka-env template en Ambari" border="true":::

    Para la versión 4.0 de HDI:

     :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png" alt-text="Edición de la propiedad de plantilla kafka-env en Ambari cuatro" border="true":::

1. Reinicie todos los agentes de Kafka.

## <a name="client-setup-without-authentication"></a>Configuración del cliente (sin autenticación)

Si no necesita realizar la autenticación, el resumen de los pasos para configurar solamente el cifrado TLS es:

1. Inicie sesión en la entidad de certificación (nodo principal activo).
1. Copie el certificado de CA en la máquina cliente de la máquina de CA (wn0).
1. Inicie sesión en la máquina cliente (hn1) y desplácese hasta la carpeta `~/ssl`.
1. Importe el certificado de CA al almacén de confianza.
1. Importe el certificado de CA al almacén de claves.

Estos pasos se describen en los siguientes fragmentos de código.

1. Inicie sesión en el nodo de CA.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Copia del certificado de CA en la máquina cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Inicie sesión en la máquina cliente (nodo principal en espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importe el certificado de CA en el almacén de confianza.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importe el certificado de CA en el almacén de claves.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Cree el archivo `client-ssl-auth.properties` en la máquina cliente (hn1). Debe tener las siguientes líneas:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Inicie el cliente de administración con las opciones de productor y consumidor para comprobar que tanto los productores como los consumidores están en funcionamiento en el puerto 9093. Consulte la sección [Comprobación](apache-kafka-ssl-encryption-authentication.md#verification) siguiente para ver los pasos necesarios para comprobar la configuración mediante el productor o consumidor de consola.

## <a name="client-setup-with-authentication"></a>Configuración del cliente (con autenticación)

> [!Note]
> Los pasos siguientes solo son necesarios si va a configurar el cifrado **y** la autenticación TLS. Si simplemente va a configurar el cifrado, continúe con [Configuración del cliente sin autenticación](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

En los cuatro pasos siguientes se resumen las tareas necesarias para completar la configuración del cliente:

1. Inicie sesión en la máquina cliente (nodo principal en espera).
1. Cree un almacén de claves Java y obtenga un certificado firmado para el agente. A continuación, copie el certificado en la máquina virtual donde se ejecuta la entidad de certificación.
1. Cambie a la máquina de la entidad de certificación (nodo principal activo) para firmar el certificado de cliente.
1. Vaya a la máquina cliente (nodo principal en espera) y desplácese hasta la carpeta `~/ssl`. Copie el certificado firmado en la máquina cliente.

A continuación se proporcionan los detalles de cada paso.

1. Inicie sesión en la máquina cliente (nodo principal en espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Quite todos los directorios de SSL existentes.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Cree un almacén de claves de Java y una solicitud de firma de certificado. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Copia de la solicitud de firma de certificado en la CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Cambie a la máquina de la entidad de certificación (nodo principal activo) y firme el certificado de cliente.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Copie el certificado de cliente firmado de la entidad de certificación (nodo principal activo) en la máquina cliente.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Copia del certificado de CA en la máquina cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Inicie sesión en la máquina cliente (nodo principal en espera) y desplácese hasta el directorio ssl.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Cree un almacén de cliente con el certificado firmado e importe el certificado de CA en el almacén de claves y el almacén de confianza en la máquina cliente (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-signed-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Cree un archivo `client-ssl-auth.properties` en la máquina cliente (hn1). Debe tener las siguientes líneas:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Comprobación

Ejecute estos pasos en la máquina cliente.

> [!Note]
> Si se instala HDInsight 4.0 y Kafka 2.1, puede usar el productor o los consumidores de la consola para comprobar la configuración. Si no, ejecute el productor Kafka en el puerto 9092 y envíe mensajes al tema; luego, use el consumidor Kafka en el puerto 9093, que usa TLS.

### <a name="kafka-21-or-above"></a>Kafka 2.1 o superior

1. Cree un tema, si todavía no existe.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Inicie el productor de la consola y proporcione la ruta de acceso a `client-ssl-auth.properties` como archivo de configuración para el productor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Abra otra conexión SSH a la máquina cliente, inicie el consumidor de consola y proporcione la ruta de acceso a `client-ssl-auth.properties` como archivo de configuración para el consumidor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. Cree un tema, si todavía no existe.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Inicie el productor de la consola y proporcione la ruta de acceso a client-ssl-auth.properties como archivo de configuración para el productor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Abra otra conexión SSH a la máquina cliente, inicie el consumidor de consola y proporcione la ruta de acceso a `client-ssl-auth.properties` como archivo de configuración para el consumidor.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Qué es Apache Kafka en HDInsight](apache-kafka-introduction.md)
