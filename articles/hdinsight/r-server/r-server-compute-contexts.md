---
title: 'Opciones de contexto de proceso para ML Services en HDInsight: Azure'
description: Obtenga información acerca de las distintas opciones de contexto de proceso disponibles para los usuarios con ML Services en HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/02/2020
ROBOTS: NOINDEX
ms.openlocfilehash: efdf410ac566297668a06b0e7da457fcd49bfb59
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299346"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opciones de contexto de proceso para ML Services en HDInsight

[!INCLUDE [retirement banner](../includes/ml-services-retirement.md)]

ML Services en Azure HDInsight controla cómo se ejecutan las llamadas mediante el establecimiento del contexto de proceso. En este artículo se describen las opciones que están disponibles para especificar si la ejecución se realiza o no en paralelo y, en caso afirmativo, cómo se lleva a cabo a través del nodo perimetral o del clúster de HDInsight.

El nodo perimetral de un clúster proporciona un lugar conveniente para conectarse al clúster y ejecutar los scripts de R. Con un nodo perimetral, tiene la opción de ejecutar las funciones distribuidas paralelizadas de RevoScaleR en los diferentes núcleos del servidor de nodo perimetral. También puede ejecutarlas en los nodos del clúster mediante los contextos de proceso de Apache Spark o Hadoop Map Reduce de RevoScaleR.

## <a name="ml-services-on-azure-hdinsight"></a>ML Services en Azure HDInsight

[ML Services en Azure HDInsight](r-server-overview.md) proporciona las más recientes funcionalidades para el análisis basado en R. Puede usar datos almacenados en un contenedor Apache Hadoop HDFS en su cuenta de almacenamiento de [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage"), un almacén de Data Lake Store o el sistema de archivos local de Linux. Como ML Services se basa en el lenguaje R de código abierto, las aplicaciones basadas en R que cree pueden aplicar cualquiera de los más de 8000 paquetes de R de código abierto. También pueden utilizar las rutinas de [RevoScaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), un paquete de análisis de macrodatos de Microsoft que se incluye con ML Services.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos de proceso de un nodo perimetral

En general, el script de R que se ejecuta en el nodo perimetral del clúster de ML Services lo hace dentro del intérprete de R de dicho nodo. Las excepciones son esos pasos que llaman a una función RevoScaleR. Las llamadas a RevoScaleR se ejecutan en un entorno de proceso determinado por la manera en que establece el contexto de proceso de RevoScaleR.  Al ejecutar el script de R desde un nodo perimetral, los posibles valores del contexto de proceso son:

- secuencial local (*local*)
- paralelo local (*localpar*)
- MapReduce
- Spark

Las opciones *local* y *localpar* solo difieren en cómo se ejecutan las llamadas de **rxExec**. Las dos ejecutan otras llamadas a función de rx de manera paralela en todos los núcleos disponibles, a menos que se especifique lo contrario mediante el uso de la opción **numCoresToUse** de RevoScaleR; por ejemplo, `rxOptions(numCoresToUse=6)`. Las opciones de ejecución en paralelo ofrecen un rendimiento óptimo.

En la tabla siguiente se resumen las distintas opciones de contexto de proceso para establecer cómo se ejecutan las llamadas:

| Contexto de proceso  | Cómo definir                      | Contexto de ejecución                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Secuencial local | rxSetComputeContext('local')    | Ejecución en paralelo en los núcleos del servidor de nodo perimetral, excepto las llamadas de rxExec, que se ejecutan en serie |
| Paralelo local   | rxSetComputeContext('localpar') | Ejecución paralelizada en los núcleos del servidor de nodo perimetral |
| Spark            | RxSpark()                       | Ejecución distribuida paralelizada vía Spark en los nodos del clúster de HDI |
| MapReduce       | RxHadoopMR()                    | Ejecución distribuida paralelizada vía MapReduce en los nodos del clúster de HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Directrices para decidir en un contexto de proceso

¿Cuál de las tres opciones que elija que proporcionan la ejecución en paralelo depende de la naturaleza del trabajo de análisis, el tamaño y la ubicación de los datos? No hay ninguna fórmula sencilla que indique qué contexto de proceso ejecutar. Sin embargo, hay algunos principios fundamentales que pueden ayudarle a tomar la decisión correcta o, al menos, a reducir sus opciones antes de ejecutar una prueba comparativa. Estos principios rectores incluyen:

- El sistema de archivos de Linux local es más rápido que HDFS.
- Los análisis repetidos son más rápidos si los datos son locales y están en XDF.
- Es preferible transmitir pequeñas cantidades de datos desde un origen de datos de texto. Si la cantidad de datos es mayor, conviértala a XDF antes del análisis.
- La sobrecarga de copiar o transmitir los datos al nodo perimetral para su análisis se convierte en algo difícil de manejar en el caso de cantidades de datos muy grandes.
- Apache Spark es más rápido que MapReduce para el análisis de Hadoop.

Con estos principios, las siguientes secciones ofrecen algunas reglas generales para seleccionar un contexto de proceso.

### <a name="local"></a>Local

- Si la cantidad de datos que se va a analizar es pequeña y no requiere un análisis repetido, transmítalos directamente a la rutina de análisis mediante *local* o *localpar*.
- Si la cantidad de datos que se va a analizar es pequeña o mediana y requiere análisis repetido, cópielos en el sistema de archivos local, impórtelos a XDF y analícelos mediante *local* o *localpar*.

### <a name="apache-spark"></a>Spark de Apache

- Si la cantidad de datos que se va a analizar es grande, impórtelos a Spark DataFrame mediante **RxHiveData** o **RxParquetData**, o a XDF en HDFS (a no ser que el almacenamiento sea un problema), y analícelos mediante el contexto de proceso de Spark.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop MapReduce

- Utilice el contexto de proceso de MapReduce solo si encuentra un problema insuperable con el contexto de procesos de Spark, ya que generalmente es más lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ayuda insertada en rxSetComputeContext
Para más información sobre los contextos de proceso de RevoScaleR y ver algunos ejemplos, consulte la ayuda insertada en R en el método rxSetComputeContext, por ejemplo:

```console
> ?rxSetComputeContext
```

También puede consultar la [información general sobre la computación distribuida ](/machine-learning-server/r/how-to-revoscaler-distributed-computing) en la [documentación de Machine Learning Server](/machine-learning-server/).

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido las opciones que están disponibles para especificar si la ejecución se realiza o no en paralelo y, en caso afirmativo, cómo se lleva a cabo a través del nodo perimetral o del clúster de HDInsight. Para más información acerca de cómo usar ML Services con clústeres de HDInsight, consulte los temas siguientes:

- [Introducción a ML Services en Apache Hadoop](r-server-overview.md)
- [Soluciones de Azure Storage para ML Services en HDInsight](r-server-storage.md)