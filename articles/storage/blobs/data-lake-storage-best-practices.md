---
title: Procedimientos recomendados para el uso de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Obtenga información sobre cómo optimizar el rendimiento, reducir los costes y proteger su cuenta de Azure Storage habilitada para Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 6042acd29325ab6bb887a74e47ceff115d000a9d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360038"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Procedimientos recomendados para usar Azure Data Lake Storage Gen2

En este artículo se proporcionan directrices de procedimientos recomendados que le ayudarán a optimizar el rendimiento, reducir los costes y proteger su cuenta de Azure Storage habilitada para Data Lake Storage Gen2. 

## <a name="find-documentation"></a>Localización de documentación

Azure Data Lake Storage Gen2 no es un servicio dedicado ni un tipo de cuenta. Se trata de un conjunto de funcionalidades que admiten cargas de trabajo analíticas de alto rendimiento. La documentación de Data Lake Storage Gen2 proporciona procedimientos recomendados e instrucciones para usar estas funcionalidades. Consulte el contenido de la documentación de [Blob Storage](storage-blobs-introduction.md) para ver todos los demás aspectos de la administración de cuentas, como la configuración de la seguridad de red, el diseño de alta disponibilidad y la recuperación ante desastres. 

#### <a name="evaluate-feature-support-and-known-issues"></a>Evaluación de la compatibilidad de las características y los problemas conocidos

Use el siguiente patrón a medida que configure la cuenta para usar las características de Blob Storage.

1. Revise el artículo [Compatibilidad con la característica Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md) para determinar si una característica es totalmente compatible con su cuenta. Algunas características aún no se admiten o tienen compatibilidad parcial en las cuentas habilitadas para Data Lake Storage Gen2. La compatibilidad con características siempre se expande, así que asegúrese de revisar periódicamente este artículo para ver si hay actualizaciones.

2. Revise el artículo [Problemas conocidos con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md) para ver si hay alguna limitación o guía especial sobre la característica que pretende usar.

3. Examine los artículos de características para obtener instrucciones específicas para las cuentas habilitadas para Data Lake Storage Gen2. 

#### <a name="understand-the-terms-used-in-documentation"></a>Información sobre los términos usados en la documentación

A medida que se mueve entre conjuntos de contenido, observará algunas pequeñas diferencias de terminología. Por ejemplo, el contenido destacado en la [documentación de Blob Storage](storage-blobs-introduction.md) usará el término *blob* en lugar del *archivo*. Técnicamente, los archivos que ingiere en la cuenta de almacenamiento se convierten en blobs en su cuenta. Por lo tanto, el término es correcto. Sin embargo, esto puede causar confusión si está acostumbrado al término *archivo*. También verá el término *contenedor*, que se usa para hacer referencia a un *sistema de archivos*. Puede considerar estos términos como sinónimos.

## <a name="optimize-for-data-ingest"></a>Optimización para la ingesta de datos

Al ingerir datos de un sistema de origen, el hardware de origen, el hardware de red de origen o la conectividad de red a la cuenta de almacenamiento pueden provocar un cuello de botella.  

![Diagrama en el que se muestran los factores que se deben tener en cuenta a la hora de ingerir datos desde un sistema de origen en Data Lake Storage Gen2.](./media/data-lake-storage-best-practices/bottleneck.png)

### <a name="source-hardware"></a>Hardware de origen

Tanto si usa máquinas locales como máquinas virtuales en Azure, asegúrese de seleccionar cuidadosamente el hardware adecuado. En el caso del hardware de disco, considere la posibilidad de usar unidades de estado sólido (SSD) y seleccionar los hardware de disco que tengan los ejes más rápidos. En cuanto al hardware de red, use los controladores de interfaz de red (NIC) más rápidos posibles. En Azure, se recomienda que las máquinas virtuales de Azure D14 tengan el hardware de red y de disco con la capacidad apropiada.

### <a name="network-connectivity-to-the-storage-account"></a>Conectividad de red con la cuenta almacenamiento

La conectividad de red entre los datos de origen y la cuenta datos de almacenamiento a veces puede provocar un cuello de botella. Cuando los datos de origen están en un entorno local, considere la posibilidad de usar un vínculo dedicado con [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si los datos de origen están en Azure, el rendimiento será el óptimo cuando los datos se encuentren en la misma región de Azure que Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configuración de herramientas de ingesta de datos para lograr una paralelización máxima

Para lograr el mejor rendimiento, emplee todo el rendimiento disponible realizando tantas lecturas y escrituras en paralelo como sea posible.

![Rendimiento de Data Lake Storage Gen2](./media/data-lake-storage-best-practices/throughput.png)

En la tabla siguiente se resume la configuración básica de varias herramientas de ingesta populares.  

| Herramienta               | Configuración | 
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m (mapper) |
| [Azure Data Factory](../../data-factory/copy-activity-performance.md) | parallelCopies    | 
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size, -m (mapper)   |   

> [!NOTE]
> El rendimiento general de las operaciones de ingesta depende de otros factores específicos de la herramienta que se usa para ingerir datos. Para obtener una guía actualizada, consulte la documentación de cada herramienta que vaya a utilizar.

Su cuenta se puede escalar para ofrecer el rendimiento necesario para todos los escenarios de análisis. De forma predeterminada, una cuenta Data Lake Storage Gen2 habilitada proporciona el rendimiento suficiente en su configuración predeterminada para satisfacer las necesidades de una amplia variedad de casos de uso. Si llega al límite predeterminado, la cuenta Data Lake Storage Gen2 se puede configurar para proporcionar más rendimiento. En ese caso, póngase en contacto con el equipo de [Soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

## <a name="structure-data-sets"></a>Conjuntos de datos de estructura

Considere la posibilidad de planear previamente la estructura de los datos. El formato de archivo, el tamaño de archivo y la estructura de directorios pueden afectar al rendimiento y al coste. 

### <a name="file-formats"></a>Formatos de archivo

Los datos se pueden ingerir en distintos formatos. Los datos pueden aparecer en formatos legibles por personas, como JSON, CSV o XML, o como formatos binarios comprimidos, como `.tar.gz`. Los datos también pueden tener distintos tamaños. Los datos pueden estar compuestos por archivos de gran tamaño (unos pocos terabytes), como los datos de una exportación de una tabla SQL desde los sistemas locales. Los datos también pueden tener la forma de un gran número de archivos diminutos (unos pocos kilobytes), como los datos de eventos en tiempo real de una solución del Internet de las cosas (IoT). Puede optimizar la eficacia y los costes si elige un formato y un tamaño de archivo adecuados. 

Hadoop admite un conjunto de formatos de archivo optimizados para almacenar y procesar datos estructurados. Algunos formatos comunes son: Avro, Parquet y Optimized Row Columnar (ORC). Todos estos formatos son formatos de archivo binario legibles por máquina. Se comprimen para ayudarle a administrar el tamaño del archivo. Tienen un esquema incrustado en cada archivo, lo que los hace autodescriptivos. La diferencia entre estos formatos radica en la forma en que se almacenan los datos. Avro almacena los datos en un formato basado en filas y los formatos Parquet y ORC almacenan los datos en formato de columnas.

Considere la posibilidad de utilizar el formato de archivo Avro en los casos en que los patrones de E/S incluyan más actividad de escritura o cuando los patrones de consulta favorezcan la recuperación de varias filas de registros en su totalidad. Por ejemplo, el formato Avro funciona bien con un bus de mensajes como Event Hubs o Kafka que escriben varios eventos o mensajes sucesivos.

Considere la posibilidad de utilizar formatos de archivo Parquet y ORC cuando los patrones de E/S sean más de lectura intensiva y cuando los patrones de consulta se centren en un subconjunto de columnas de los registros. Las transacciones de lectura pueden optimizarse para recuperar columnas específicas en lugar de leer todo el registro.

Apache Parquet es un formato de archivo de código abierto optimizado para canalizaciones de análisis de lectura intensa. La estructura de almacenamiento en columnas de Parquet permite omitir los datos no relevantes. Sus consultas son mucho más eficaces porque pueden limitar el ámbito de los datos que se van a enviar desde el almacenamiento hasta el motor de análisis. Además, dado que los tipos de datos similares (para una columna) se almacenan juntos, Parquet admite esquemas de codificación y compresión de datos eficaces que pueden reducir los costos de almacenamiento de datos. Servicios como [Azure Synapse Analytics](../../synapse-analytics/overview-what-is.md), [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) y [Azure Data Factory](../../data-factory/introduction.md) tienen funcionalidades nativas que aprovechan los formatos de archivo Parquet.

### <a name="file-size"></a>Tamaño de archivo

Los archivos de mayor tamaño permiten mejorar el rendimiento y reducir los costes. 

Normalmente, los motores de análisis como HDInsight tienen una sobrecarga por archivo que implica tareas como enumerar, comprobar el acceso y realizar varias operaciones de metadatos. Si los datos se almacenan en muchos archivos pequeños, puede afectar desfavorablemente al rendimiento. En general, organice los datos en archivos de mayor tamaño para mejorar el rendimiento (tamaño de 256 MB a 100 GB). Algunos motores y aplicaciones podrían tener problemas para procesar eficazmente los archivos que tienen un tamaño superior a 100 GB. 

Aumentar el tamaño del archivo también puede reducir los costes de las transacciones. Las operaciones de lectura y escritura se facturan en incrementos de 4 megabytes, por lo que se le cobrará por la operación independientemente de si el archivo contiene 4 megabytes o solo unos pocos kilobytes. Para obtener información sobre precios, consulte [Azure Data Lake Storage pricing](https://azure.microsoft.com/pricing/details/storage/data-lake/) (Precios de Azure Data Lake Storage).

A veces, las canalizaciones de datos ejercen un control limitado sobre los datos sin procesar, que tienen una gran cantidad de archivos pequeños. En general, se recomienda que el sistema tenga algún tipo de proceso para agregar archivos pequeños en otros más grandes y que así puedan usarlos aplicaciones de nivel inferior. Si está procesando datos en tiempo real, puede usar un motor de transmisión en tiempo real (como [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) o [Spark Streaming](https://databricks.com/glossary/what-is-spark-streaming)) junto con un agente de mensajes (como [Event Hub](../../event-hubs/event-hubs-about.md) o [Apache Kafka](https://kafka.apache.org/)) para almacenar los datos como archivos más grandes. A medida que agrega archivos pequeños en archivos más grandes, considere la posibilidad de guardarlos en un formato optimizado para lectura, como [Apache Parquet](https://parquet.apache.org/), para procesarlos en dirección descendente. 

### <a name="directory-structure"></a>Estructura de directorios

Aunque cada carga de trabajo tiene distintos requisitos con respecto al consumo de los datos, estos son algunos diseños habituales que deben tenerse en cuenta al trabajar con escenarios de Internet de las cosas y lotes o cuando se optimiza para los datos de series temporales.

#### <a name="iot-structure"></a>Estructura de IoT

En las cargas de trabajo de IoT, pueden ingerirse una gran cantidad de datos que abarquen numerosos productos, dispositivos, organizaciones y clientes. Es importante planificar previamente el diseño de directorios con el fin de garantizar la organización, la seguridad y un procesamiento eficaz de los datos para los consumidores de nivel inferior. Una plantilla general a tener en cuenta podría tener el siguiente diseño:

`*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*`

Por ejemplo, la telemetría de aterrizaje de un motor de un avión del Reino Unido podría ser parecida a la estructura siguiente:

`*UK/Planes/BA1293/Engine1/2017/08/11/12/*`

En este ejemplo, al colocar la fecha al final de la estructura de directorios, puede usar ACL para proteger más fácilmente regiones y asuntos para usuarios y grupos específicos. Si coloca la estructura de datos al principio, resultaría mucho más difícil proteger estas regiones y estos asuntos. Por ejemplo, si quisiera proporcionar acceso solo a los datos del Reino Unido o a determinados planos, tendría que aplicar un permiso independiente para numerosos directorios en cada directorio de hora. Esta estructura también aumentaría exponencialmente el número de directorios con el paso del tiempo.

#### <a name="batch-jobs-structure"></a>Estructura de trabajos por lotes

Un enfoque que se usa habitualmente en el procesamiento por lotes es colocar los datos en un directorio "in". Posteriormente, una vez procesados los datos, coloque los nuevos datos en un directorio "out" para que los consuman los procesos de nivel inferior. Esta estructura de directorios se usa a veces con trabajos que requieren el procesamiento de archivos individuales, pero puede que no requieran un procesamiento paralelo masivo con grandes conjuntos de datos. Al igual que en la estructura de IoT que se recomienda anteriormente, una estructura adecuada cuenta con directorios de nivel primario para elementos como regiones o asuntos (por ejemplo, organización, producto o productor). Considere la posibilidad de usar la fecha y la hora en la estructura para permitir una mejor organización, búsquedas filtradas, seguridad y automatización del procesamiento. El nivel de granularidad de la estructura de fecha viene determinado por el intervalo en el que los datos se cargan o procesan como, por ejemplo, cada hora, cada día o incluso mensualmente.

En algunas ocasiones, el procesamiento de archivos es incorrecto debido a datos dañados o a formatos imprevistos. En tales casos, podría resultar útil que la estructura de directorios tuviera una carpeta **/bad** a la que mover los archivos para inspeccionarlos más a fondo. El trabajo por lotes también puede controlar el informe o notificación de estos archivos *incorrectos* para una posterior intervención manual. Tenga en cuenta la siguiente estructura de plantilla:

`*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/*\`
`*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/*\`
`*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*`

Por ejemplo, una empresa de marketing recibe a diario extractos de datos de actualizaciones de los clientes de Norteamérica. Podría tener el aspecto del siguiente fragmento de código antes y después del procesamiento:

`*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\`
`*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*`

En el caso habitual de procesamiento de datos por lotes directamente en bases de datos como Hive o instancias tradicionales de SQL Database, no se necesitan directorios **/in** o **/out**, ya que la salida ya va a una carpeta independiente de la tabla de Hive o la base de datos externa. Por ejemplo, los extractos diarios de los clientes llegarían a sus respectivos directorios. A continuación, un servicio como [Azure Data Factory](../../data-factory/introduction.md), [Apache Oozie](https://oozie.apache.org/)o [Apache Airflow](https://airflow.apache.org/) desencadenaría un trabajo diario de Hive o Spark para procesar y escribir los datos en una tabla de Hive.

#### <a name="time-series-data-structure"></a>Estructura de datos de series temporales

En las cargas de trabajo de Hive, la eliminación de las particiones de los datos de serie temporal puede ayudar a algunas consultas a leer solo un subconjunto de los datos, lo que mejora el rendimiento.    

Aquellas canalizaciones que ingieren datos de serie temporal suelen ubicar sus archivos con una nomenclatura estructurada para los archivos y las carpetas. A continuación se muestra un ejemplo común para los datos estructurados por fecha:

*\DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv*

Observe que la información de fecha y hora aparece tanto en las carpetas como en el nombre de archivo.

Para la fecha y la hora, el siguiente es un patrón común

*\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv*

De nuevo, su elección de organización de los archivos y carpetas debería ser la que consiga un tamaño de archivo mayor y un número razonable de archivos en cada carpeta.

## <a name="set-up-security"></a>Configuración de la seguridad

Empiece por revisar las recomendaciones del artículo [Recomendaciones de seguridad para Blob Storage](security-recommendations.md). Encontrará instrucciones de procedimientos recomendados sobre cómo proteger los datos contra borrados accidentales o malintencionados, proteger los datos detrás de un firewall y utilizar Azure Active Directory (Azure AD) como base de la administración de identidades. 

A continuación, revise el artículo [Modelo de control de acceso de Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md) para obtener instrucciones específicas de las cuentas habilitadas para Data Lake Storage Gen2. Este artículo le ayuda a comprender cómo usar roles de control de acceso basado en rol (Azure RBAC) junto con las listas de control de acceso (ACL) para aplicar permisos de seguridad en los directorios y archivos de su sistema de archivos jerárquico. 

## <a name="ingest-data"></a>Ingerir datos

En esta sección se resaltan los distintos orígenes de datos y las distintas formas en que esos datos se pueden ingerir en una cuenta de Data Lake Storage Gen2.

#### <a name="ad-hoc-data"></a>Datos ad-hoc

Conjuntos de datos más pequeños que se utilizan para la creación de un prototipo de una aplicación de macrodatos. Considere la posibilidad de usar cualquiera de estas herramientas para ingerir datos: 

- Azure Portal
- [Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [CLI de Azure](data-lake-storage-directory-file-acl-cli.md)
- [REST](/rest/api/storageservices/data-lake-storage-gen2)
- [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [AzCopy](../common/storage-use-azcopy-v10.md)

#### <a name="streamed-data"></a>Datos de streaming

Generado por diversos orígenes, como aplicaciones, dispositivos y sensores. Las herramientas que se utilizan para ingerir este tipo de datos suelen capturar y procesar los datos evento por evento en tiempo real y, a continuación, escriben los eventos por lotes en su cuenta. Considere la posibilidad de usar cualquiera de estas herramientas para ingerir datos:

- [HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md)
- [Azure Stream Analytics](../../stream-analytics/stream-analytics-quick-create-portal.md)

#### <a name="relational-data"></a>Datos relacionales

Las bases de datos relacionales recopilan un gran número de registros, que pueden proporcionar información clave si se procesan a través de una canalización de big data. Se recomienda usar [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) para ingerir datos relacionales.

#### <a name="web-server-log-data"></a>Datos del registro del servidor web

Archivos de registro que contienen información, como el historial de solicitudes de página. Considere la posibilidad de escribir scripts o aplicaciones personalizados para cargar estos datos, de modo que tenga la flexibilidad de incluir su componente de carga de datos como parte de la aplicación de big data más grande. Considere la posibilidad de usar estas herramientas y SDK:

- [Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [CLI de Azure](data-lake-storage-directory-file-acl-cli.md)
- [REST](/rest/api/storageservices/data-lake-storage-gen2)
- Azure SDK ([.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), and [Node.js](data-lake-storage-directory-file-acl-javascript.md))
- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

#### <a name="hdinsight-clusters"></a>Clústeres de HDInsight

La mayoría de los tipos de clúster de HDInsight (Hadoop, HBase, Storm) admiten Data Lake Storage Gen2 como repositorio de almacenamiento de datos. Considere la posibilidad de usar cualquiera de estas herramientas para ingerir datos:

- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [AzCopy](../common/storage-use-azcopy-v10.md)

#### <a name="hadoop-clusters"></a>Clústeres Hadoop

Estos clústeres pueden ejecutarse de forma local o en la nube. Considere la posibilidad de usar cualquiera de estas herramientas para ingerir datos:

- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [WANdisco LiveData Migrator for Azure](migrate-gen2-wandisco-live-data-platform.md)
- [Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)

#### <a name="large-data-sets"></a>Conjuntos de datos grandes

Para cargar conjuntos de datos cuyo tamaño oscila en varios terabytes, el uso de los métodos descritos anteriormente puede a veces resultar lento y costoso. En esos casos, puede usar Azure ExpressRoute.  

Azure ExpressRoute permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura de un entorno local. Esto ofrece una opción confiable para transferir grandes cantidades de datos. Para más información, consulte [Información general sobre ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-and-analyze-data"></a>Proceso y análisis de los datos

Una vez que los datos están disponibles en Data Lake Storage Gen2, puede ejecutar análisis sobre esos datos, crear visualizaciones e incluso descargar datos a su máquina local o a otros repositorios, como una base de datos de Azure SQL o una instancia de SQL Server. En las secciones siguientes se recomiendan herramientas que puede usar para analizar, visualizar y descargar datos.

#### <a name="tools-for-analyzing-data"></a>Herramientas para analizar datos

- [Azure Synapse Analytics](../../synapse-analytics/get-started-analyze-storage.md)
- [HDInsight de Azure](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse)

#### <a name="tools-for-visualizing-data"></a>Herramientas para visualizar datos

- [Power BI](/power-query/connectors/datalakestorage)
- [Aceleración de consultas de Azure Data Lake Storage](data-lake-storage-query-acceleration.md)

#### <a name="tools-for-downloading-data"></a>Herramientas para descargar datos

- Azure Portal
- [PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [CLI de Azure](data-lake-storage-directory-file-acl-cli.md)
- [REST](/rest/api/storageservices/data-lake-storage-gen2)
- Azure SDK ([.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), and [Node.js](data-lake-storage-directory-file-acl-javascript.md))
- [Explorador de Azure Storage](data-lake-storage-explorer.md)
- [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)
- [Azure Data Factory](../../data-factory/copy-activity-overview.md)
- [Apache DistCp](./data-lake-storage-use-distcp.md)

## <a name="monitor-telemetry"></a>Supervisión de la telemetría

La supervisión del uso y el rendimiento es una parte importante de la puesta en marcha de su servicio. Algunos ejemplos son las operaciones frecuentes, las operaciones con latencia alta o las operaciones que provocan una limitación del servicio. 

Toda la telemetría de la cuenta de almacenamiento está disponible a través de los [registros de Azure Storage en Azure Monitor](monitor-blob-storage.md). Esta característica integra la cuenta de almacenamiento con Log Analytics y Event Hubs, al tiempo que permite archivar los registros en otra cuenta de almacenamiento. Para ver la lista completa de registros de métricas y recursos y su esquema asociado, consulte [Referencia de datos de supervisión de Azure Storage](monitor-blob-storage-reference.md).

El lugar en el que decida almacenar los registros dependerá de cómo piense acceder a ellos. Por ejemplo, si desea acceder a los registros casi en tiempo real y poder correlacionar los eventos de los registros con otras métricas de Azure Monitor, puede almacenar los registros en un área de trabajo de Log Analytics. Esto le permite consultar los registros mediante consultas KQL y de autor que enumeran la tabla `StorageBlobLogs` en el área de trabajo.

Si desea almacenar los registros tanto para consultarlos casi en tiempo real como para conservarlos a largo plazo, puede configurar las opciones de diagnóstico para enviar registros a un área de trabajo de Log Analytics y a una cuenta de almacenamiento.

Si desea acceder a los registros a través de otro motor de consultas como Splunk, puede configurar las opciones de diagnóstico para enviar registros a un centro de eventos e ingerir registros desde el centro de eventos al destino elegido.

Los registros de Azure Storage en Azure Monitor pueden habilitarse a través de las plantillas de Azure Portal, PowerShell, CLI de Azure y Azure Resource Manager. En el caso de las implementaciones a escala, Azure Policy se puede usar con compatibilidad total para las tareas de corrección. Para más información, consulte [Azure/Community-Policy](https://github.com/Azure/Community-Policy/tree/master/Policies/Storage/deploy-storage-monitoring-log-analytics) y [ciphertxt/AzureStoragePolicy](https://github.com/ciphertxt/AzureStoragePolicy).


## <a name="see-also"></a>Consulte también

- [Modelo de control de acceso de Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
- [Guía del usuario de Data Lake](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
