---
title: Integración de Data Lake Storage Gen1 con otros servicios de Azure
description: Descripción de cómo se integra Azure Data Lake Storage Gen1 con otros servicios de Azure.
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: a5d5c67b89d35284a4c441ac87d9ae467edb5aa5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602847"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integración de Azure Data Lake Storage Gen1 con otros servicios de Azure
El Azure Data Lake Storage Gen1 puede usarse junto con otros servicios de Azure para habilitar una gama más amplia de escenarios. En el siguiente artículo se enumeran los servicios con los que puede integrarse Data Lake Storage Gen1.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Uso de Data Lake Storage Gen1 con Azure HDInsight
Puede aprovisionar un clúster de [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que use Data Lake Storage Gen1 como almacenamiento compatible con HDFS. Para esta versión, para los clústeres Hadoop y Storm en Windows y Linux, puede usar el Data Lake Storage Gen1 solo como almacenamiento adicional. Dichos clústeres todavía usan Azure Storage (WASB) como almacenamiento predeterminado. Sin embargo, para los clústeres de HBase en Windows y Linux, puede usar Data Lake Storage Gen1 como almacenamiento predeterminado, como almacenamiento adicional o ambos.

Para obtener instrucciones sobre cómo aprovisionar un clúster de HDInsight con Data Lake Storage Gen1, consulte:

* [Aprovisionamiento de un clúster de HDInsight con Data Lake Storage Gen1 mediante Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aprovisionamiento de un clúster de HDInsight con Data Lake Storage Gen1 como almacenamiento predeterminado mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Aprovisionamiento de un clúster de HDInsight con Data Lake Storage Gen1 como almacenamiento adicional mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Uso de Data Lake Storage Gen1 con Azure Data Lake Analytics
[Análisis de Azure Data Lake](../data-lake-analytics/data-lake-analytics-overview.md) le permite trabajar con macrodatos a una escala de nube. Dinámicamente aprovisiona recursos y le permite realizar análisis en terabytes o incluso exabytes de datos que pueden almacenarse en un número de orígenes de datos admitidos, siendo uno de ellos Data Lake Storage Gen1. Data Lake Analytics está especialmente optimizado para trabajar con Data Lake Storage Gen1: proporciona el nivel máximo de rendimiento, el procesamiento y la ejecución en paralelo para las cargas de trabajo de macrodatos.

Para obtener instrucciones sobre cómo usar el Data Lake Analytics con Data Lake Storage Gen1, consulte [Introducción a Análisis de Data Lake con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Uso de Data Lake Storage Gen1 con Azure Data Factory
Puede usar [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para recopilar datos de tablas de Azure, Azure SQL Database, Azure SQL Warehouse, Azure Storage Blob y bases de datos locales. Al ser considerado un elemento de primera clase en el ecosistema de Azure, Azure Data Factory puede usarse para orquestar la recopilación de datos desde esos orígenes a Data Lake Storage Gen1.

Para obtener instrucciones sobre cómo usar Azure Data Factory con Data Lake Storage Gen1, consulte [Desplazamiento de datos hacia y desde Data Lake Storage Gen1 mediante Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Copia de datos de los blobs de Azure Storage en Data Lake Storage Gen1
Azure Data Lake Storage Gen1 proporciona una herramienta de línea de comandos, AdlCopy, que le permite copiar datos desde Azure Blob Storage en una cuenta de Data Lake Storage Gen1. Para más información, consulte [Copia de datos de Azure Storage Blob en Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Copia de datos entre Azure SQL Database y Data Lake Storage Gen1
Puede usar Apache Sqoop para importar y exportar datos entre Azure SQL Database y Data Lake Storage Gen1. Para más información, consulte [Copia de datos entre Data Lake Storage Gen1 y Azure SQL Database mediante Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Uso de Data Lake Storage Gen1 con Stream Analytics
Puede utilizar Data Lake Storage Gen1 como una de las salidas para almacenar los datos que se transmiten mediante Azure Stream Analytics. Para más información, consulte [Transmisión de datos de Azure Storage Blob a Data Lake Storage Gen1 mediante Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Uso de Data Lake Storage Gen1 con Power BI
Puede usar Power BI para importar datos desde una cuenta de Data Lake Storage Gen1 para analizar y visualizar los datos. Para más información, consulte [Análisis de datos en Data Lake Storage Gen1 mediante Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Uso de Data Lake Storage Gen1 con Data Catalog
Puede registrar los datos desde Data Lake Storage Gen1 a Azure Data Catalog para que los datos se puedan reconocer en toda la organización. Para más información, consulte [Registro de datos de Data Lake Storage Gen1 en Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Uso de Data Lake Storage Gen1 con SQL Server Integration Services (SSIS)
Puede usar el Administrador de conexiones de Data Lake Storage Gen1 en SSIS para conectar un paquete de SSIS a Data Lake Storage Gen1. Para más información, consulte [Uso de Data Lake Storage Gen1 con SSIS](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Uso de Data Lake Storage Gen1 con Azure Synapse Analytics
Puede usar PolyBase para cargar datos de Data Lake Storage Gen1 en Azure Synapse Analytics. Para más información, consulte [Uso de Data Lake Storage Gen1 con Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Uso de Data Lake Storage Gen1 con Azure Event Hubs
Puede usar Azure Data Lake Storage Gen1 para archivar y capturar datos recibidos por Azure Event Hubs. Para más información, vea [Uso de Data Lake Storage Gen1 con Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Consulte también
* [Introducción a Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introducción a Data Lake Storage Gen1 con Portal](data-lake-store-get-started-portal.md)
* [Introducción a Data Lake Storage Gen1 con PowerShell](data-lake-store-get-started-powershell.md)