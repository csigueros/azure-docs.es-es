---
title: Optimizar Azure Data Lake Storage Gen2 para el rendimiento | Microsoft Docs
description: Aprenda a optimizar Azure Data Lake Storage Gen2 para el rendimiento. Ingiera datos, estructure el conjunto de datos y mucho más.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3cb875f14fe3d9b18b5249a9d2cd9e1901000610
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609315"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Optimizar Azure Data Lake Storage Gen2 para el rendimiento

Azure Data Lake Storage Gen2 permite un alto rendimiento en el movimiento de datos y el análisis de consumo de la entrada y salida. Este artículo le ayuda a optimizar el rendimiento y a acceder de forma eficaz a los datos.

> [!NOTE]
> El rendimiento general de la canalización de análisis también depende de factores específicos de los motores de análisis. Para obtener las mejores instrucciones actualizadas sobre cómo optimizar el rendimiento de las cargas de trabajo, consulte la documentación de cada sistema que desea usar.

## <a name="optimize-data-ingestion"></a>Optimización de la ingesta de datos

Al ingerir datos de un sistema de origen, el hardware de origen, el hardware de red de origen o la conectividad de red a la cuenta de almacenamiento pueden provocar un cuello de botella.

![Diagrama en el que se muestran los factores que se deben tener en cuenta a la hora de ingerir datos desde un sistema de origen en Data Lake Storage Gen2.](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

### <a name="source-hardware"></a>Hardware de origen

Tanto si usa máquinas locales como máquinas virtuales en Azure, asegúrese de seleccionar cuidadosamente el hardware adecuado. En el caso del hardware de disco, considere la posibilidad de usar unidades de estado sólido (SSD) y seleccionar los hardware de disco que tengan los ejes más rápidos. En cuanto al hardware de red, use los controladores de interfaz de red (NIC) más rápidos posibles. En Azure, se recomienda que las máquinas virtuales de Azure D14 tengan el hardware de red y de disco con la capacidad apropiada.

### <a name="network-connectivity-to-the-storage-account"></a>Conectividad de red con la cuenta almacenamiento

La conectividad de red entre los datos de origen y la cuenta datos de almacenamiento a veces puede provocar un cuello de botella. Cuando los datos de origen están en un entorno local, considere la posibilidad de usar un vínculo dedicado con [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si los datos de origen están en Azure, el rendimiento será el óptimo cuando los datos se encuentren en la misma región de Azure que Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configuración de herramientas de ingesta de datos para lograr una paralelización máxima

Para lograr el mejor rendimiento, emplee todo el rendimiento disponible realizando tantas lecturas y escrituras en paralelo como sea posible.

![Rendimiento de Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

En la tabla siguiente se resume la configuración básica de varias herramientas de ingesta populares.

| Herramienta               | Configuración |
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m (mapper) |
| [Azure Data Factory](../../data-factory/copy-activity-performance.md) | parallelCopies    |
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size, -m (mapper)   |

Su cuenta se puede escalar para ofrecer el rendimiento necesario para todos los escenarios de análisis. De forma predeterminada, una cuenta Data Lake Storage Gen2 habilitada proporciona el rendimiento suficiente en su configuración predeterminada para satisfacer las necesidades de una amplia variedad de casos de uso. Si llega al límite predeterminado, la cuenta Data Lake Storage Gen2 se puede configurar para proporcionar más rendimiento. En ese caso, póngase en contacto con el equipo de [Soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

## <a name="structure-your-data-set"></a>Estructuración del conjunto de datos

Cuando los datos se almacenan en una cuenta habilitada de Data Lake Storage Gen2, el tamaño de los archivos, su número y la estructura de carpetas afectan al rendimiento.  En la siguiente sección se describen los procedimientos recomendados en estas áreas.

### <a name="file-size"></a>Tamaño de archivo

Normalmente, los motores de análisis como HDInsight tienen una sobrecarga por archivo que implica tareas como enumerar, comprobar el acceso y realizar varias operaciones de metadatos. Si los datos se almacenan en muchos archivos pequeños, puede afectar desfavorablemente al rendimiento. En general, organice los datos en archivos de un tamaño mayor para mejorar el rendimiento (tamaño de 256 MB a 100 GB). Algunos motores y aplicaciones podrían tener problemas para procesar eficazmente los archivos que tienen un tamaño superior a 100 GB.

A veces, las canalizaciones de datos ejercen un control limitado sobre los datos sin procesar, que tienen una gran cantidad de archivos pequeños. En general, se recomienda que el sistema tenga algún tipo de proceso para agregar archivos pequeños en otros más grandes y que así puedan usarlos aplicaciones de nivel inferior. Si está procesando datos en tiempo real, puede usar un motor de transmisión en tiempo real (como [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) o [Spark Streaming](https://databricks.com/glossary/what-is-spark-streaming)) junto con un broker de mensajes (como [Event Hub](../../event-hubs/event-hubs-about.md) o [Apache Kafka](https://kafka.apache.org/)) para almacenar los datos como archivos más grandes.

A medida que agrega archivos pequeños en archivos más grandes, considere la posibilidad de guardarlos en un formato optimizado para lectura, como [Apache Parquet](https://parquet.apache.org/), para procesarlos en dirección descendente. Apache Parquet es un formato de archivo de código abierto optimizado para canalizaciones de análisis de lectura intensa. La estructura de almacenamiento en columnas de Parquet permite omitir los datos no relevantes. Las consultas son mucho más eficaces porque pueden limitar el ámbito de los datos que se van a enviar desde el almacenamiento hasta el motor de análisis. Además, dado que los tipos de datos similares (para una columna) se almacenan juntos, Parquet admite esquemas de codificación y compresión de datos eficaces que pueden reducir los costos de almacenamiento de datos. Servicios como [Azure Synapse Analytics](../../synapse-analytics/overview-what-is.md), [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) y [Azure Data Factory](../../data-factory/introduction.md) tienen funcionalidades nativas que aprovechan los formatos de archivo Parquet.

Para obtener más información sobre los formatos de datos, consulte la [sección de formato de datos del artículo de procedimientos recomendados](data-lake-storage-best-practices.md).

### <a name="organizing-time-series-data-in-folders"></a>Organización de los datos de serie temporal en carpetas

En las cargas de trabajo de Hive, la eliminación de las particiones de los datos de serie temporal puede ayudar a algunas consultas a leer solo un subconjunto de los datos, lo que mejora el rendimiento.

Aquellas canalizaciones que ingieren datos de serie temporal suelen ubicar sus archivos con una nomenclatura muy estructurada para los archivos y las carpetas. A continuación se muestra un ejemplo muy común para los datos estructurados por fecha:

*\DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv*

Observe que la información de fecha y hora aparece tanto en las carpetas como en el nombre de archivo.

Para la fecha y la hora, el siguiente es un patrón común

*\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv*

De nuevo, su elección de organización de los archivos y carpetas debería ser la que consiga un tamaño de archivo mayor y un número razonable de archivos en cada carpeta.

Para otras sugerencias de estructura de diseño de directorios, consulte [Estructura de directorios](data-lake-storage-best-practices.md#directory-layout-considerations).

### <a name="access-data-efficiently-with-query-acceleration"></a>Acceso a los datos de forma eficaz con la aceleración de consultas

La aceleración de consultas permite a las aplicaciones y los marcos de análisis optimizar considerablemente el procesamiento de datos mediante la recuperación de solo los datos necesarios para realizar una operación determinada. Esto reduce el tiempo y la capacidad de procesamiento necesarios para obtener información fundamental sobre los datos almacenados.

La aceleración de consultas acepta el filtrado de predicados y proyecciones de columnas que permiten a las aplicaciones filtrar filas y columnas en el momento en que se leen los datos del disco. Solo los datos que cumplen las condiciones de un predicado se transfieren a la aplicación a través de la red. Esto reduce la latencia de red y el costo de proceso.

Para obtener más información, consulte [Aceleración de consultas de Azure Data Lake Storage](data-lake-storage-query-acceleration.md).

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimización de trabajos con uso intensivo de operaciones de E/S en las cargas de trabajo de Hadoop y Spark en HDInsight

Ese tipo de trabajos dedican la mayor parte de su tiempo a realizar operaciones de E/S.  Un ejemplo común es un trabajo de copia que solo realice operaciones de lectura y escritura.  Otros ejemplos son los trabajos de preparación de datos que leen una gran cantidad de datos, realizan alguna transformación en ellos y luego los escriben de nuevo en el almacén.  Puede tener un trabajo que lee o escribe hasta 100 MB en una sola operación, pero almacenar en búfer dicho tamaño puede comprometer el rendimiento. Para optimizar el rendimiento, intente mantener el tamaño de una operación de E/S entre 4 MB y 16 MB.

### <a name="io-intensive-jobs-with-hdinsight-clusters"></a>Trabajos intensivos de E/S con clústeres de HDInsight

- **Versiones de HDInsight.** Para obtener el mejor rendimiento, utilice la versión más reciente de HDInsight.
- **Regiones.** Coloque la cuenta de Data Lake Storage Gen2 en la misma región que el clúster de HDInsight.

Un clúster de HDInsight se compone de dos nodos principales y algunos nodos de trabajo. Cada nodo de trabajo proporciona una cantidad específica de núcleos y de memoria, lo que se determina según el tipo de máquina virtual.  Cuando se ejecuta un trabajo, YARN es el negociador de recursos que asigna la memoria disponible y los núcleos para crear los contenedores.  Cada contenedor ejecuta las tareas necesarias para completar el trabajo.  Los contenedores se ejecutan en paralelo para procesar las tareas rápidamente. Por lo tanto, el rendimiento se mejora ejecutando tantos contenedores en paralelo como sea posible.

Hay tres niveles dentro de un clúster de HDInsight que se pueden optimizar para aumentar el número de contenedores y usar toda la capacidad de proceso disponible.

- **Nivel físico**
- **Nivel de YARN**
- **Nivel de carga de trabajo**

### <a name="physical-layer"></a>Nivel físico

**Ejecute el clúster con más nodos o máquinas virtuales de un tamaño mayor.**  Un clúster mayor le permitirá ejecutar más contenedores de YARN, tal como se muestra en la ilustración siguiente.

![Diagrama en el que se muestra cómo un clúster mayor le permitirá ejecutar más contenedores de YARN.](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Use máquinas virtuales con más ancho de banda de red.**  La cantidad de ancho de banda de red puede ser un cuello de botella, si hay menos de lo que necesita la capacidad de proceso de Data Lake Storage Gen2.  Diferentes máquinas virtuales tendrán varios tamaños de ancho de banda de red.  Elija un tipo de máquina virtual que tenga el mayor ancho de banda de red posible.

### <a name="yarn-layer"></a>Nivel de YARN

**Use contenedores de YARN menores.**  Reduzca el tamaño de cada contenedor de YARN para crear más contenedores con la misma cantidad de recursos.

![Diagrama en el que se muestra el resultado al reducir el tamaño de cada contenedor de YARN para crear más contenedores.](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

En función de la carga de trabajo, siempre habrá un tamaño de contenedor de YARN mínimo que se necesite. Si elige un contenedor demasiado pequeño, los trabajos no tendrán memoria suficiente. Normalmente, los contenedores de YARN no deben ser menores de 1 GB. Es habitual ver contenedores de YARN de 3 GB. Para algunas cargas de trabajo, puede que necesite contenedores de YARN mayores.

**Aumente los núcleos para cada contenedor de YARN.**  Aumente el número de núcleos asignados a cada contenedor para incrementar las tareas en paralelo que se ejecutan en cada uno.  Funciona en las aplicaciones como Spark que ejecutan varias tareas por contenedor.  En las aplicaciones como Hive que ejecutan un único subproceso en cada contenedor, es mejor tener varios contenedores en lugar de más núcleos por contenedor.

### <a name="workload-layer"></a>Nivel de carga de trabajo

**Use todos los contenedores disponibles.**  Establezca el número de tareas para que sea igual o mayor que el número de contenedores disponibles de modo que se usen todos los recursos.

![Diagrama en el que se muestra el uso de todos los contenedores.](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Los errores de las tareas son costosos.** Si cada tarea tiene una gran cantidad de datos que se van a procesar, el error de una tarea provoca un reintento que resulta caro.  Por lo tanto, es mejor que cree más tareas y que cada una procese una pequeña cantidad de datos.

Además de las directrices generales anteriores, cada sistema o marco de análisis tiene parámetros diferentes que puede optimizar para obtener un rendimiento óptimo. Para obtener las mejores instrucciones actualizadas sobre cómo optimizar el rendimiento de las cargas de trabajo, consulte la documentación de cada sistema que desea usar.

## <a name="see-also"></a>Consulte también

- [Procedimientos recomendados para usar Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)
- [Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
