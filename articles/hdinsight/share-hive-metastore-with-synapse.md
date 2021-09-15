---
title: Compartir Metastore de Hive con un grupo de Spark de Azure Synapse
description: Obtenga información sobre cómo compartir el metastore de Hive externo de Azure HDInsight con el grupo de Spark de Azure Synapse.
keywords: external Hive metastore,share,Synapse
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/22/2020
ms.openlocfilehash: 69168ff4bd02800115560e2a91988289d0ca2d67
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440253"
---
# <a name="share-hive-metastore-with-synapse-spark-pool-preview"></a>Uso compartido de metastore de Hive con el grupo de Spark de Synapse (versión preliminar)

Azure Synapse Analytics permite que grupos de Apache Spark en la misma área de trabajo compartan un metastore compatible con HMS (Servicio de metastore de Hive) administrado como su catálogo. Cuando los quieren desean compartir objetos de catálogo con otros motores de cálculo fuera del área de trabajo, como clústeres de Hive y Spark de HDInsight, prefieren conectarse a un metastore de Hive externo. En este artículo, aprenderá a conectar Spark de Synapse a un metastore de Apache Hive externo. 

## <a name="supported-hive-metastore-versions"></a>Versiones admitidas de metastore de Hive

La característica funciona con Spark 2.4 y Spark 3.0. En la tabla siguiente se muestran las versiones compatibles del servicio de metastore de Hive (HMS) para cada versión de Spark.

|Versión de Spark|HMS 1.2.X|HMS 2.1.X|HMS 3.1.X|
|--|--|--|--|
|2.4|Sí|Sí|No|
|3|Sí|Sí|Sí|

> [!NOTE]
> Puede usar el metastore de Hive externo existente desde clústeres de HDInsight, tanto en clústeres 3.6 como 4.0. Consulte [Uso de repositorios de metadatos externos en Azure HDInsight](./hdinsight-use-external-metadata-stores.md).
Siga estos pasos para configurar un servicio vinculado al metastore externo de Hive y al almacenamiento de catálogo subyacente en el área de trabajo de Synapse y configure el grupo de Spark para que use el metastore externo de Hive vinculado.

## <a name="set-up-hive-metastore-linked-service"></a>Configuración del servicio vinculado de metastore de Hive

> [!NOTE]
> Solo Azure SQL Database se admite como metastore de Hive externo.
Siga estos pasos para configurar un servicio vinculado al metastore de Hive externo en el área de trabajo de Synapse.
1.  Abra Synapse Studio, vaya a **Administrar > Servicios vinculados** a la izquierda y haga clic en **Nuevo**  para crear un nuevo servicio vinculado.

    :::image type="content" source="./media/share-hive-metastore-with-synapse/set-up-hive-metastore-linked-service.png" alt-text="Configuración del servicio vinculado al metastore de Hive" border="true":::

2.  Elija **Azure SQL Database** y haga clic en **Continuar**.

3.  Proporcione el **Nombre** del servicio vinculado. Registre el nombre del servicio vinculado. Esta información se usará para configurar Spark en breve.

4.  Puede seleccionar el valor de Azure SQL Database para el metastore de Hive externo en la lista de suscripciones de Azure o escribir la información manualmente.

5.  Actualmente solo se admite la autenticación SQL como tipo de autenticación. Proporcione el **Nombre de usuario** y la **Contraseña** para configurar la conexión.

6.  **Prueba de conexión** para comprobar el nombre de usuario y la contraseña.

7.  Haga clic en **Crear** para crear el servicio vinculado. 

### <a name="test-connection-and-get-the-metastore-version-in-notebook"></a>Probar la conexión y obtener la versión del metastore en el cuaderno
Algunas opciones de configuración de reglas de seguridad de red pueden bloquear el acceso desde el grupo de Spark a la base de datos del metastore de Hive externo. Antes de configurar el grupo de Spark, ejecute el código siguiente en cualquier cuaderno del grupo de Spark para probar la conexión a la base de datos del metastore de Hive externo. 

También puede obtener la versión de Metastore de Hive a partir de los resultados de salida. La versión de Metastore de Hive se usará en la configuración de Spark.

```
%%spark 
import java.sql.DriverManager 
/** this url could be copied from Azure portal > Azure SQL database > Connection strings > JDBC **/ 
val url = s"jdbc:sqlserver://{your_servername_here}.database.windows.net:1433;database={your_database_here};user={your_username_here};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;" 
try { 
    val connection = DriverManager.getConnection(url) 
    val result = connection.createStatement().executeQuery("select t.SCHEMA_VERSION from VERSION t") 
    result.next(); 
    println(s"Successful to test connection. Hive metastore version is ${result.getString(1)}") 
} catch { 
    case ex: Throwable =>println(s"Failed to establish connection:\n $ex") 
}  
```

## <a name="configure-spark-to-use-the-external-hive-metastore"></a>Configuración de Spark para usar el metastore de Hive externo
Después de crear correctamente el servicio vinculado al metastore de Hive externo, debe configurar algunas opciones en Spark para usar el metastore de Hive externo. Puede definir la configuración en el nivel de grupo de Spark o en el nivel de sesión de Spark. 

Estas son las configuraciones y descripciones:

|Configuración de Spark|Descripción|
|--|--|
|`spark.sql.hive.metastore.version`|Versiones compatibles: <ul><li>`1.2`</li><li>`2.1`</li><li>`3.1`</li></ul> Asegúrese de usar las dos primeras partes sin la tercera|
|`spark.sql.hive.metastore.jars`|<ul><li>Versión 1.2: `/opt/hive-metastore/lib-1.2/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>Versión 2.1: `/opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>Versión 3.1: `/opt/hive-metastore/lib-3.1/*:/usr/hdp/current/hadoop-client/lib/*`</li></ul>|
|`spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`|Nombre del servicio vinculado creado en Azure SQL Database.|

### <a name="configure-spark-pool"></a>Configuración del grupo de Spark 
Al crear el grupo de Spark, en la pestaña **Configuración adicional**, coloque las configuraciones siguientes en un archivo de texto y cárguelo en la sección de **Configuración de Apache Spark**. También puede usar el menú contextual de un grupo de Spark existente y elegir la configuración de Apache Spark para agregar estas configuraciones.

   :::image type="content" source="./media/share-hive-metastore-with-synapse/config-spark-pool.png" alt-text="Configuración del grupo de Spark":::

Actualice la versión del metastore y el nombre del servicio vinculado y guarde las configuraciones siguientes en un archivo de texto para la configuración del grupo de Spark:

```
spark.sql.hive.metastore.version <your hms version, Make sure you use the first 2 parts without the 3rd part>
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name <your linked service name to Azure SQL DB>
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*
```

Este es un ejemplo de metastore versión 2.1 con un servicio vinculado denominado HiveCatalog21:

```
spark.sql.hive.metastore.version 2.1
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name HiveCatalog21
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*
```

### <a name="configure-a-spark-session"></a>Configuración de una sesión de Spark
Si no quiere configurar el grupo de Spark, también puede configurar la sesión de Spark en el cuaderno mediante el comando %%configure magic. Este es el código. La misma configuración también se puede aplicar a un trabajo por lotes de Spark. 

```
%%configure -f
{
    "conf":{
        "spark.sql.hive.metastore.version":"<your linked service name to Azure SQL DB>",
        "spark.hadoop.hive.synapse.externalmetastore.linkedservice.name":"<your linked service name to Azure SQL DB>",
        "spark.sql.hive.metastore.jars":"/opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*"
    }
}
```

### <a name="run-queries-to-verify-the-connection"></a>Realizar consultas para comprobar la conexión
Después de toda esta configuración, pruebe a enumerar objetos de catálogo mediante la ejecución de la consulta siguiente en el cuaderno de Spark para comprobar la conectividad con el metastore de Hive externo.
```python
spark.sql("show databases").show()
```

## <a name="set-up-storage-connection"></a>Configuración de una conexión de almacenamiento
El servicio vinculado a la base de datos del metastore de Hive solo proporciona acceso a los metadatos del catálogo de Hive. Para consultar las tablas existentes, también debe configurar la conexión a la cuenta de almacenamiento que almacena los datos subyacentes de las tablas de Hive.

### <a name="set-up-connection-to-adls-gen-2"></a>Configuración de la conexión a ADLS Gen2
#### <a name="workspace-primary-storage-account"></a>Cuenta de almacenamiento principal del área de trabajo
Si los datos subyacentes de las tablas de Hive se almacenan en la cuenta de almacenamiento principal del área de trabajo, no es necesario realizar configuraciones adicionales. Solo funcionará siempre que siga las instrucciones de configuración de almacenamiento durante la creación del área de trabajo.

#### <a name="other-adls-gen-2-account"></a>Otra cuenta de ADLS Gen2
Si los datos subyacentes de los catálogos de Hive se almacenan en otra cuenta de ADLS Gen2, debe asegurarse de que los usuarios que ejecutan consultas de Spark tienen un rol de **colaborador de datos de Storage Blob** en la cuenta de almacenamiento de ADLS Gen2. 

### <a name="set-up-connection-to-blob-storage"></a>Configuración de la conexión a Blob Storage
Si los datos subyacentes de las tablas de Hive se almacenan en la cuenta de Azure Blob Storage, configure la conexión siguiendo estos pasos:

1.  Abra Synapse Studio, vaya a **Datos > Pestaña vinculada > botón Agregar** > **Conectar a datos externos**.

    :::image type="content" source="./media/share-hive-metastore-with-synapse/connect-to-storage-account.png" alt-text="Conectar con la cuenta de almacenamiento" border="true":::

2.  Escoja **Azure Blob Storage** y haga clic en **Continuar**.
3.  Proporcione el **Nombre** del servicio vinculado. Registre el nombre del servicio vinculado; esta información se usará en la configuración de sesión de Spark en breve.
4.  Seleccione la cuenta de Azure Blob Storage. Asegúrese de que el método de autenticación es **Clave de cuenta**. Actualmente, el grupo de Spark solo puede acceder a la cuenta de Blob Storage a través de la clave de cuenta.
5.  **Pruebe la conexión** y haga clic en **Crear**.
6.  Después de crear el servicio vinculado a la cuenta de Blob Storage, al ejecutar consultas de Spark, asegúrese de ejecutar el siguiente código de Spark en el cuaderno para obtener acceso a la cuenta de Blob Storage para la sesión de Spark. Obtenga más información sobre por qué debe hacerlo [aquí](../synapse-analytics/spark/apache-spark-secure-credentials-with-tokenlibrary.md).

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

Después de configurar las conexiones de almacenamiento, puede consultar las tablas existentes en el metastore de Hive.

## <a name="known-limitations"></a>Limitaciones conocidas

- El explorador de objetos de Synapse Studio seguirá mostrando objetos en el metastore de Synapse administrado, en lugar del HMS externo. Estamos mejorando esta experiencia.
- [SQL <-> sincronización de Spark](../synapse-analytics/sql/develop-storage-files-spark-tables.md) no funciona al utilizar HMS externo.  
- Solo Azure SQL Database se admite como base de datos de metastore de Hive externo. Solo se admite autorización SQL.
- Actualmente, Spark solo funciona con tablas externas de Hive y tablas de Hive administradas no transitorias o no ACID. Actualmente, no es compatible con las tablas de Hive ACID o transaccionales.
- La integración de Apache Ranger no se admite de momento.

## <a name="troubleshooting"></a>Solución de problemas
### <a name="see-below-error-when-querying-a-hive-table-with-data-stored-in-blob-storage"></a>Vea el siguiente error al consultar una tabla de Hive con datos almacenados en Blob Storage
```
Py4JJavaError : An error occurred while calling o241.load. : org.apache.hadoop.fs.azure.AzureException: org.apache.hadoop.fs.azure.AzureException: No credentials found for account demohdicatalohdistorage.blob.core.windows.net in the configuration, and its container demohdicatalog-2021-07-15t23-42-51-077z is not accessible using anonymous credentials. Please check if the container exists first. If it is not publicly available, you have to provide account credentials.
```

Al usar la autenticación de clave en la cuenta de almacenamiento a través de un servicio vinculado, debe realizar un paso adicional para obtener el token para la sesión de Spark. Ejecute el código siguiente para configurar la sesión de Spark antes de ejecutar la consulta. Obtenga más información sobre por qué debe hacerlo aquí.

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

### <a name="see-below-error-when-query-a-table-stored-in-adls-gen2-account"></a>Vea el siguiente error al consultar una tabla almacenada en una cuenta de ADLS Gen2.
```
Py4JJavaError : An error occurred while calling o305.load. : Operation failed: "This request is not authorized to perform this operation using this permission.", 403, HEAD
```

Esto podría ocurrir porque el usuario que ejecuta la consulta de Spark no tiene suficiente acceso a la cuenta de almacenamiento subyacente. Asegúrese de que los usuarios que ejecutan consultas de Spark tienen el rol de **colaborador de datos de Storage Blob** en la cuenta de almacenamiento de ADLS Gen2. Este paso se puede realizar más adelante después de crear el servicio vinculado.

### <a name="hms-schema-related-settings"></a>Configuración relacionada con el esquema de HMS 
Para evitar cambiar la versión o el esquema de back-end de HMS, el sistema establece las siguientes configuraciones de Hive de forma predeterminada: 
```
spark.hadoop.hive.metastore.schema.verification true 
spark.hadoop.hive.metastore.schema.verification.record.version false 
spark.hadoop.datanucleus.fixedDatastore true 
spark.hadoop.datanucleus.schema.autoCreateAll false 
```

Si la versión de HMS es 1.2.1 o 1.2.2, hay un problema en Hive que solicita solo 1.2.0 si establece spark.hadoop.hive.metastore.schema.verification en true. Nuestra sugerencia es que puede modificar la versión de HMS a 1.2.0 o sobrescribir las dos configuraciones siguientes como solución:

```
spark.hadoop.hive.metastore.schema.verification false 
spark.hadoop.hive.synapse.externalmetastore.schema.usedefault false
```

Si necesita migrar la versión de HMS, se recomienda usar la [herramienta de esquema de Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool). Y si los clústeres de HDInsight han usado HMS, se recomienda usar la [versión proporcionada por HDI](./interactive-query/apache-hive-migrate-workloads.md). 

### <a name="when-sharing-the-metastore-with-hdinsight-40-spark-clusters-i-cannot-see-the-tables"></a>Al compartir el metastore con clústeres de Spark de HDInsight 4.0, no puedo ver las tablas.
Si desea compartir el catálogo de Hive con un clúster de Spark en HDInsight 4.0, asegúrese de que la propiedad `spark.hadoop.metastore.catalog.default` de Spark de Synapse esté alineada con el valor de Spark de HDInsight. El valor predeterminado es `Spark`.

### <a name="when-sharing-the-hive-metastore-with-hdinsight-40-hive-clusters-i-can-list-the-tables-successfully-but-only-get-empty-result-when-i-query-the-table"></a>Al compartir el metastore de Hive con clústeres de Hive de HDInsight 4.0, puedo enumerar las tablas correctamente, pero solo obtengo un resultado vacío al consultar la tabla.
Como se mencionó en las limitaciones, el grupo de Spark de Synapse solo admite tablas externas de Hive y tablas administradas no transitorias o ACID. Actualmente no admite tablas ACID o transaccionales de Hive. De forma predeterminada, en los clústeres de Hive de HDInsight 4.0, todas las tablas administradas se crean como tablas ACID o transaccionales de forma predeterminada, por eso obtiene resultados vacíos al consultarlas. 