---
title: Copia de datos a y desde WASB en Azure Data Lake Storage Gen1 mediante DistCp
description: Use la herramienta DistCp para copiar datos hacia y desde blobs Azure Storage a Azure Data Lake Storage Gen1
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: normesta
ms.openlocfilehash: 5e1f4cda87bbc73218826e76e3e11760c31dc159
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637441"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Uso de DistCp para copiar datos entre Azure Storage blobs y Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Uso de DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Uso de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Si tiene un clúster de HDInsight con acceso a Azure Data Lake Storage Gen1, puede usar herramientas del ecosistema de Hadoop como DistCp para copiar datos desde y hacia un almacenamiento de clúster de HDInsight (WASB) en una cuenta de Data Lake Storage Gen1. En este artículo se muestra cómo usar la herramienta DistCp.

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Azure Data Lake Storage Gen1**. Para instrucciones sobre cómo crear una, consulte la [introducción a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Clúster de Azure HDInsight** con acceso a una cuenta de Data Lake Storage Gen1. Consulte [Creación de un clúster de HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de habilitar el Escritorio remoto para el clúster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar DistCp desde un clúster de HDInsight de Linux

Un clúster HDInsight incluye la herramienta DistCp, que se puede usar para copiar datos de orígenes diferentes en un clúster de HDInsight. Si ha configurado el clúster de HDInsight para que use Data Lake Storage Gen1 como almacenamiento adicional, puede usar DistCp para copiar datos desde y hacia una cuenta de Data Lake Storage Gen1. En esta sección, veremos cómo usar la herramienta DistCp.

1. Desde el escritorio, use SSH para conectarse al clúster. Consulte [Conexión a un clúster de HDInsight basado en Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Ejecute los comandos desde el símbolo del sistema SSH.

1. Compruebe si puede tener acceso a la Azure Storage blobs (WASB). Ejecute el siguiente comando:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   La salida proporciona una lista de contenido en el blob de almacenamiento.

1. De forma similar, compruebe si puede tener acceso a la cuenta de Data Lake Storage Gen1 desde el clúster. Ejecute el siguiente comando:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    La salida proporciona una lista de archivos y carpetas de la cuenta de Data Lake Storage Gen1.

1. Use DistCp para copiar datos de WASB a una cuenta de Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    El comando copia el contenido de la carpeta **/example/data/gutenberg/** de WASB en **/myfolder** en la cuenta de Data Lake Storage Gen1.

1. Asimismo, use DistCp para copiar datos de una cuenta de Data Lake Storage Gen1 a WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    El comando copia el contenido de **/myfolder** de la cuenta de Data Lake Storage Gen1 en la carpeta **/example/data/gutenberg/** de WASB.

## <a name="performance-considerations-while-using-distcp"></a>Consideraciones de rendimiento sobre el uso de DistCp

Dado que la granularidad más baja de la herramienta DistCp es un archivo único, establecer el número máximo de copias simultáneas es el parámetro más importante para optimizarla en Data Lake Storage Gen1. El número de copias simultáneas se controla mediante la definición del número de parámetros de mapeador (‘m’) en la línea de comandos. Este parámetro especifica el número máximo de mapeadores que se usan para copiar los datos. El valor predeterminado es 20.

Ejemplo:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Cómo se puede determinar el número de mapeadores que se debe usar

A continuación hay algunas instrucciones que puede usar.

* **Paso 1: Determinación de la memoria total de YARN**: el primer paso consiste en determinar la memoria YARN disponible para el clúster donde se ejecuta el trabajo DistCp. Esta información está disponible en el portal de Ambari asociado con el clúster. Vaya a YARN y vea la pestaña **Configuraciones** para ver la memoria de YARN. Para obtener la memoria de YARN total, multiplique la memoria de YARN por cada nodo por el número de nodos que tiene en el clúster.

* **Paso 2: Cálculo del número de mapeadores**: el valor de **m** es igual al cociente de la memoria de YARN total dividido por el tamaño del contenedor de YARN. La información del tamaño del contenedor YARN también está disponible en el portal Ambari. Navegue a YARN y examine la pestaña **Configs** (Configuraciones). En esta ventana se muestra el tamaño del contenedor de YARN. La ecuación para llegar al número de asignadores (**m**) es:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Ejemplo:

Supongamos que tiene cuatro nodos D14v2s en el clúster y desea transferir 10 TB de datos desde 10 carpetas diferentes. Cada una de las carpetas contiene diferentes cantidades de datos y los tamaños de archivo dentro de cada carpeta son diferentes.

* Memoria de YARN total: en el portal de Ambari determinará que la memoria de YARN es de 96 GB para un nodo D14. Por lo tanto, la memoria de YARN total para el clúster de cuatro nodos es:

   `YARN memory = 4 * 96GB = 384GB`

* Número de asignadores: en el portal de Ambari determinará que el tamaño del contenedor de YARN es 3072 para un nodo del clúster D14. Por lo tanto, el número de mapeadores es:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Si otras aplicaciones usan memoria, puede elegir usar solo una parte de la memoria de YARN del clúster para DistCp.

### <a name="copying-large-datasets"></a>Copia de grandes conjuntos de datos

Cuando el tamaño del conjunto de datos que se va a mover es grande (por ejemplo, > 1 TB) o si tiene muchas carpetas diferentes, considere la posibilidad de usar varios trabajos de DistCp. No es probable que se produzca un aumento del rendimiento, pero se propagan los trabajos para que, si se produce un error en algún trabajo, solo sea necesario reiniciar ese trabajo específico en lugar de todo el trabajo.

### <a name="limitations"></a>Limitaciones

* DistCp intenta crear a asignadores que tengan un tamaño similar para optimizar el rendimiento. El aumento del número de asignadores no siempre aumenta el rendimiento.

* DistCp está limitado a solo un asignador por archivo. Por lo tanto, no debe tener más mapeadores que archivos. Dado que DistCp solo puede asignar un mapeador a un archivo, esto limita la cantidad de simultaneidad que se puede usar para copiar archivos grandes.

* Si tiene un número pequeño de archivos grandes, divídalo en fragmentos de archivo de 256 MB para proporcionar una mayor simultaneidad.

* Si va a copiar desde una cuenta de almacenamiento de blobs de Azure, es posible que el trabajo de copia esté limitado en el lado de almacenamiento de blobs. Esta situación degrada el rendimiento de su trabajo de copia. Para más información sobre los límites del almacenamiento de blobs de Azure, consulte [Azure Storage limits en](../azure-resource-manager/management/azure-subscription-service-limits.md) Límites de suscripción y servicios de Azure.

## <a name="see-also"></a>Consulte también

* [Copia de datos de blobs de Azure Storage en Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Protección de datos en Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics with Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md) (Uso de Azure Data Lake Analytics con Data Lake Storage Gen1)
* [Use Azure HDInsight with Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md) (Uso de Azure HDInsight con Data Lake Storage Gen1)
