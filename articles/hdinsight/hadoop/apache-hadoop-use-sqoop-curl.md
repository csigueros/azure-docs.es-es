---
title: Uso de Curl para exportar datos con Apache Sqoop en Azure HDInsight
description: Sepa cómo enviar remotamente trabajos de Apache Sqoop a Azure HDInsight mediante Curl.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: f2a097e18f6e1f285335a89574efd720fd251e92
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "122653346"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Ejecución de trabajos de Apache Sqoop en HDInsight con Curl

[!INCLUDE [sqoop-selector](../includes/hdinsight-selector-use-sqoop.md)]

En este documento aprenderá a utilizar Curl para ejecutar trabajos de Apache Sqoop en un clúster de Apache Hadoop en HDInsight. En este artículo se muestra cómo exportar datos desde Azure Storage y cómo importarlos en una base de datos de SQL Server mediante Curl. Este artículo es una continuación de [Uso de Apache Sqoop con Hadoop en HDInsight](./hdinsight-use-sqoop.md).

Curl sirve para demostrar cómo se puede interactuar con HDInsight usando solicitudes HTTP sin procesar para ejecutar, supervisar y recuperar los resultados de los trabajos de Sqoop. Esto funciona mediante la API de REST de WebHCat (conocida anteriormente como Templeton) proporcionada por el clúster de HDInsight.

## <a name="prerequisites"></a>Requisitos previos

* Finalización de la [configuración de un entorno de prueba](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [Uso de Apache Sqoop con Hadoop en HDInsight](./hdinsight-use-sqoop.md).

* Un cliente para consultar Azure SQL Database. Considere el uso de [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) o [Visual Studio Code](../../azure-sql/database/connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl es una herramienta que se usa para transferir datos desde o hacia un clúster de HDInsight.

* [jq](https://stedolan.github.io/jq/). La utilidad jq se usa para procesar los datos JSON que devuelven las solicitudes de REST.

* Familiaridad con Sqoop. Para más información, consulte la [guía de usuario de Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Envío de trabajos de Apache Sqoop mediante Curl

Use Curl para exportar datos mediante trabajos de Apache Sqoop desde Azure Storage a SQL Server.

> [!NOTE]  
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se usa para enviar las solicitudes al servidor.

Cuando se usen los comandos de esta sección, reemplace `USERNAME` por el usuario para autenticación en el clúster y `PASSWORD` por la contraseña de la cuenta de usuario. Reemplace `CLUSTERNAME` por el nombre del clúster.

La API de REST se protege con la [autenticación básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.

1. Para facilitar su uso, establezca las variables siguientes. Este ejemplo se basa en un entorno de Windows, revise según sea necesario para su entorno.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Debería recibir una respuesta similar a la siguiente:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Para enviar el trabajo de sqoop, use lo siguiente:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Los parámetros que se utilizan en este comando son los siguientes:

   * **-d**: como `-G` no se usa, la solicitud establece como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

       * **user.name** : el usuario que ejecuta el comando.

       * **command** : el comando de Sqoop para ejecutar.

       * **statusdir** : el directorio donde se escribirá el estado de este trabajo.

     Este comando devolverá un identificador de trabajo que se pueda usar para comprobar el estado del trabajo.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Para revisar el estado del trabajo, use el siguiente comando. Reemplace `JOBID` por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fue `{"id":"job_1415651640909_0026"}`, entonces `JOBID` debería ser `job_1415651640909_0026`. Revise la ubicación de `jq` según sea necesario.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Si se completó el trabajo, el estado será **SUCCEEDED**.

   > [!NOTE]  
   > Esta solicitud de Curl devuelve un documento de notación de objetos JavaScript (JSON) con información acerca del trabajo; jq se usa para recuperar solo el valor de estado.

1. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde Azure Blob Storage. El parámetro `statusdir` pasado con la consulta contiene la ubicación del archivo de salida; en este caso, `wasb:///example/data/sqoop/curl`. Esta dirección almacena la salida del trabajo en el directorio `example/data/sqoop/curl` en el contenedor de almacenamiento predeterminado que su clúster de HDInsight usa.

    Puede usar Azure Portal para tener acceso a los blobs "stderr" y "stdout".

1. Para comprobar que los datos se han exportado, use las siguientes consultas en el cliente SQL para ver los datos exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitaciones

* Exportación masiva: con HDInsight basado en Linux, el conector Sqoop que se utiliza para exportar datos a Microsoft SQL Server o Azure SQL Database no es compatible actualmente con las inserciones masivas.
* Procesamiento por lotes: con HDInsight basado en Linux, cuando se usa `-batch` al realizar inserciones, Sqoop realizará varias inserciones en lugar de procesar por lotes las operaciones de inserción.

## <a name="summary"></a>Resumen

Tal como se ha mostrado en este documento, puede usar una solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Sqoop en su clúster de HDInsight.

Para más información sobre la interfaz REST utilizada en este artículo, consulte la <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guía de Apache Sqoop REST API</a>.

## <a name="next-steps"></a>Pasos siguientes

[Uso de Apache Sqoop con Apache Hadoop en HDInsight](hdinsight-use-sqoop.md)

Si quiere leer más artículos de HDInsight en los que se incluya Curl, consulte:

* [Creación de clústeres de Apache Hadoop mediante la API REST de Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Ejecución de consultas de Apache Hive con Apache Hadoop en HDInsight mediante REST](apache-hadoop-use-hive-curl.md)
* [Ejecución de trabajos de MapReduce con Apache Hadoop en HDInsight con REST](apache-hadoop-use-mapreduce-curl.md)
