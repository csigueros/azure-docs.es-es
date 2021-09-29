---
title: 'Almacenamiento: Migración de Apache Hadoop local a Azure HDInsight'
description: Obtenga información acerca de las prácticas recomendadas de almacenamiento para migrar clústeres locales de Apache Hadoop a Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 6efc962a915bc549fa14008b8a6487085e0a49e6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797655"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migración de clústeres locales de Apache Hadoop a Azure HDInsight

En este artículo se proporcionan recomendaciones para el almacenamiento de datos en sistemas de Azure HDInsight. Forma parte de una serie de artículos que proporcionan prácticas recomendadas para ayudar a migrar sistemas locales de Apache Hadoop a Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Elección del sistema de almacenamiento correcto para los clústeres de HDInsight

Puede volver a crear la estructura del directorio local del sistema de archivos de Apache Hadoop (HDFS) en Azure Blob Storage o en Azure Data Lake Storage. A continuación, podrá eliminar de forma segura los clústeres de HDInsight que se usan para el cálculo sin perder datos del usuario. Ambos servicios se pueden usar como sistema de archivos predeterminado y sistema de archivos adicional para un clúster de HDInsight. El clúster de HDInsight y la cuenta de almacenamiento deben ubicarse en la misma región.

### <a name="azure-storage"></a>Azure Storage

Los clústeres de HDInsight pueden usar el contenedor de blobs de Azure Storage como sistema de archivos predeterminado o sistema de archivos adicional.  La cuenta de almacenamiento de nivel estándar se puede usar con los clústeres de HDInsight. Sin embargo, no se admite el nivel Premier. El contenedor de blobs predeterminado almacena información específica del clúster, como registros y el historial de trabajos.  No se permite compartir un contenedor de blobs como el sistema de archivos predeterminado entre varios clústeres.

Las cuentas de almacenamiento definidas en el proceso de creación y sus claves correspondientes se almacenan en `%HADOOP_HOME%/conf/core-site.xml` en los nodos de clúster. También se puede acceder desde la sección "Custom core site" (Sitio principal personalizado) de la configuración de HDFS en la interfaz de usuario de Ambari. La clave de la cuenta de almacenamiento se cifra de forma predeterminada y se usa un script personalizado de descifrado para descifrar las claves antes de pasarlas a los demonios de Hadoop. Los trabajos, incluidos Hive, MapReduce, streaming de Hadoop y Pig, llevan una descripción de cuentas de almacenamiento y metadatos con ellos.

El almacenamiento de Azure Storage se puede replicar geográficamente. Aunque la replicación geográfica aporta recuperación geográfica y redundancia de datos, una conmutación por error en la ubicación replicada geográficamente afecta gravemente al rendimiento y puede incurrir en costes adicionales. Se recomienda que elija la replicación geográfica de forma inteligente y únicamente si merece la pena pagar el costo adicional por el valor de los datos.

Uno de los formatos siguientes puede utilizarse para acceder a los datos almacenados en Azure Storage:

|Formato de acceso a datos |Descripción |
|---|---|
|`wasb:///`|acceso al almacenamiento predeterminado mediante comunicación sin cifrar.|
|`wasbs:///`|acceso al almacenamiento predeterminado mediante comunicación cifrada.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|se usa al comunicarse con una cuenta de almacenamiento no predeterminada. |

En [Objetivos de escalabilidad para cuentas de almacenamiento estándar](../../storage/common/scalability-targets-standard-account.md) se indican los límites actuales de las cuentas de Azure Storage. Si las necesidades de la aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, la aplicación puede compilarse de forma que use varias cuentas de almacenamiento y, después, se dividan los datos entre dichas cuentas de almacenamiento.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) proporciona métricas para todos los servicios de almacenamiento y Azure Portal se puede configurar a fin de recopilar métricas para visualizarlas mediante gráficos. Puede crear alertas para recibir notificaciones cuando se alcancen los umbrales de las métricas de los recursos de almacenamiento.

Azure Storage ofrece la posibilidad de [eliminar temporalmente objetos de blob](../../storage/blobs/soft-delete-blob-overview.md) para ayudar a recuperar datos que una aplicación u otro usuario de la cuenta de almacenamiento han modificado o eliminado por error.

También puede crear [instantáneas de blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob). Una instantánea es una versión de solo lectura de un blob que se ha realizado en un momento dado y proporciona una manera de realizar una copia de seguridad de dicho blob. Una vez se crea la instantánea, puede leerla, copiarla o eliminarla, pero no modificarla.

> [!Note]
> Para la versión anterior de las distribuciones locales de Hadoop que no tiene el certificado "wasbs", debe importarse al almacén de confianza de Java.

Los siguientes métodos pueden usarse para importar certificados al almacén de confianza de Java:

Descargue el certificado TLS/SSL de Azure Blob a un archivo

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importe el archivo anterior al almacén de confianza de Java en todos los nodos

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Compruebe que el certificado se ha agregado al almacén de confianza

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Para más información, consulte los siguientes artículos.

- [Uso de Azure Storage con clústeres de Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Objetivos de escalabilidad para cuentas de almacenamiento estándar](../../storage/common/scalability-targets-standard-account.md)
- [Objetivos de escalabilidad y rendimiento de Blob Storage](../../storage/blobs/scalability-targets.md)
- [Lista de comprobación de rendimiento y escalabilidad de Microsoft Azure Storage](../../storage/blobs/storage-performance-checklist.md)
- [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Supervisión de una cuenta de almacenamiento en Azure Portal](../../storage/common/manage-storage-analytics-logs.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementa el modelo de control de acceso de estilo POSIX y HDFS. Proporciona integración de primera clase con Azure AD para control de acceso específico. No hay límites en el tamaño de los datos que puede almacenar, ni en su capacidad para ejecutar análisis paralelos masivos.

Para más información, consulte los siguientes artículos.

- [Creación de clústeres de HDInsight con Data Lake Storage Gen1 mediante Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Usar Data Lake Storage Gen1 con clústeres de Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 es la oferta de almacenamiento más reciente. Unifica las capacidades básicas de la primera generación de Azure Data Lake Storage Gen1 con un punto de conexión de sistema de archivos compatible con Hadoop, que se integra directamente en Azure Blob Storage. Esta mejora combina las ventajas de escala y costo de almacenamiento de objetos con la confiabilidad y el rendimiento que normalmente solo se asocian con sistemas de archivos locales.

Azure Data Lake Storage Gen2 se basa en [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) y permite establecer una conexión con los datos mediante los paradigmas de sistema de archivos y almacenamiento de objetos. Las características de [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), como la semántica del sistema de archivos, la escala y la seguridad de nivel de archivo, se combinan con funcionalidades de recuperación ante desastres o alta disponibilidad, y de almacenamiento por niveles de bajo costo, así como con un ecosistema de herramientas o SDK de [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md). En Data Lake Storage Gen2, permanecen todas las cualidades del almacenamiento de objetos y se agregan las ventajas de una interfaz de sistema de archivos optimizada para cargas de trabajo de análisis.

Una característica fundamental de Data Lake Storage Gen2 es la adición de un [espacio de nombres jerárquico](../../storage/blobs/data-lake-storage-namespace.md) al servicio de almacenamiento de blobs que organiza los objetos o archivos en una jerarquía de directorios para el acceso a datos de rendimiento.  La estructura jerárquica permite que operaciones como el cambio de nombre o la eliminación de un directorio se convierten en operaciones de metadatos atómicas únicas en el directorio, en lugar de enumerar y procesar todos los objetos que comparten el prefijo del nombre del directorio.

Anteriormente, los análisis basados en la nube tenían que llegar a un acuerdo en materia de rendimiento, administración y seguridad. Las características clave de Azure Data Lake Storage (ADLS) Gen2 son las siguientes:

- **Acceso compatible con Hadoop**: Azure Data Lake Storage Gen2 permite administrar datos y acceder a ellos igual que lo haría con un [sistema de archivos distribuido de Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). El nuevo [controlador ABFS](../../storage/blobs/data-lake-storage-abfs-driver.md) está disponible en todos los entornos de Apache Hadoop que se incluyen en [Azure HDInsight](../index.yml). Este controlador permite acceder a datos almacenados en Data Lake Storage Gen2.

- **Superconjunto de permisos POSIX**: el modelo de seguridad de Data Lake Gen2 es totalmente compatible con los permisos de ACL y POSIX junto con granularidad adicional específica de Data Lake Storage Gen2. Se puede realizar la configuración mediante herramientas de administración o marcos, como Hive y Spark.

- **Rentabilidad**: Data Lake Storage Gen2 ofrece transacciones y capacidad de almacenamiento de bajo costo. Mientras las transiciones de datos recorren su ciclo de vida completo, las tasas de facturación cambian para minimizar los costos a través de características integradas, como el [ciclo de vida de Azure Blob Storage](../../storage/blobs/lifecycle-management-overview.md).

- **Funciona con aplicaciones, marcos y herramientas de Blob Storage**: Data Lake Storage Gen2 sigue funcionando con una amplia gama de herramientas, marcos y aplicaciones que existen actualmente para Blob Storage.

- **Controlador optimizado**: el controlador de Azure Blob File System (ABFS) está [específicamente optimizado](../../storage/blobs/data-lake-storage-abfs-driver.md) para el análisis de macrodatos. Las API REST correspondientes se exponen a través del punto de conexión dfs, dfs.core.windows.net.

Uno de los formatos siguientes puede utilizarse para acceder a los datos almacenados en ADLS Gen2:
- `abfs:///`: accede al almacén Data Lake Storage predeterminado del clúster.
- `abfs://file_system@account_name.dfs.core.windows.net`: se usa al comunicarse con un almacén Data Lake Storage no predeterminado.

Para más información, consulte los siguientes artículos.

- [Introducción a Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [Controlador del sistema de archivos de Azure Blob (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Uso de Data Lake Storage Gen2 con clústeres de Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Claves seguras de Azure Storage dentro de la configuración del clúster local de Hadoop

Las claves de Azure Storage agregadas a los archivos de configuración de Hadoop establecen la conectividad entre la instancia local de HDFS y Azure Blob Storage. Estas claves se pueden proteger mediante su cifrado con el marco de proveedores de credenciales de Hadoop. Una vez cifradas, pueden almacenarse y se puede acceder a ellas de forma segura.

**Para aprovisionar las credenciales:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Para agregar la ruta de acceso del proveedor anterior a core-site.xml o a la configuración de Ambari en el sitio principal personalizado:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> La propiedad de la ruta de acceso del proveedor también puede agregarse a la línea de comandos distcp, en lugar de almacenar la clave en el nivel de clúster en core-site.xml como sigue:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Restricción del acceso a datos de Azure Storage mediante SAS

Por defecto, HDInsight tiene acceso total a los datos de las cuentas de Azure Storage asociadas con el clúster. Las firmas de acceso compartido (SAS) en el contenedor de blobs pueden usarse para restringir el acceso a los datos, como por ejemplo, para proporcionar a los usuarios acceso de solo lectura a los datos.

### <a name="using-the-sas-token-created-with-python"></a>Uso del token SAS creado con python

1. Abra el archivo [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) y cambie los valores siguientes:

    |Propiedad del token|Descripción|
    |---|---|
    |policy_name|El nombre que se usará para la directiva almacenada que se va a crear.|
    |storage_account_name|El nombre de la cuenta de almacenamiento.|
    |storage_account_key|La clave de la cuenta de almacenamiento.|
    |storage_container_name|El contenedor de la cuenta de almacenamiento a la que desea restringir el acceso.|
    |example_file_path|La ruta de acceso a un archivo que se carga en el contenedor.|

2. El archivo SASToken.py viene con los permisos de `ContainerPermissions.READ + ContainerPermissions.LIST` y se puede ajustar según el caso de uso.

3. Ejecute el script como se indica a continuación: `python SASToken.py`.

4. Cuando finaliza el script, se muestra un token de SAS similar al texto siguiente: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`.

5. Para limitar el acceso a un contenedor con la Firma de acceso compartido, agregue una entrada personalizada a la configuración del sitio principal para el clúster con la propiedad Agregar del sitio principal personalizado en la configuración avanzada de Ambari HDFS.

6. Utilice los siguientes valores para los campos **Key** (Clave) y **Value** (Valor):

    **Clave**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Valor**: clave SAS que devuelve la aplicación Python en el paso 4 anterior.

7. Haga clic en el botón **Add** (Agregar) para guardar esta clave y este valor y, a continuación, haga clic en el botón **Save** (Guardar) para guardar los cambios de configuración. Cuando se le solicite, agregue una descripción del cambio ("Agregar acceso de almacenamiento de SAS", por ejemplo) y haga clic en **Save** (Guardar).

8. En la interfaz de usuario web de Ambari, seleccione HDFS en la lista de la izquierda y, a continuación, seleccione **Restart All Affected** (Reiniciar todas las entradas afectadas) en la lista desplegable Service Actions (Acciones del servicio) de la derecha. Cuando se le solicite, seleccione **Confirm Restart All** (Confirmar reiniciar todo).

9. Repita este proceso para MapReduce2 y YARN.

Hay tres cosas importantes que se deben tener en cuenta sobre el uso de tokens de SAS en Azure:

1. Cuando se crean tokens de SAS con permisos "READ + LIST", los usuarios que accedan al contenedor de blobs con ese token SAS no podrán escribir ni eliminar datos. Los usuarios que accedan al contenedor de blobs con ese token SAS e intenten realizar una operación de escritura o eliminación, verán un mensaje similar al siguiente `"This request is not authorized to perform this operation"`.

2. Cuando los tokens de SAS se generan con permisos `READ + LIST + WRITE` (para restringir solo `DELETE`), comandos como `hadoop fs -put` escriben primero en un archivo `\_COPYING\_` y, después, intentan cambiar el nombre del archivo. Esta operación HDFS se asigna a `copy+delete` para WASB. Puesto que el permiso `DELETE` no se ha proporcionado, "put" genera un error. La operación `\_COPYING\_` es una característica de Hadoop diseñada para proporcionar cierto control de simultaneidad. Actualmente no hay ninguna manera de restringir solo la operación "DELETE" sin que ello afecte a las operaciones "WRITE".

3. Lamentablemente, el proveedor de credenciales de Hadoop y el proveedor de claves de descifrado (ShellDecryptionKeyProvider) no funcionan actualmente con los tokens de SAS y, por lo tanto, no pueden resguardarse actualmente de la visibilidad.

Para más información, consulte [Uso de firmas de acceso compartido de Azure Storage para restringir el acceso a datos en HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Uso de cifrado de datos y replicación

Todos los datos escritos en Azure Storage se cifran automáticamente con el [cifrado del servicio Storage (SSE)](../../storage/common/storage-service-encryption.md). Los datos de la cuenta de Azure Storage siempre se replican para lograr alta disponibilidad.  Cuando cree una cuenta de almacenamiento, puede elegir una de las siguientes opciones de replicación:

- [Almacenamiento con redundancia local (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Almacenamiento con redundancia de zona (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Almacenamiento con redundancia geográfica (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).](../../storage/common/storage-redundancy.md)

Azure Storage proporciona almacenamiento con redundancia local (LRS), pero también debe copiar los datos críticos a otra cuenta de Azure Storage en otra región con una frecuencia orientada a las necesidades del plan de recuperación ante desastres. Existen varios métodos para copiar datos, como [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md) o [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md).  También se recomienda aplicar directivas de acceso a la cuenta de Azure Storage para evitar la eliminación accidental.

Para más información, consulte los siguientes artículos.

- [Replicación de Azure Storage](../../storage/common/storage-redundancy.md)
- [Guía de recuperación ante desastres para Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Adjuntar cuentas de Azure Storage adicionales al clúster

Durante el proceso de creación de HDInsight, se elige una cuenta de Azure Storage, Azure Data Lake Storage Gen1 o Azure Data Lake Storage Gen2 como sistema de archivos predeterminado. Además de esta cuenta de almacenamiento predeterminada, puede agregar otras desde la misma suscripción de Azure o desde otras diferentes tanto durante el proceso de creación del clúster como después de que el clúster se haya creado.

Puede agregarse una cuenta de almacenamiento adicional de una de las maneras siguientes:
- En la configuración avanzada de Ambari HDFS, vaya al sitio principal personalizado para agregar el nombre y la clave de la cuenta de almacenamiento, y reinicie los servicios
- Uso de la [acción de script](../hdinsight-hadoop-add-storage.md) pasando el nombre y la clave de la cuenta de almacenamiento

> [!Note]
> En casos de uso válidos, se pueden aumentar los límites de almacenamiento de Azure a través de una solicitud realizada al [Soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

Para más información, consulte [Adición de más cuentas de almacenamiento a HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Pasos siguientes

Lea el siguiente artículo de esta serie: [Data migration best practices for on-premises to Azure HDInsight Hadoop migration](apache-hadoop-on-premises-migration-best-practices-data-migration.md) (Procedimientos recomendados para migrar datos del almacenamiento local a Azure HDInsight Hadoop)