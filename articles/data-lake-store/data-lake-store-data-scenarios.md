---
title: Escenarios de datos relacionados con Data Lake Storage Gen1| Microsoft Docs
description: Descripción de los diferentes escenarios y herramientas mediante los cuales los datos se pueden ingerir, procesar, descargar y visualizar en Data Lake Storage Gen1 (anteriormente conocido como Azure Data Lake Store).
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: normesta
ms.openlocfilehash: f70ec43d1c44acd3505314fe3cd6664ca84d422f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603018"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Uso de Azure Data Lake Storage Gen1 para requisitos de macrodatos

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Hay cuatro fases principales en el procesamiento de macrodatos:

* Introducción de grandes cantidades de datos en un almacén de datos, en tiempo real o por lotes
* Procesamiento de los datos
* Descarga de los datos
* Visualización de los datos

En este artículo nos centramos en estas fases relativas a Azure Data Lake Storage Gen1 para comprender las opciones y herramientas disponibles para satisfacer sus necesidades de macrodatos.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Ingesta de datos en Data Lake Storage Gen1
En esta sección se resaltan los distintos orígenes de datos y las distintas formas en que esos datos se pueden ingerir en una cuenta de Data Lake Storage Gen1.

![Ingesta de datos en Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Ingesta de datos en Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Datos ad-hoc
Representan conjuntos de datos más pequeños que se utilizan para la creación de un prototipo de una aplicación de macrodatos. Hay diferentes maneras de introducir datos ad hoc, según el origen de los datos.

| Origen de datos | Introducir mediante |
| --- | --- |
| Equipo local |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[CLI de Azure](data-lake-store-get-started-cli-2.0.md)</li> <li>[Usar herramientas de Data Lake para Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Blob de Azure Storage |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[herramienta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp ejecutándose en un clúster de HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Datos de streaming
Representa los datos que se pueden generar por diversos orígenes, como aplicaciones, dispositivos o sensores, entre otros. Estos datos se pueden ingerir en Data Lake Storage Gen1 mediante distintas herramientas. Estas herramientas normalmente capturan y procesan los datos en eventos individuales y en tiempo real y, después, escriben los eventos en lotes en Data Lake Storage Gen1 para que puedan procesarse posteriormente.

A continuación, se muestran las herramientas que se pueden usar:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md): los eventos ingeridos en Event Hubs se pueden escribir en Azure Data Lake Storage Gen1 mediante una salida de Azure Data Lake Storage Gen1.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md): se pueden escribir datos directamente en Data Lake Storage Gen1 desde el clúster de Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md): se pueden recibir eventos desde Event Hubs y, después, escribirlos en Data Lake Storage Gen1 con el [SDK de .NET para Data Lake Store Gen1](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Datos relacionales
También se pueden originar datos desde bases de datos relacionales. Durante un tiempo, las bases de datos relacionales recopilan grandes cantidades de datos que pueden proporcionar información clave si se procesan a través de una canalización de macrodatos. Puede usar las herramientas siguientes para mover estos datos a Data Lake Storage Gen1.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Datos de registro de servidor web (carga mediante aplicaciones personalizadas)
Este tipo de conjunto de datos es específicamente necesario porque el análisis de los datos de registro del servidor web es un caso de uso común para aplicaciones de macrodatos y requiere que se carguen grandes volúmenes de archivos de registro en Data Lake Storage Gen1. Puede utilizar cualquiera de las herramientas siguientes para escribir sus propios scripts o aplicaciones para cargar dichos datos.

* [CLI de Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [SDK de .NET de Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Para cargar datos de registro del servidor web y también para cargar otros tipos de datos (por ejemplo, datos de opiniones sociales), un buen enfoque es escribir sus propios aplicaciones o scripts personalizados, porque le ofrece la flexibilidad de incluir el componente que carga los datos como parte de su aplicación de macrodatos mayor. En algunos casos, este código puede adoptar la forma de un script o de una utilidad de línea de comandos simple. En otros casos, el código puede utilizarse para integrar el procesamiento de macrodatos en una aplicación o solución de negocio.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Datos asociados con los clústeres de HDInsight de Azure
La mayoría de los tipos de clúster de HDInsight (Hadoop, HBase, Storm) admiten Data Lake Storage Gen1 como repositorio de almacenamiento de datos. Los clústeres de HDInsight acceden a los datos de Azure Storage Blob (WASB). Para mejorar el rendimiento, puede copiar los datos de WASB en una cuenta de Data Lake Storage Gen1 asociada con el clúster. Puede usar las herramientas siguientes para copiar los datos.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Servicio de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Datos almacenados en clústeres locales o de IaaS de Hadoop
Es posible que haya grandes cantidades de datos almacenados en clústeres de Hadoop existentes, de forma local en los equipos mediante HDFS. Los clústeres de Hadoop pueden encontrarse en una implementación local o dentro de un clúster de IaaS en Azure. Podrían existir requisitos para copiar estos datos en Azure Data Lake Storage Gen1 de forma puntual o periódica. Existen diversas opciones que puede usar para lograr esto. A continuación se muestra una lista de alternativas, además de las ventajas y desventajas asociadas.

| Enfoque | Detalles | Ventajas | Consideraciones |
| --- | --- | --- | --- |
| Usar Azure Data Factory (ADF) para copiar datos directamente desde los clústeres de Hadoop hasta Azure Data Lake Storage Gen1 |[ADF admite HDFS como origen de datos](../data-factory/connector-hdfs.md) |ADF proporciona compatibilidad inmediata con HDFS, así como una supervisión y administración integrales y de primera clase. |Requiere que se implemente Data Management Gateway localmente o en el clúster de IaaS. |
| Exportar datos desde Hadoop como archivos. Luego, copiar los archivos en Azure Data Lake Storage Gen1 con el mecanismo adecuado. |Puede copiar archivos en Azure Data Lake Storage Gen1 por medio de: <ul><li>[Azure PowerShell para SO Windows](data-lake-store-get-started-powershell.md)</li><li>[CLI de Azure](data-lake-store-get-started-cli-2.0.md)</li><li>Aplicación personalizada con cualquier SDK de Data Lake Storage Gen1</li></ul> |Se empieza rápido. Se pueden usar cargas personalizadas. |Proceso de varios pasos en el que participan varias tecnologías. La administración y la supervisión presentarán dificultades con el tiempo, dada la naturaleza personalizada de las herramientas. |
| Usar Distcp para copiar datos de Hadoop en Azure Storage. Después, copiar los datos de Azure Storage en Data Lake Store Gen1 con el mecanismo adecuado. |Puede copiar datos de Azure Storage a Data Lake Store Gen1 por medio de: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[herramienta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp ejecutándose en clústeres de HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Puede usar herramientas de código abierto. |Proceso de varios pasos en el que participan varias tecnologías. |

### <a name="really-large-datasets"></a>Conjuntos de datos realmente grandes
Para cargar conjuntos de datos cuyo tamaño oscila en varios terabytes, el uso de los métodos descritos anteriormente puede a veces resultar lento y costoso. En tales casos, puede utilizar las opciones siguientes.

* **Uso de Azure ExpressRoute**. Azure ExpressRoute permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura de un entorno local. Esto ofrece una opción confiable para transferir grandes cantidades de datos. Para obtener más información, consulte la [documentación de Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **Carga "sin conexión" de los datos**. Si por cualquier razón no es posible utilizar Azure ExpressRoute, puede usar el [servicio Azure Import/Export](../import-export/storage-import-export-service.md) para enviar unidades de disco duro con sus datos a un centro de datos de Azure. Los datos se cargan primero a Blobs de Azure Storage. Después, puede usar [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) o la [herramienta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para copiar datos de Azure Storage Blobs en Data Lake Store.

  > [!NOTE]
  > Al usar el servicio Import/Export, el tamaño de los archivos en los discos que se envían al centro de datos de Azure no debe ser mayor que 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Procesamiento de los datos almacenados en Data Lake Storage Gen1
Cuando los datos están disponibles en Data Lake Storage Gen1, puede ejecutar un análisis en esos datos mediante las aplicaciones de macrodatos admitidas. Actualmente, se puede usar Azure HDInsight y Azure Data Lake Analytics para ejecutar trabajos de análisis de datos en los datos almacenados en Data Lake Storage Gen1.

![Análisis de datos en Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Análisis de datos en Data Lake Storage Gen1")

Puede buscar en los ejemplos siguientes.

* [Creación de un clúster de HDInsight con Data Lake Storage Gen1 como almacenamiento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Use Azure Data Lake Analytics with Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md) (Uso de Azure Data Lake Analytics con Data Lake Storage Gen1)

## <a name="download-data-from-data-lake-storage-gen1"></a>Descarga de datos de Data Lake Storage Gen1
También puede descargar o mover datos de Azure Data Lake Storage Gen1 en escenarios como los siguientes:

* Mover datos a otros repositorios para interactuar con las canalizaciones de procesamiento de datos existentes. Por ejemplo, puede mover los datos de Data Lake Storage Gen1 a Azure SQL Database o a SQL Server.
* Descargar datos en el equipo local para procesarlos en entornos IDE durante la creación de prototipos de aplicaciones.

![Salida de datos desde Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Salida de datos desde Data Lake Storage Gen1")

En tales casos, puede utilizar cualquiera de las opciones siguientes.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

También puede usar los métodos siguientes para escribir su propio script o aplicación para descargar datos de Data Lake Storage Gen1.

* [CLI de Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [SDK de .NET de Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualización de datos en Data Lake Storage Gen1
Puede usar una combinación de servicios para crear representaciones visuales de los datos almacenados en Data Lake Storage Gen1.

![Visualización de datos en Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Visualización de datos en Data Lake Storage Gen1")

* Puede comenzar con [Azure Data Factory para mover datos desde Data Lake Storage Gen1 a Azure Synapse Analytics](../data-factory/copy-activity-overview.md).
* Después, puede [integrar Power BI con Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) para crear una representación visual de los datos.