---
title: Migración en vivo a Azure Managed Instance for Apache Cassandra mediante Apache Spark y un proxy de doble escritura
description: Vea cómo migrar a Azure Managed Instance for Apache Cassandra usando Apache Spark y un proxy de doble escritura.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 03862b42fb181adcf1a6c4edbed66ce61ee49e73
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426137"
---
# <a name="live-migration-to-azure-managed-instance-for-apache-cassandra-by-using-a-dual-write-proxy"></a>Migración en vivo a Azure Managed Instance for Apache Cassandra mediante un proxy de doble escritura

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Siempre que sea posible, se recomienda usar la funcionalidad nativa de Apache Cassandra para migrar datos de un clúster a Azure Managed Instance for Apache Cassandra mediante la configuración de un [clúster híbrido](configure-hybrid-cluster.md). Esta funcionalidad utiliza el protocolo gossip de Apache Cassandra para replicar datos de un centro de datos de origen en un nuevo centro de datos de instancia administrada de forma fluida. Sin embargo, puede haber algunos escenarios en los que la versión de la base de datos de origen no sea compatible, o donde una configuración de clúster híbrido no sea factible. 

En este tutorial se describe cómo migrar datos en vivo a Azure Managed Instance for Apache Cassandra mediante un [proxy de doble escritura](https://github.com/Azure-Samples/cassandra-proxy) y Apache Spark. Las ventajas de este enfoque son:

- **Cambios mínimos en las aplicaciones**. El proxy puede aceptar conexiones desde el código de las aplicaciones con pocos o ningún cambio de configuración. Enrutará todas las solicitudes a la base de datos de origen y, de forma asincrónica, enrutará las operaciones de escritura hacia un destino secundario. 
- **Dependencia del protocolo de transferencia del cliente**. Puesto que este enfoque no depende de recursos de back-end ni protocolos internos, se puede usar con cualquier sistema Cassandra de origen o destino que implemente el protocolo de transferencia de Apache Cassandra.

En la siguiente imagen, se ilustra este enfoque.


:::image type="content" source="./media/migration/live-migration.gif" alt-text="Animación que muestra la migración de datos en vivo a Azure Managed Instance for Apache Cassandra." border="false":::

## <a name="prerequisites"></a>Requisitos previos

* Aprovisione una instancia de Azure Managed Instance for Apache Cassandra desde [Azure Portal](create-cluster-portal.md) o la [CLI de Azure](create-cluster-cli.md). Asegúrese de que puede [conectarse al clúster con CQLSH](./create-cluster-portal.md#connecting-to-your-cluster).

* [Aprovisione una cuenta de Azure Databricks en la red virtual de la instancia de Cassandra administrada](deploy-cluster-databricks.md). Asegúrese de que la cuenta tenga acceso de red al clúster de Cassandra de origen. Crearemos un clúster de Spark en esta cuenta para la carga de datos históricos.

* Asegúrese de haber migrado ya el espacio de claves o el esquema de tabla de la base de datos Cassandra de origen a la base de datos de la instancia de Cassandra administrada de destino.


## <a name="provision-a-spark-cluster"></a>Aprovisionamiento de un clúster de Spark

Se recomienda seleccionar la versión 7.5 de Azure Databricks Runtime, que es compatible con Spark 3.0.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Captura de pantalla que muestra la búsqueda de la versión de Azure Databricks Runtime.":::

## <a name="add-spark-dependencies"></a>Adición de dependencias de Spark

Tiene que agregar la biblioteca de conectores de Cassandra de Apache Spark a su clúster para conectarse a los puntos de conexión nativos y de Cassandra de Azure Cosmos DB. En el clúster, seleccione **Libraries** > **Install New** > **Maven** (Bibliotecas > Instalar nueva > Maven) y, después, agregue `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` en las coordenadas de Maven.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Captura de pantalla que muestra la búsqueda de paquetes Maven en Azure Databricks.":::

Seleccione **Install** (Instalar) y asegúrese de reiniciar el clúster cuando se complete la instalación.

> [!NOTE]
> Asegúrese de reiniciar el clúster de Azure Databricks después de instalar la biblioteca del conector de Cassandra.

## <a name="install-the-dual-write-proxy"></a>Instalación del proxy de doble escritura

Para obtener un rendimiento óptimo durante las operaciones de doble escritura, se recomienda instalar el proxy en todos los nodos del clúster de Cassandra de origen.

```bash
#assuming you do not have git already installed
sudo apt-get install git 

#assuming you do not have maven already installed
sudo apt install maven

#clone repo for dual-write proxy
git clone https://github.com/Azure-Samples/cassandra-proxy.git

#change directory
cd cassandra-proxy

#compile the proxy
mvn package
```

## <a name="start-the-dual-write-proxy"></a>Inicio del proxy de doble escritura

Se recomienda instalar el proxy en todos los nodos del clúster de Cassandra de origen. Como mínimo, ejecute el siguiente comando para iniciar el proxy en cada nodo. Reemplace `<target-server>` por una dirección IP o de servidor de uno de los nodos del clúster de destino. Reemplace `<path to JKS file>` por la ruta de acceso a un archivo .jks local, y `<keystore password>`, por la contraseña correspondiente.  

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password>
```

Al iniciar el proxy de esta manera, se da por hecho que se cumple lo siguiente:

- Los puntos de conexión de origen y destino tienen el mismo nombre de usuario y la misma contraseña.
- Los puntos de conexión de origen y destino implementan el protocolo SSL (Capa de sockets seguros).

Si los puntos de conexión de origen y destino no pueden cumplir estos criterios, siga leyendo para conocer más opciones de configuración. 

### <a name="configure-ssl"></a>Configuración de SSL

Para el protocolo SSL, puede implementar un almacén de claves que ya utilice (por ejemplo, el que usa el clúster de origen) o crear un certificado autofirmado mediante `keytool`:

```bash
keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
```
También puede deshabilitar SSL para los puntos de conexión de origen o destino si no implementan este protocolo. Use las marcas `--disable-source-tls` o `--disable-target-tls`:

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password> --disable-source-tls true  --disable-target-tls true 
```

> [!NOTE]
> Asegúrese de que la aplicación cliente usa el mismo almacén de claves y la misma contraseña que el proxy de doble escritura cuando cree conexiones SSL a la base de datos a través del proxy.


### <a name="configure-the-credentials-and-port"></a>Configuración de las credenciales y el puerto

De forma predeterminada, se pasan las credenciales de origen de la aplicación cliente. El proxy usará las credenciales para establecer conexiones con los clústeres de origen y de destino. Como hemos mencionado anteriormente, en este proceso se da por supuesto que las credenciales del origen y el destino son las mismas. Si es necesario, puede especificar aparte un nombre de usuario y una contraseña diferentes para el punto de conexión de Cassandra de destino al iniciar el proxy:

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

Si no se especifican, los puertos de origen y destino predeterminados serán el 9042. Si el punto de conexión de Cassandra de destino o de origen se ejecuta en un puerto diferente, puede usar `--source-port` o `--target-port` para especificar otro número de puerto: 

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

### <a name="deploy-the-proxy-remotely"></a>Implementación del proxy de forma remota

Puede haber circunstancias en las que no quiera instalar el proxy en los propios nodos del clúster y prefiera instalarlo en una máquina aparte. En este caso, debe especificar la dirección IP de `<source-server>`:

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar <source-server> <destination-server>
```

> [!NOTE]
> Si no instala y ejecuta el proxy en todos los nodos de un clúster nativo de Apache Cassandra, afectará al rendimiento de la aplicación. El controlador de cliente no podrá abrir conexiones con todos los nodos del clúster. 

### <a name="allow-zero-application-code-changes"></a>Cero cambios en el código de las aplicaciones

De forma predeterminada, el proxy escucha en el puerto 29042. El código de las aplicaciones debe cambiarse para que apunte a este puerto. Sin embargo, puede cambiar el puerto en el que el proxy escucha. Puede hacerlo si desea evitar cambios de código en las aplicaciones de las siguientes formas:

- Haciendo que el servidor de Cassandra de origen se ejecute en otro puerto.
- Haciendo que el proxy se ejecute en el puerto estándar 9042 de Cassandra.

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042
```

> [!NOTE]
> La instalación del proxy en los nodos del clúster no requiere que se reinicien los nodos. Sin embargo, si tiene muchos clientes de aplicaciones y prefiere que el proxy se ejecute en el puerto estándar 9042 de Cassandra para evitar cambios de código en las aplicaciones, debe cambiar el [puerto predeterminado de Apache Cassandra](https://cassandra.apache.org/doc/latest/cassandra/faq/#what-ports-does-cassandra-use). Después, debe reiniciar los nodos del clúster y configurar el puerto de origen para que sea el nuevo puerto que definió para el clúster de Cassandra de origen. 
>
> En el ejemplo siguiente, cambiamos el clúster de Cassandra de origen para que se ejecute en el puerto 3074 e iniciamos el clúster en el puerto 9042:
>
>```bash
>java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042 --source-port 3074
>``` 

### <a name="force-protocols"></a>Imposición de protocolos

El proxy tiene funcionalidad para imponer protocolos, lo que podría ser necesario si el punto de conexión de origen es más avanzado que el de destino o no se admite por otro motivo. En ese caso, puede especificar `--protocol-version` y `--cql-version` para exigir que el protocolo cumpla con el destino:

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --protocol-version 4 --cql-version 3.11
```

Una vez que se ejecute el proxy de doble escritura, deberá cambiar el puerto en el cliente de la aplicación y reiniciarlo; o bien cambie el puerto de Cassandra y reinicie el clúster si ha elegido ese método. A continuación, el proxy iniciará el reenvío de operaciones de escritura al punto de conexión de destino. Puede obtener información sobre [la supervisión y las métricas](https://github.com/Azure-Samples/cassandra-proxy#monitoring) disponibles en la herramienta de proxy. 


## <a name="run-the-historical-data-load"></a>Ejecución de la carga de datos históricos

Para cargar los datos, cree un cuaderno de Scala en la cuenta de Azure Databricks. Reemplace las configuraciones de Cassandra de origen y de destino por las credenciales correspondientes, así como los espacios de claves y las tablas de origen y destino. Agregue más variables para cada tabla según sea necesario en el ejemplo siguiente y ejecútelo. Cuando la aplicación empiece a enviar solicitudes al proxy de doble escritura, estará listo para migrar datos históricos. 

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val sourceCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val targetCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "1",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//set timestamp to ensure it is before read job starts
val timestamp: Long = System.currentTimeMillis / 1000

//Read from source Cassandra
val DFfromSourceCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(sourceCassandra)
  .load
  
//Write to target Cassandra
DFfromSourceCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(targetCassandra)
  .option("writetime", timestamp)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> En el ejemplo anterior de Scala, observará que `timestamp` se establece en la hora actual antes de leer todos los datos de la tabla de origen. Después, `writetime` se antedata con esta marca de tiempo. Esto garantiza que los registros que se escriben con la carga de datos históricos en el punto de conexión de destino no sobrescriban las actualizaciones que se incorporan con una marca de tiempo posterior desde el proxy de doble escritura mientras se leen los datos históricos.
>
> Si, por algún motivo, necesita conservar las marcas de tiempo *exactas*, debe seguir un enfoque de migración de datos históricos que mantenga las marcas de tiempo, como en [este ejemplo](https://github.com/Azure-Samples/cassandra-migrator). 

## <a name="validate-the-source-and-target"></a>Validación del origen y el destino

Una vez completada la carga de datos históricos, las bases de datos deben estar sincronizadas y listas para la transición. Sin embargo, se recomienda validar el origen y el destino para asegurarse de que los resultados de las solicitudes coincidan antes de la transición definitiva.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración de recursos de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure](manage-resources-cli.md)
