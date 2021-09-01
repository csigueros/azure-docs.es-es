---
title: 'Uso de Apache Hadoop Hive con Curl en HDInsight: Azure'
description: Aprenda a enviar remotamente trabajos de Apache Pig a Azure HDInsight mediante Curl.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: a2e0cd1477f5fd7931093b14dced433e71c76236
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "122652896"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Ejecución de consultas de Apache Hive con Apache Hadoop en HDInsight mediante REST

[!INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Obtenga información acerca de cómo usar la API REST de WebHCat para ejecutar consultas de Apache Hive con Apache Hadoop en un clúster de Azure HDInsight.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Apache Hadoop en HDInsight. Consulte [Introducción a HDInsight en Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Un cliente REST. Este documento usa [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) en Windows PowerShell y [Curl](https://curl.haxx.se/) en [Bash](/windows/wsl/install-win10).

* Si usa Bash, también necesitará jq, un procesador JSON de línea de comandos.  Vea [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>URI base para API REST

El identificador uniforme de recursos (URI) base de la API REST en HDInsight es `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, donde `CLUSTERNAME` es el nombre del clúster.  Los nombres de clúster en los URI **distinguen mayúsculas de minúsculas**.  Si bien el nombre del clúster en la parte del nombre de dominio completo (FQDN) del URI (`CLUSTERNAME.azurehdinsight.net`) no distingue entre mayúsculas y minúsculas, otras apariciones en el identificador URI sí lo hacen.

## <a name="authentication"></a>Authentication

Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. La API de REST se protege con la [autenticación básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Para garantizar que las credenciales se envían de manera segura al servidor, siempre debe crear solicitudes usando HTTP segura (HTTPS).

### <a name="setup-preserve-credentials"></a>Configuración (conservar las credenciales)

Conserve las credenciales para evitar volver a escribirlas en cada ejemplo.  El nombre del clúster se conservará en un paso independiente.

**A. Bash**  
Edite el siguiente script, reemplazando `PASSWORD` por su contraseña real.  Luego, escriba el comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Ejecute el código siguiente y escriba sus credenciales en la ventana emergente:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificación del nombre del clúster con las mayúsculas y minúsculas correctas

Las mayúsculas y minúsculas reales del nombre del clúster pueden no ser como cabría esperar, dependen de la forma en que se haya creado el clúster.  Estos pasos mostrarán las mayúsculas y minúsculas reales y después las almacenarán en una variable para todos los ejemplos posteriores.

Edite los siguientes scripts para reemplazar `CLUSTERNAME` por su nombre de clúster. Luego, escriba el comando. (El nombre del clúster para el nombre de dominio completo no distingue mayúsculas de minúsculas).

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Ejecución de una consulta de Hive

1. Utilice uno de los siguientes comandos para comprobar que puede conectarse al clúster de HDInsight:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Debe recibir una respuesta similar al texto siguiente:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Los parámetros que se utilizan en este comando son los siguientes:

    * `-u`: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * `-G`: indica que esta es una solicitud de operación GET.

1. El principio de la dirección URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, es el mismo para todas las solicitudes. La ruta de acceso, `/status`, indica que la solicitud debe devolver un estado de WebHCat (también conocido como Templeton) al servidor. También puede solicitar la versión de Hive con el siguiente comando:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Esta solicitud devuelve una respuesta similar al texto siguiente:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Use lo siguiente para crear una tabla llamada **log4jLogs**:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Esta solicitud utiliza el método POST, que envía los datos como parte de la solicitud a la API de REST. Los siguientes valores de datos se envían con la solicitud:

     * `user.name`: el usuario que ejecuta el comando.
     * `execute`: las instrucciones HiveQL para ejecutar.
     * `statusdir`: el directorio donde se escribe el estado de este trabajo.

   Estas instrucciones realizan las acciones siguientes:

   * `DROP TABLE`: si la tabla ya existe, se elimina.
   * `CREATE EXTERNAL TABLE`: crea una nueva tabla "externa" en Hive. Las tablas externas solo almacenan la definición de tabla en Hive. Los datos permanecen en la ubicación original.

     > [!NOTE]  
     > Las tablas externas se deben utilizar cuando se espera que un origen externo actualice los datos subyacentes. Por ejemplo, un proceso de carga de datos automatizado u otra operación de MapReduce.
     >
     > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

   * `ROW FORMAT`: indica el formato de los datos. Los campos de cada registro se separan mediante un espacio.
   * `STORED AS TEXTFILE LOCATION`: indica dónde se almacenan los datos (el directorio example/data) y que se almacenan como texto.
   * `SELECT`: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]** . Esta instrucción devuelve un valor de **3** porque hay tres filas que contienen este valor.

     > [!NOTE]  
     > Observe que los espacios entre las instrucciones HiveQL se reemplazan por el carácter `+` cuando se usa con Curl. Los valores entre comillas que contengan un espacio, como el delimitador, no se reemplazarán por `+`.

      Este comando devuelve un identificador de trabajo que se pueda usar para comprobar el estado del trabajo.

1. Para revisar el estado del trabajo, use el siguiente comando:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Si se ha completado el trabajo, el estado es **SUCCEEDED**.

1. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde Azure Blob Storage. El parámetro `statusdir` pasado con la consulta contiene la ubicación del archivo de salida; en este caso, `/example/rest`. Esta dirección almacena la salida en el directorio `example/curl` del almacenamiento predeterminado del clúster.

    Puede enumerar y descargar estos archivos mediante la [CLI de Azure](/cli/azure/install-azure-cli). Para más información acerca del uso de la CLI de Azure con Azure Storage, consulte el documento [Uso de la CLI de Azure con Azure Storage](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Apache Hive con Apache Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de MapReduce con Apache Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener más información sobre la API de REST usada en este documento, consulte el documento [WebHCat reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) (Referencia de WebHCat).