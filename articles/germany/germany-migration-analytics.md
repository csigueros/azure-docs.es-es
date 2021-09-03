---
title: Migración de recursos de análisis de Azure, Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos de análisis de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 93630f536127971e922ddceaf5d1e5fa82ea3ad4
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029150"
---
# <a name="migrate-analytics-resources-to-global-azure"></a>Migración de recursos de análisis a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]


En este artículo se proporciona información que puede ayudarle a migrar los recursos de análisis de Azure desde Azure Alemania a Azure global.
  
## <a name="event-hubs"></a>Event Hubs

No puede migrar directamente recursos de Azure Event Hubs de Azure Alemania a Azure global. El servicio Event Hubs no tiene funcionalidades de exportación o importación de datos. Sin embargo, puede exportar recursos de Event Hubs [como una plantilla](../azure-resource-manager/templates/export-template-portal.md). Después, adapta la plantilla exportada para Azure global y vuelve a crear los recursos.

> [!NOTE]
> Cuando se exporta una plantilla de Event Hubs, no se copian los datos (por ejemplo, los mensajes). Exportar una plantilla solo vuelve a crear los metadatos de Event Hubs.

> [!IMPORTANT]
> Cambie la ubicación, los secretos de Azure Key Vault, los certificados y otros GUID para que sea coherente con la nueva región.

### <a name="event-hubs-metadata"></a>Metadatos de Event Hubs

Los siguientes elementos de metadatos se vuelven a crear cuando se exporta una plantilla de Event Hubs:

- Espacios de nombres
- Centros de eventos
- Grupos de consumidores
- Reglas de autorización

Para obtener más información:

- Revise la [introducción a Event Hubs](../event-hubs/event-hubs-about.md).
- Actualice sus conocimientos completando los [tutoriales de Event Hubs](../event-hubs/index.yml).
- Compruebe los pasos de migración para [Azure Service Bus](./germany-migration-integration.md#service-bus).
- Familiarícese con el procedimiento de [exportación de plantillas de Azure Resource Manager](../azure-resource-manager/templates/export-template-portal.md) o lea la introducción a [Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="hdinsight"></a>HDInsight

Para migrar los clústeres de Azure HDInsight de Azure Alemania a Azure global:

1. Detenga el clúster de HDInsight.
2. Migre los datos de la cuenta de Azure Storage a la nueva región con AzCopy u otra herramienta similar.
3. Cree nuevos recursos de proceso en Azure global y, después, asocie los recursos de almacenamiento como el almacenamiento asociado principal.

Para los clústeres más especializados y de ejecución prolongada (Kafka, Spark streaming, Storm o HBase), se recomienda coordinar la transición de cargas de trabajo a la nueva región.

Para obtener más información:

- Revise la [documentación de Azure HDInsight](../hdinsight/index.yml).
- Actualice sus conocimientos completando los [tutoriales de HDInsight](../hdinsight/index.yml).
- Para obtener ayuda con el [escalado de clústeres de HDInsight](../hdinsight/hdinsight-administer-use-powershell.md#scale-clusters), consulte [Administración de HDInsight con PowerShell](../hdinsight/hdinsight-administer-use-powershell.md).
- Aprenda a usar [AzCopy](../storage/common/storage-use-azcopy-v10.md).

## <a name="stream-analytics"></a>Stream Analytics

Para migrar los servicios Azure Stream Analytics de Azure Alemania a Azure global, vuelva a crear manualmente toda la configuración en una región de Azure global mediante Azure Portal o PowerShell. Los orígenes de entrada y salida para un trabajo de Stream Analytics pueden estar en cualquier región.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md).
- Revise la [introducción de Stream Analytics](../stream-analytics/stream-analytics-introduction.md).
- Aprenda a [crear un trabajo de Stream Analytics mediante PowerShell](../stream-analytics/stream-analytics-quick-create-powershell.md).

## <a name="sql-database"></a>SQL Database

Para migrar las cargas de trabajo más pequeñas de Azure SQL Database, utilice la función de exportación para crear un archivo BACPAC. Un archivo BACPAC es un archivo comprimido (.zip) que contiene los metadatos y los datos de la base de datos de SQL Server. Después de crear el archivo BACPAC, puede copiar el archivo en el entorno de destino (por ejemplo, mediante el uso de AzCopy) y utilizar la función de importación para recompilar la base de datos. Tenga presente las siguientes consideraciones:

- Para que una exportación sea transaccionalmente coherente, asegúrese de que se cumple una de las siguientes condiciones:
  - No se produce ninguna actividad de escritura durante la exportación.
  - Realiza la exportación desde una copia transaccionalmente coherente de la base de datos SQL.
- Para exportar a Azure Blob Storage, el tamaño del archivo BACPAC se limita a 200 GB. Para un archivo BACPAC de mayor tamaño, exporte al almacenamiento local.
- Si la operación de exportación de SQL Database tarda más de 20 horas, es posible que se cancele la operación. Consulte los artículos siguientes para obtener sugerencias sobre cómo aumentar el rendimiento.

> [!NOTE]
> La cadena de conexión cambia después de la operación de exportación porque el nombre DNS del servidor cambia durante la exportación.

Para obtener más información:

- Aprenda a [exportar una base de datos a un archivo BACPAC](../azure-sql/database/database-export.md).
- Aprenda a [importar un archivo BACPAC en una base de datos](../azure-sql/database/database-import.md).
- Revise la [documentación de Azure SQL Database](/azure/sql-database/).

## <a name="analysis-services"></a>Analysis Services

Para migrar los modelos de Azure Analysis Services de Azure Alemania a Azure global, use las [operaciones de copia de seguridad y restauración](../analysis-services/analysis-services-backup.md).

Si desea migrar solo los metadatos del modelo y no los datos, una alternativa es [volver a implementar el modelo de SQL Server Data Tools](../analysis-services/analysis-services-deploy.md).

Para obtener más información:

- Más información sobre [Copia de seguridad y restauración de Analysis Services](../analysis-services/analysis-services-backup.md).
- Revise la [introducción a Analysis Services](../analysis-services/analysis-services-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)