---
title: Linaje de SQL Server Integration Services
description: En este artículo se describe la extracción del linaje de datos de SQL Server Integration Services.
author: chugugrace
ms.author: chugu
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 2bdcdf79ee28bf6a3c233decd6dceafce74276ef
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258377"
---
# <a name="how-to-get-lineage-from-sql-server-integration-services-ssis-into-azure-purview"></a>Obtención del linaje de SQL Server Integration Services (SSIS) en Azure Purview

En este artículo se detallan los aspectos del linaje de datos de SQL Server Integration Services (SSIS) en Azure Purview.

## <a name="prerequisites"></a>Requisitos previos

- [Migrar cargas de trabajo de SQL Server Integration Services a la nube mediante lift-and-shift](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

## <a name="supported-scenarios"></a>Escenarios admitidos

El ámbito actual de compatibilidad incluye la extracción del linaje de los paquetes SSIS ejecutados por el entorno de ejecución de integración SSIS de Azure Data Factory.

Todavía no se admite la extracción del linaje de SSIS local.

### <a name="supported-data-stores"></a>Almacenes de datos compatibles

| Almacén de datos | Compatible |
| ------------------- | ------------------- |
| Azure Blob Storage | Sí |
| Azure Data Lake Storage Gen1 | Sí |
| Azure Data Lake Storage Gen2 | Sí |
| Azure Files | Sí |
| Azure SQL Database \* | Sí |
| Azure SQL Managed Instance \*| Sí |
| Azure Synapse Analytics \* | Sí |
| SQL Server \* | Sí |

*\* Actualmente, Azure Purview no admite la consulta ni el procedimiento almacenado para el linaje o el examen. El linaje se limita a los orígenes de tabla y vista.*


## <a name="how-to-bring-ssis-lineage-into-purview"></a>Cómo llevar el linaje de SSIS a Purview

### <a name="step-1-connect-a-data-factory-to-azure-purview"></a>Paso 1. [Conexión de Data Factory a Azure Purview](how-to-link-azure-data-factory.md)

### <a name="step-2-trigger-ssis-activity-execution-in-azure-data-factory"></a>Paso 2. Desencadenamiento de la ejecución de la actividad de SSIS en Azure Data Factory

Puede [ejecutar el paquete SSIS con la actividad de ejecución de paquetes de SSIS](../data-factory/how-to-invoke-ssis-package-ssis-activity.md) o puede [ejecutar el paquete SSIS con Transact-SQL en ADF SSIS Integration Runtime](../data-factory/how-to-invoke-ssis-package-stored-procedure-activity.md).  

Una vez que la actividad de ejecución de paquetes de SSIS finaliza la ejecución, puede comprobar el estado del informe de linaje en la salida de la actividad en el [monitor de actividades de Data Factory](../data-factory/monitor-visually.md#monitor-activity-runs).
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/activity-report-lineage-status.png" alt-text="ssis-status":::

### <a name="step-3-browse-lineage-information-in-your-azure-purview-account"></a>Paso 3. Examen de la información de linaje en la cuenta de Azure Purview

- Puede examinar Data Catalog mediante la selección del tipo de recurso "SQL Server Integration Services".

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png" alt-text="Recursos del explorador" lightbox="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-2.png" alt-text="browser-assets-ssis":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-3.png" alt-text="browse-assets-ssis-package":::

- También puede buscar Data Catalog mediante palabras clave.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/search-assets.png" alt-text="search-ssis" lightbox="media/how-to-lineage-sql-server-integration-services/search-assets.png":::

- Puede ver la información de linaje de una actividad de ejecución de paquetes de SSIS y abrirla en Data Factory para ver o editar la configuración de la actividad.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage.png" alt-text="show-ssis-lineage" lightbox="media/how-to-lineage-sql-server-integration-services/lineage.png":::

- Puede elegir un origen de datos para profundizar en cómo se asignan las columnas del origen a las columnas del destino.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png" alt-text="show-ssis-lineage-drill-in" lightbox="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png":::

## <a name="next-steps"></a>Pasos siguientes

- [Migrar cargas de trabajo de SQL Server Integration Services a la nube mediante lift-and-shift](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Obtener información sobre el linaje de datos en Azure Purview](catalog-lineage-user-guide.md)
- [Vinculación de Azure Data Factory para insertar linaje automatizado](how-to-link-azure-data-factory.md)