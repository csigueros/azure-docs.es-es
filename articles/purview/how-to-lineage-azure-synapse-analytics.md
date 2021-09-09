---
title: Metadatos y linaje de Azure Synapse Analytics
description: En este artículo se explica cómo conectar Azure Synapse Analytics y Azure Purview para realizar un seguimiento del linaje de datos.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/10/2021
ms.openlocfilehash: a6ff17795cfb65ddc5dc8b3bf8c55d3d8084efc8
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228881"
---
# <a name="how-to-get-lineage-from-azure-synapse-analytics-into-azure-purview"></a>Procedimiento para obtener el linaje de Azure Synapse Analytics en Azure Purview

En este documento se explican los pasos necesarios para conectar un área de trabajo de Azure Synapse con una cuenta de Azure Purview para realizar un seguimiento del linaje de datos. El documento también profundiza en el ámbito de cobertura y las funcionalidades de linaje admitidas.

## <a name="supported-azure-synapse-capabilities"></a>Funcionalidades de Azure Synapse admitidas

Actualmente, Azure Purview captura el linaje en tiempo de ejecución de las siguientes actividades de canalización de Azure Synapse:

- [Copiar datos](../data-factory/copy-activity-overview.md)

> [!IMPORTANT]
> Azure Purview anula el linaje si el origen o el destino usan un sistema de almacenamiento de datos no admitido.

[!INCLUDE[azure-synapse-supported-activity-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

## <a name="bring-azure-synapse-lineage-into-purview"></a>Incorporación del linaje de Azure Synapse a Purview

### <a name="step-1-connect-azure-synapse-workspace-to-your-purview-account"></a>Paso 1: Conexión del área de trabajo de Azure Synapse a la cuenta de Purview

Puede conectar un área de trabajo de Azure Sysnpase a Purview y la conexión permitirá a Azure Synapse insertar información de linaje en Purview. Siga los pasos que se detallan en [Conexión de una cuenta de Azure Purview en Synapse](../synapse-analytics/catalog-and-governance/quickstart-connect-azure-purview.md). Varias áreas de trabajo de Azure Synapse pueden conectarse a una sola cuenta de Azure Purview para realizar un seguimiento holístico del linaje.

### <a name="step-2-run-pipeline-in-azure-synapse-workspace"></a>Paso 2: Ejecución de la canalización en el área de trabajo de Azure Synapse

Puede crear canalizaciones con la actividad de copia en el área de trabajo de Azure Synapse. No se requiere ninguna configuración adicional para la captura de datos de linaje. Los datos de linaje se capturarán automáticamente durante la ejecución de las actividades.

### <a name="step-3-monitor-lineage-reporting-status"></a>Paso 3: Supervisión del estado de los informes de linaje

Después de ejecutar la canalización de Azure Synapse, en la vista de supervisión de la canalización de Synapse, puede comprobar el estado de los informes de linaje haciendo clic en el botón **Lineage status** (Estado del linaje). La misma información también está disponible en el resultado de la actividad JSON -> sección `reportLineageToPurvew`.

:::image type="content" source="../data-factory/media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="Supervise el estado de los informes de linaje en la vista de supervisión de la canalización.":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Paso 4: Visualización de la información de linaje en la cuenta de Purview

En la cuenta de Purview, puede examinar los recursos y elegir el tipo "Azure Synapse Analytics". También puede buscar Data Catalog mediante palabras clave.

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-assets.png" alt-text="Examinar los recursos de Azure Synapse en Purview":::

Seleccione la cuenta de Synapse -> canalización -> actividad y podrá ver la información de linaje.

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png" alt-text="Examine el linaje de canalización de Azure Synapse en Purview." lightbox="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png":::

## <a name="next-steps"></a>Pasos siguientes

[Guía de usuario del linaje de Data Catalog](catalog-lineage-user-guide.md)

[Vínculo a Azure Data Share para linaje](how-to-link-azure-data-share.md)
