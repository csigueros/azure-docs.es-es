---
title: Temas de Apache Kafka en Azure HDInsight
description: Aprenda a usar la característica de creación de reflejo de Apache Kafka para mantener una réplica de un Kafka en un clúster de HDInsight mediante el reflejo de temas en un clúster secundario.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 7327af790eb8a3ddda646f0da208083d4431934a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112280232"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Uso de MirrorMaker para replicar temas de Apache Kafka con Kafka en HDInsight

Obtenga información sobre cómo usar la característica de creación de reflejo de Apache Kafka para replicar temas a un clúster secundario. La creación de reflejo se puede ejecutar como proceso continuo, o utilizar de forma intermitente como método de migración de datos de un clúster a otro.

> [!NOTE]
> Este artículo contiene referencias al término *lista de permitidos*, un término que Microsoft ya no usa. Cuando se quite el término del software, se quitará también del artículo.

En este ejemplo, la creación de reflejo se usa para replicar temas entre dos clústeres de HDInsight. Los dos clústeres están en redes virtuales diferentes de distintos centros de datos.

> [!WARNING]  
> La creación de reflejo no debe considerarse como un medio para conseguir tolerancia a errores. El desplazamiento a los elementos de un tema es diferente entre los clústeres principales y secundarios, por lo que los clientes no pueden usarlos indistintamente.
>
> Si le preocupa la tolerancia a errores, establezca la replicación para los temas en el clúster. Para obtener más información, vea [Introducción a Apache Kafka en HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Funcionamiento de la creación de reflejo de Apache Kafka

La creación de reflejo se realiza con la herramienta [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (incluida en Apache Kafka), que consume los registros de los temas del clúster principal y crea una copia local en el clúster secundario. MirrorMaker usa uno o varios *consumidores* que leen los datos del clúster principal y un *productor* que escribe los datos en el clúster local (secundario).

La configuración de la creación de reflejo que resulta más útil para la recuperación ante desastres consiste en utilizar clústeres de Kafka en diferentes regiones de Azure. Para ello, se emparejan las redes virtuales en las que residen los clústeres.

En el siguiente diagrama, se ilustra el proceso de creación de reflejo y cómo fluye la comunicación entre los clústeres:

:::image type="content" source="./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png" alt-text="Diagrama del proceso de creación de reflejo" border="false":::

Los clústeres principales y secundarios pueden tener un número diferente de nodos y de particiones. Además, los desplazamientos dentro de los temas también son diferentes. La creación de reflejos conserva el valor de la clave que se utiliza para crear particiones, por lo que se mantiene el orden de los registros en una base por claves.

### <a name="mirroring-across-network-boundaries"></a>Creación de reflejo en los límites de red

Si tiene que crear un reflejo entre los clústeres Kafka en distintas redes, existen las siguientes consideraciones adicionales:

* **Puertas de enlace**: las redes deben poder comunicarse en el nivel TCP/IP.

* **Direccionamiento del servidor**: Si lo desea, puede direccionar los nodos del clúster utilizando direcciones IP o nombres de dominio completo.

    * **Direcciones IP**: Si configura los clústeres de Kafka para que utilicen publicidad basada en direcciones IP, puede configurar la creación de reflejo utilizando las direcciones IP de los nodos de agentes y los nodos de Zookeeper.
    
    * **Nombres de dominio**: Si no configura los clústeres de Kafka para que utilicen publicidad basada en direcciones IP, es necesario que los clústeres puedan conectarse entre sí utilizando los nombres de dominio completo (FQDN). Para ello, debe haber un Sistema de nombres de dominio (DNS) en cada red que esté configurado de forma que se reenvíen solicitudes a las demás redes. Al crear una instancia de Azure Virtual Network, en lugar de usar el DNS automático proporcionado con la red, debe especificar un servidor DNS personalizado y la dirección IP para el servidor. Una vez creada la red virtual, debe crear una máquina virtual de Azure que use esa dirección IP, y en ella instalar y configurar el software DNS.

    > [!WARNING]  
    > Cree y configure el servidor DNS personalizado antes de instalar HDInsight en la red virtual. No es necesaria ninguna configuración adicional para que HDInsight use el servidor DNS configurado para la red virtual.

Para más información sobre cómo conectar dos instancias de Azure Virtual Network, consulte [Configuración de una conexión de red virtual a red virtual](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Arquitectura de creación de reflejo

Esta arquitectura contiene dos clústeres en diferentes grupos de recursos y redes virtuales: uno **principal** y otro **secundario**.

### <a name="creation-steps"></a>Pasos de creación

1. Cree dos nuevos grupos de recursos:

    |Grupo de recursos | Location |
    |---|---|
    | kafka-primary-rg | Centro de EE. UU. |
    | kafka-secondary-rg | Centro-Norte de EE. UU |

1. Cree una nueva red virtual, **kafka-primary-vnet**, en **kafka-primary-rg**. Deje los valores predeterminados.
1. Cree una nueva red virtual, **kafka-secondary-vnet**, en **kafka-secondary-rg** también con la configuración predeterminada.

1. Cree dos nuevos clústeres de Kafka:

    | Nombre del clúster | Grupo de recursos | Virtual Network | Cuenta de almacenamiento |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Cree los emparejamientos de las redes virtuales. Este paso creará dos emparejamientos: uno de **kafka-primary-vnet** a **kafka-secondary-vnet** y otro de **kafka-secondary-vnet** a **kafka-primary-vnet**.
    1. Seleccione la red virtual **kafka-primary-vnet**.
    1. En **Configuración**, seleccione **Emparejamiento**.
    1. Seleccione **Agregar**.
    1. En la pantalla **Agregar emparejamiento**, escriba los datos tal y como aparecen en la captura de pantalla siguiente.

        :::image type="content" source="./media/apache-kafka-mirroring/hdi-add-vnet-peering.png" alt-text="Agregar emparejamiento de red virtual de HDInsight Kafka" border="true":::

### <a name="configure-ip-advertising"></a>Configuración del anuncio de direcciones IP

La configuración del anuncio de direcciones IP permite al cliente conectarse mediante direcciones IP de agente en lugar de nombres de dominio.

1. Vaya al panel de Ambari del clúster principal: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
1. Seleccione **Services** >  (Servicios) **Kafka**. Seleccione la pestaña **Configs** (Configuraciones).
1. Agregue las siguientes líneas de configuración en la sección **kafka-env template** que encontrará en la parte inferior. Seleccione **Guardar**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Escriba una nota en la pantalla **Save Configuration** (Guardar configuración) y haga clic en **Save** (Guardar).
1. Si aparece un mensaje con una advertencia de configuración, haga clic en **Proceed Anyway** (Continuar de todos modos).
1. En **Save Configuration Changes** (Guardar cambios de configuración), haga clic en **Ok** (Aceptar).
1. Seleccione **Restart** (Reiniciar) > **Restart All Affected** (Reiniciar todos los elementos afectados) en la notificación **Restart Required** (Es necesario reiniciar). Seleccione **Confirm Restart All** (Confirmar reinicio de todo).

    :::image type="content" source="./media/apache-kafka-mirroring/ambari-restart-notification.png" alt-text="Apache Ambari: reinicar todos los afectados" border="true":::

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Configure Kafka para que escuche en todas las interfaces de red.
    
1. Permanezca en la pestaña **Configs** (Configuraciones) de **Services** (Servicios) > **Kafka**. En la sección **Kafka Broker** (Agente de Kafka), establezca la propiedad **listeners** en `PLAINTEXT://0.0.0.0:9092`.
1. Seleccione **Guardar**.
1. Seleccione **Restart** (Reiniciar) y **Confirm Restart All** (Confirmar reinicio de todo).

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Registre las direcciones IP de los agentes y las direcciones Zookeeper del clúster principal.

1. Seleccione **Hosts** en el panel de Ambari.
1. Tome nota de las direcciones IP de los agentes y los nodos de Zookeeper. El nombre del host de los nodos de los agentes comienza por **wn**, mientras que el nombre del host de los nodos de Zookeeper comienza por **zk**.

    :::image type="content" source="./media/apache-kafka-mirroring/view-node-ip-addresses2.png" alt-text="Ver direcciones IP de nodo en Apache Ambari" border="true":::

1. Repita los tres pasos anteriores con el segundo clúster, **kafka-secondary-cluster**: configure la publicidad basada en IP, establezca los agentes de escucha y tome nota de las direcciones IP de Zookeeper y el agente.

## <a name="create-topics"></a>Creación de temas

1. Conéctese al clúster **principal** con SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Reemplace **sshuser** por el nombre de usuario de SSH que usó al crear el clúster. Sustituya **SECONDARYCLUSTER** por el nombre base que se usó al crear el clúster.

    Para más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Use el comando siguiente para crear una variable con los hosts de Apache Zookeeper para el clúster principal. Las cadenas como `ZOOKEEPER_IP_ADDRESS1` deben reemplazarse por las direcciones IP reales que anotó anteriormente; por ejemplo, `10.23.0.11` y `10.23.0.7`. Si utiliza la resolución FQDN con un servidor DNS personalizado, siga [estos pasos](apache-kafka-get-started.md#getkafkainfo) para obtener los nombres de agente y de Zookeeper:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Para crear un tema llamado `testtopic`, use el comando siguiente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Use el siguiente comando para comprobar que se creó el tema:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    La respuesta contiene `testtopic`.

1. Use lo siguiente para ver la información del host de Zookeeper de este clúster (el **principal**):

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Esto devuelve información similar al texto siguiente:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Guarde esta información. Se usa en la siguiente sección.

## <a name="configure-mirroring"></a>Configuración del reflejo

1. Conéctese al clúster **secundario** mediante otra sesión SSH:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Reemplace **sshuser** por el nombre de usuario de SSH que usó al crear el clúster. Sustituya **SECONDARYCLUSTER** por el nombre de usuario que usó al crear el clúster.

    Para más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Para configurar la comunicación con el clúster **principal**, se utiliza un archivo `consumer.properties`. Para crear el archivo, use el comando siguiente:

    ```bash
    nano consumer.properties
    ```

    Use el texto siguiente como contenido del archivo `consumer.properties`:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Sustituya **PRIMARY_ZKHOSTS** por las direcciones IP de Zookeeper del clúster **principal**.

    Este archivo contiene la información del consumidor que se va a usar cuando se lea desde el clúster Kafka principal. Para más información sobre la configuración de los consumidores, consulte [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configuraciones de consumidor) en kafka.apache.org.

    Para guardar el archivo, presione **Ctr+X**, luego, **Y** y **Entrar**.

1. Antes de configurar el productor que se comunica con el clúster secundario, configure una variable para las direcciones IP del agente del clúster **secundario**. Utilice los comandos siguientes para crear esta variable:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    La información devuelta por el comando `echo $SECONDARY_BROKERHOSTS` debe ser similar a la siguiente:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Para establecer comunicación con el **clúster secundario**, se utiliza un archivo `producer.properties`. Para crear el archivo, use el comando siguiente:

    ```bash
    nano producer.properties
    ```

    Use el texto siguiente como contenido del archivo `producer.properties`:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Sustituya **SECONDARY_BROKERHOSTS** por las direcciones IP de los agentes que utilizó en el paso anterior.

    Para más información sobre la configuración del productor, consulte [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configuraciones de productor) en kafka.apache.org.

1. Use los siguientes comandos para crear una variable de entorno con las direcciones IP de los hosts de Zookeeper del clúster secundario:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. La configuración predeterminada para Kafka en HDInsight no permite la creación automática de temas. Debe usar una de las siguientes opciones antes de iniciar el proceso de creación de reflejo:

    * **Create the topics on the destination cluster** (Crear los temas en el clúster de destino): esta opción también le permite establecer el número de particiones y el factor de replicación.

        Puede crear temas con antelación mediante el comando siguiente:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Reemplace `testtopic` por el nombre del tema que se va a crear.

    * **Configure the cluster for automatic topic creation** (Configurar el clúster para la creación automática de temas): esta opción permite que MirrorMaker cree temas automáticamente, pero puede que los cree con un número de particiones o un factor de replicación diferentes a los del tema principal.

        Para configurar el clúster secundario de forma que cree automáticamente los temas, siga estos pasos:

        1. Vaya al panel de Ambari del clúster secundario: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Haga clic en **Services** (Servicios) > **Kafka**. Seleccione la pestaña **Configs** (Configuraciones).
        1. En el campo __Filtro__, escriba un valor de `auto.create`. La lista de propiedades se filtra y se muestra el valor `auto.create.topics.enable`.
        1. Cambie el valor de `auto.create.topics.enable` a true y, a continuación, seleccione __Guardar__. Agregue una nota y, a continuación, seleccione de nuevo __Guardar__.
        1. Seleccione el servicio __Kafka__, seleccione __Reiniciar__ y luego seleccione __Restart all affected__ (Reiniciar todos los afectados). Cuando se le solicite, seleccione __Confirm Restart All__ (Confirmar reiniciar todo).

        :::image type="content" source="./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png" alt-text="Habilitar la creación automática de temas en Kafka" border="true":::

## <a name="start-mirrormaker"></a>Inicio de MirrorMaker

1. En la conexión SSH con el clúster **secundario**, use el siguiente comando para iniciar el proceso de MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parámetros que se utilizan en este ejemplo:

    |Parámetro |Descripción |
    |---|---|
    |--consumer.config|especifica el archivo que contiene las propiedades de consumidor. Estas propiedades se utilizan para crear un consumidor que lea los datos desde el clúster de Kafka *principal*.|
    |--producer.config|especifica el archivo que contiene las propiedades de productor. Estas propiedades se utilizan para crear un productor que escriba en el clúster de Kafka *secundario*.|
    |--whitelist|lista de temas del clúster principal que MirrorMaker replica en el secundario.|
    |--num.streams|número de subprocesos de consumidor para crear.|

    Ahora, el consumidor del nodo secundario espera a recibir mensajes.

2. En la conexión SSH con el clúster **principal**, use el siguiente comando para iniciar un productor y enviar mensajes al tema:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Cuando llegue a una línea en blanco con un cursor, escriba algunos mensajes de texto. Los mensajes se envían al tema del clúster **principal**. Cuando haya terminado, use **Ctrl + C** para finalizar el proceso de productor.

3. En la conexión SSH con el clúster **secundario**, use **Ctrl + C** para iniciar el proceso de MirrorMaker. El proceso puede tardar varios segundos en finalizar. Para comprobar que los mensajes se han replicado en el clúster secundario, use el siguiente comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    La lista de temas ahora incluye `testtopic`, que se crea cuando MirrorMaster refleja el tema del clúster principal en el secundario. Los mensajes recuperados del tema son los mismos que los que se especificaron en el clúster principal.

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../includes/hdinsight-delete-cluster-warning.md)]

Al seguir los pasos de este documento, se crearon clústeres en diferentes grupos de recursos de Azure. Para eliminar todos los recursos que se crearon, puede eliminar los dos grupos de recursos: **kafka-primary-rg** y **kafka-secondary_rg**. Al eliminar los grupos de recursos, se eliminan todos los recursos que se crearon al seguir el procedimiento descrito en este documento, incluidos los clústeres, las redes virtuales y las cuentas de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a utilizar [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para crear una réplica de un clúster de [Apache Kafka](https://kafka.apache.org/). Utilice los vínculos siguientes para conocer otras formas de trabajar con Kafka:

* [Documentación de Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) en cwiki.apache.org.
* [Procedimientos recomendados de Kafka Mirror Maker](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Introducción a Apache Kafka en HDInsight](apache-kafka-get-started.md)
* [Uso de Apache Spark con Apache Kafka en HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Conexión a Apache Kafka a través de una instancia de Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
