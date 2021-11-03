---
title: Servicios de Azure que admiten Azure Data Lake Storage Gen2 | Microsoft Docs
description: Obtenga información sobre qué servicios de Azure se integran con Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 284971b1b9a54d21f29cf3c3c81c194be1f5071c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022500"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Servicios de Azure que admiten Azure Data Lake Storage Gen2

Puede usar los servicios de Azure para ingerir datos, realizar análisis y crear representaciones visuales. En este artículo se proporciona una lista de los servicios de Azure admitidos, se revela su nivel de compatibilidad y se proporcionan vínculos a artículos que le ayudan a usar estos servicios con Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Servicios de Azure compatibles

En esta tabla se enumeran los servicios de Azure que puede usar con Azure Data Lake Storage Gen2. Los elementos que aparecen en estas tablas van a cambiar con el tiempo a medida que se siga ampliando la compatibilidad.

> [!NOTE]
> El nivel de compatibilidad solo se refiere al modo en que se admite el servicio con Data Lake Storage Gen 2.

|Servicio de Azure |Nivel de compatibilidad |Azure AD |Clave compartida| Artículos relacionados |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Disponibilidad general|Sí|Sí|[Carga de datos en Azure Data Lake Storage Gen2 con Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponibilidad general|Sí|Sí|[Uso con Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) <br> [Tutorial: Extracción, transformación y carga de datos mediante Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse) <br>[Tutorial: Acceso a los datos de Azure Data Lake Storage Gen2 con Azure Databricks mediante Spark](data-lake-storage-use-databricks-spark.md)|
|Centro de eventos de Azure|Disponibilidad general|No|Sí|[Captura de eventos a través de Azure Event Hubs en Azure Blob Storage o Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md)|
|Azure Event Grid|Disponibilidad general|Sí|Sí|[Tutorial: Implementación del patrón de captura de Data Lake para actualizar una tabla de Databricks Delta](data-lake-storage-events.md)|
|Azure Logic Apps|Disponibilidad general|No|Sí|[Introducción: ¿Qué es Azure Logic Apps?](../../logic-apps/logic-apps-overview.md)|
|Azure Machine Learning|Disponibilidad general|Sí|Sí|[Acceso a los datos en los servicios de almacenamiento de Azure](../../machine-learning/how-to-access-data.md)|
|Azure Stream Analytics|Disponibilidad general|Sí|Sí|[Inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure Portal](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Salida a Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Data Box|Disponibilidad general|No|Sí|[Uso de Azure Data Box para migrar datos de un almacén HDFS local a Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Disponibilidad general|Sí|Sí|[Uso de Data Lake Storage Gen2 con clústeres de Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)<br>[Uso de la CLI de HDFS con Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutorial: Extracción, transformación y carga de datos mediante Apache Hive en Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Disponibilidad general|Sí|Sí|[Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión](../../iot-hub/iot-hub-devguide-messages-d2c.md)|
|Power BI|Disponibilidad general|Sí|Sí|[Análisis de datos en Data Lake Storage Gen2 mediante Power BI](/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (anteriormente SQL Data Warehouse)|Disponibilidad general|Sí|Sí|[Análisis de datos en una cuenta de almacenamiento](../../synapse-analytics/get-started-analyze-storage.md)|
|SQL Server Integration Services (SSIS)|Disponibilidad general|Sí|Sí|[Administrador de conexiones de Azure Storage](/sql/integration-services/connection-manager/azure-storage-connection-manager)|
|Explorador de datos de Azure|Disponibilidad general|Sí|Sí|[Consulta de datos en Azure Data Lake con Azure Data Explorer](/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|Versión preliminar|Sí|Sí|[Indexación y búsqueda de documentos de Azure Data Lake Storage Gen2 (versión preliminar)](../../search/search-howto-index-azure-data-lake-storage.md)|
|Azure Content Delivery Network|Todavía no se admite|No aplicable|No aplicable|[Indexación y búsqueda de documentos de Azure Data Lake Storage Gen2 (versión preliminar)](../../cdn/cdn-overview.md)|
|Azure SQL Database|Todavía no se admite|No aplicable|No aplicable|[¿Qué es Azure SQL Database?](../../azure-sql/database/sql-database-paas-overview.md)|

> [!TIP]
> Para ver cómo se organizan los servicios en categorías como la ingesta, descarga, proceso y visualización, consulte [Ingesta, proceso y análisis](./data-lake-storage-best-practices.md#ingest-process-and-analyze).

## <a name="see-also"></a>Consulte también

- [Problemas conocidos con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Compatibilidad con la característica Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md)
- [Plataformas de código abierto que admiten Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acceso multiprotocolo en Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
- [Procedimientos recomendados para usar Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)