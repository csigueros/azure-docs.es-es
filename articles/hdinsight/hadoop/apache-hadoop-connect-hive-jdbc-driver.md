---
title: Consulta de Apache Hive mediante el controlador JDBC en Azure HDInsight
description: Use el controlador JDBC desde una aplicación Java para enviar consultas de Apache Hive a Hadoop en HDInsight. Conéctese mediante programación y desde el cliente de SQuirrel SQL.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 3e363d04fbfd0222cb49ffa9fa02b88d7fba2468
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300604"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Consulta de Apache Hive mediante el controlador JDBC en HDInsight

[!INCLUDE [ODBC-JDBC-selector](../includes/hdinsight-selector-odbc-jdbc.md)]

Aprenda a usar el controlador JDBC desde una aplicación de Java. Para enviar consultas de Apache Hive a Apache Hadoop en Azure HDInsight. La información de este documento muestra cómo conectarse mediante programación y desde el cliente de SQuirreL SQL.

Para obtener más información sobre la interfaz JDBC de Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Prerrequisitos

* Un clúster de Hadoop de HDInsight: Para crear uno, vea [Introducción a HDInsight de Azure](apache-hadoop-linux-tutorial-get-started.md). Asegúrese de que el servicio HiveServer2 está en ejecución.
* [Kit para desarrolladores de Java (JDK), versión 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) o posterior.
* [SQL SQuirreL](http://squirrel-sql.sourceforge.net/). SQuirreL es una aplicación de cliente JDBC.

## <a name="jdbc-connection-string"></a>Cadena de conexión JDBC

Las conexiones de JDBC a un clúster de HDInsight en Azure se realizan a través del puerto 443. El tráfico se protege mediante TLS/SSL. La puerta de enlace pública tras la que se encuentran los clústeres redirige el tráfico al puerto que HiveServer2 escucha. En la siguiente cadena de conexión puede verse el formato que se va a utilizar en HDInsight:

```http
    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2
```

Reemplace `CLUSTERNAME` por el nombre del clúster de HDInsight.

También puede obtener la conexión mediante **Ambari UI > Hive > Configs > Advanced** (Interfaz de usuario de Ambari > Hive > Configuraciones > Avanzadas).

:::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png" alt-text="Obtención de la cadena de conexión JDBC mediante Ambari" border="true":::

### <a name="host-name-in-connection-string"></a>Nombre de host en la cadena de conexión

El nombre de host "CLUSTERNAME.azurehdinsight.net" de la cadena de conexión es el mismo que el de la dirección URL del clúster. Puede obtenerlo en Azure Portal.

### <a name="port-in-connection-string"></a>Puerto de la cadena de conexión

Solo puede usar el **puerto 443** para conectarse al clúster desde algunos lugares fuera de la red virtual de Azure. HDInsight es un servicio administrado, lo que significa que todas las conexiones al clúster se administran a través de una puerta de enlace segura. No se puede conectar a HiveServer 2 directamente en los puertos 10001 o 10000. Estos puertos no tienen exposición al exterior.

## <a name="authentication"></a>Authentication

Al establecer la conexión, utilice el nombre y la contraseña de administrador del clúster de HDInsight para realizar la autenticación. En los clientes de JDBC como SQuirreL SQL, escriba el nombre y la contraseña de administrador en la configuración de cliente.

Desde una aplicación de Java, tiene que utilizar el nombre y la contraseña al establecer una conexión. Por ejemplo, el siguiente código Java abre una nueva conexión:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conexión con el cliente SQL SQuirreL

SQL SQuirreL es un cliente JDBC que puede utilizarse para ejecutar consultas de Hive de manera remota con el clúster de HDInsight. En los pasos siguientes se da por hecho que ya ha instalado SQuirreL SQL.

1. Cree un directorio para que contenga determinados archivos que se van a copiar desde el clúster.

2. En el siguiente script, reemplace `sshuser` por el nombre de la cuenta de usuario SSH del clúster.  Reemplace `CLUSTERNAME` por el nombre del clúster de HDInsight.  Desde una línea de comandos, cambie el directorio de trabajo por el que creó en el paso anterior y escriba el siguiente comando para copiar archivos desde el clúster de HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Inicie la aplicación SQL SQuirreL. A la izquierda de la ventana, seleccione **Drivers** (Controladores).

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png" alt-text="Pestaña de controladores a la izquierda de la ventana" border="true":::

4. En los iconos en la parte superior del cuadro de diálogo **Drivers** (Controladores), seleccione el icono de **+** para crear un nuevo controlador.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png" alt-text="Icono de controladores de la aplicación de SQL SQuirreL" border="true":::

5. En el cuadro de diálogo Add driver (agregar controlador), agregue la siguiente información:

    |Propiedad | Valor |
    |---|---|
    |Nombre|Hive|
    |Example URL (URL de ejemplo)|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Extra Class Path (Ruta de acceso de clase adicional):|use el botón **Add** (Agregar) para agregar todos los archivos .jar que se descargaron anteriormente.|
    |Class Name (Nombre de clase)|org.apache.hive.jdbc.HiveDriver|

   :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png" alt-text="cuadro de diálogo para agregar controlador con parámetros" border="true":::

   Seleccione **Aceptar** para guardar la configuración.

6. En el lado izquierdo de la ventana de SQL SQuirreL, seleccione **Aliases**. A continuación, seleccione el icono de **+** para crear un nuevo alias de conexión.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png" alt-text="&quot;Cuadro de diálogo para agregar nuevo alias de SQuirreL SQL&quot;" border="true":::

7. Use los siguientes valores en el cuadro de diálogo **Add Alias** (Agregar alias).

    |Propiedad |Valor |
    |---|---|
    |Nombre|Hive en HDInsight|
    |Controlador|Use la lista desplegable para seleccionar el controlador **Hive**.|
    |URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.|
    |Nombre de usuario|nombre de la cuenta de inicio de sesión del clúster de HDInsight. El valor predeterminado es **admin**.|
    |Contraseña|contraseña para la cuenta de inicio de sesión del clúster.|

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png" alt-text="cuadro de diálogo para agregar alias con parámetros" border="true":::

    > [!IMPORTANT]
    > Use el botón **Test** (Probar) para comprobar que la conexión funciona. Cuando aparezca el cuadro de diálogo **Connect to: Hive on HDInsight** (Conectarse a: Hive en HDInsight), haga clic en **Connect** (Conectar) para realizar la prueba. Si la prueba se realiza con éxito, verá un cuadro de diálogo **Connection successful** (Conexión correcta). Si se produce un error, consulte [Solución de problemas](#troubleshooting).

    Use el botón **OK** (Aceptar) situado en la parte inferior del cuadro de diálogo **Add Alias** (Agregar alias) para guardar el alias de conexión.

8. En la lista desplegable **Connect to** (Conectar a), en la parte superior de SQL SQuirreL, seleccione **Hive on HDInsight** (Hive en HDInsight). Cuando se le pida, seleccione **Connect** (Conectar).

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png" alt-text="cuadro de diálogo de conexión con parámetros" border="true":::

9. Una vez conectado, escriba la siguiente consulta en el cuadro de diálogo de consulta SQL y seleccione el icono **Run** (Ejecutar) (la persona que lo ejecuta). El área de resultados debe mostrar los resultados de la consulta.

    ```hiveql
    select * from hivesampletable limit 10;
    ```

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png" alt-text="cuadro de diálogo de consulta de SQL, incluidos los resultados" border="true":::

## <a name="connect-from-an-example-java-application"></a>Conexión desde una aplicación de Java de ejemplo

Hay un ejemplo del uso de un cliente de Java para consultar Hive en HDInsight disponible en [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Siga las instrucciones del repositorio para crear y ejecutar el ejemplo.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Error inesperado al intentar abrir una conexión SQL

**Síntomas**: si se conecta a un clúster de HDInsight con la versión 3.3 o posterior, es posible que reciba un mensaje donde se indique que se produjo un error inesperado. Se iniciará el seguimiento de la pila para este error con las siguientes líneas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: este error lo causa un archivo commons-codec.jar de una versión anterior que está incluido en SQuirreL.

**Solución:** para corregir este error, siga estos pasos:

1. Salga de SQuirreL y vaya al directorio donde está instalado SQuirreL en el sistema, por ejemplo, `C:\Program Files\squirrel-sql-4.0.0\lib`. En el directorio de SQuirreL, bajo el directorio `lib` , reemplace el archivo commons-codec.jar existente por el descargado desde el clúster de HDInsight.

1. Reinicie SQuirreL. El error ya no debería ocurrir al conectarse a Hive en HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Conexión desconectada por HDInsight

**Síntomas**: al intentar descargar una gran cantidad de datos (por ejemplo, varios GB) mediante JDBC u ODBC, HDInsight desconecta la conexión de forma inesperada durante la descarga.

**Causa**: este error se debe a la limitación en los nodos de puerta de enlace. Al obtener datos de JDBC u ODBC, todos los datos deben pasar por el nodo de puerta de enlace. Sin embargo, una puerta de enlace no está diseñada para descargar una gran cantidad de datos, por lo que podría cerrar la conexión si no puede administrar el tráfico.

**Solución:** evite el uso del controlador JDBC u ODBC para descargar grandes cantidades de datos. En su lugar, copie los datos directamente del almacenamiento de blobs.

## <a name="next-steps"></a>Pasos siguientes

Ahora que aprendió a usar JDBC para que funcione con Hive, utilice los siguientes vínculos para explorar otras formas de trabajar con Azure HDInsight.

* [Visualización de datos de Apache Hive con Microsoft Power BI en Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualización de datos de Interactive Query Hive con Power BI en Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Conexión de Excel a Hadoop en Azure HDInsight con Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conexión de Excel a Apache Hadoop con Power Query](apache-hadoop-connect-excel-power-query.md).
* [Uso de Apache Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Apache Pig con HDInsight](../use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)
