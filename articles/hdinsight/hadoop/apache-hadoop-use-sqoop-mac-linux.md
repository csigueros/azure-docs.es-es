---
title: 'Apache Sqoop con Apache Hadoop: Azure HDInsight'
description: Aprenda a usar Apache Sqoop para realizar importaciones y exportaciones entre Apache Hadoop en HDInsight y Azure SQL Database.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 8e7fd6c476fd56dcece162e34d5e6bd1ba33a34f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "122653173"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Use Apache Sqoop para importar y exportar datos entre Apache Hadoop en HDInsight y Azure SQL Database.

[!INCLUDE [sqoop-selector](../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar Apache Sqoop para realizar importaciones y exportaciones entre un clúster de Apache Hadoop en Azure HDInsight y Azure SQL Database o Microsoft SQL Server. En los pasos descritos en este documento se usa el comando `sqoop` directamente desde el nodo principal del clúster de Hadoop. Usaremos SSH para conectarnos al nodo principal y ejecutar los comandos de este documento. Este artículo es una continuación de [Uso de Apache Sqoop con Hadoop en HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Requisitos previos

* Finalización de la [configuración de un entorno de prueba](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [Uso de Apache Sqoop con Hadoop en HDInsight](./hdinsight-use-sqoop.md).

* Un cliente SSH. Para más información, consulte [Conexión a través de SSH con HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Familiaridad con Sqoop. Para más información, consulte la [guía de usuario de Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Configurar

1. Use el [comando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) para conectarse al clúster. Modifique el comando siguiente: reemplace CLUSTERNAME por el nombre del clúster y, luego, escriba el comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para facilitar su uso, establezca variables. Reemplace `PASSWORD`, `MYSQLSERVER` y `MYDATABASE` por los valores pertinentes y, luego, escriba los siguientes comandos:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Exportación de Sqoop

De Hive a SQL.

1. Para comprobar que Sqoop puede ver la base de datos, escriba el comando siguiente en la conexión SSH abierta. Este comando devuelve una lista de las bases de datos.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Escriba el siguiente comando para ver una lista de tablas de la base de datos especificada:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Para exportar datos de la tabla `hivesampletable` de Hive a la tabla `mobiledata` en la base de datos, escriba el comando siguiente en la conexión SSH abierta:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Para comprobar que los datos se han exportado, use las siguientes consultas desde la conexión SSH para ver los datos exportados:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Importación de Sqoop

De SQL a Azure Storage.

1. Escriba el comando siguiente en la conexión SSH abierta para importar datos desde la tabla `mobiledata` en SQL al directorio `wasbs:///tutorials/usesqoop/importeddata` en HDInsight. Los campos de los datos se separan mediante un carácter de tabulación y las líneas terminan con un carácter de nueva línea.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Como alternativa, también puede especificar una tabla de Hive:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Una vez que se complete la importación, escriba el comando siguiente en la conexión SSH abierta para mostrar los datos del directorio nuevo:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Use [beeline](./apache-hadoop-use-hive-beeline.md) para comprobar que la tabla se ha creado en Hive.

    1. Conectar

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Ejecute cada una de las siguientes consultas una en una y revise la salida:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Salga de beeline con `!exit`.

## <a name="limitations"></a>Limitaciones

* Exportación masiva: con HDInsight basado en Linux, el conector Sqoop que se usa para exportar datos a SQL no admite inserciones masivas.

* Procesamiento por lotes: con HDInsight basado en Linux, cuando se usa `-batch` al realizar inserciones, Sqoop realiza varias inserciones en lugar de procesar por lotes las operaciones de inserción.

## <a name="important-considerations"></a>Consideraciones importantes

* Tanto HDInsight como SQL Server deben estar en la misma red Azure Virtual Network.

    Para obtener un ejemplo, vea el documento [Conexión de HDInsight a la red local](./../connect-on-premises-network.md).

    Para más información sobre cómo usar HDInsight con redes Azure Virtual Network, vea el documento [Extensión de las funcionalidades de HDInsight con Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md). Para más información sobre Azure Virtual Network, vea el documento [Información general sobre Virtual Network](../../virtual-network/virtual-networks-overview.md).

* SQL Server estar configurado para permitir la autenticación SQL. Para más información, vea el documento [Choose an Authentication Mode (Elegir un modo de autenticación)](/sql/relational-databases/security/choose-an-authentication-mode).

* Es posible que tenga que configurar SQL Server para aceptar conexiones remotas. Para más información, vea el documento [How to troubleshoot connecting to the SQL Server database engine (Solución de problemas de conexión al motor de base de datos de SQL Server)](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Pasos siguientes

Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

* [Uso de Apache Oozie con HDInsight](../hdinsight-use-oozie-linux-mac.md): use la acción Sqoop en un flujo de trabajo de Oozie.
* [Análisis de la información de retraso de vuelos con HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): use Interactive Query para analizar los datos de retraso de vuelos y luego use Sqoop para exportar los datos a una base de datos en Azure.
* [Carga de datos en HDInsight](../hdinsight-upload-data.md): busque otros métodos para cargar datos en HDInsight o Azure Blob Storage.