---
title: Inserción de datos de linaje de Data Factory en Azure Purview
description: Información sobre cómo insertar datos de linaje de Data Factory en Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 08/10/2021
ms.openlocfilehash: a49c9af5899ce847d2081d3294b32ec904e5c1f4
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228673"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Inserción de datos de linaje de Data Factory en Azure Purview (versión preliminar)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este tutorial, usará la interfaz de usuario (UI) de Data Factory para crear una canalización que ejecute actividades y transmita datos de linaje a la cuenta de Azure Purview. Después, puede ver toda la información de linaje en su cuenta de Azure Purview. 

Actualmente, el linaje se admite para las actividades de copia, flujo de datos y ejecución de SSIS. Obtenga más información sobre las funcionalidades admitidas en [Actividades admitidas de Azure Data Factory](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="prerequisites"></a>Requisitos previos

* **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.
* **Azure Data Factory**. Si no tiene ninguna instancia de Azure Data Factory, consulte el artículo sobre la [creación de una instancia de Azure Data Factory](./quickstart-create-data-factory-portal.md).
* **Cuenta de Azure Purview**. La cuenta de Purview captura todos los datos de linaje que genera la factoría de datos. Si no dispone de una cuenta de Azure Purview, consulte [Creación de una instancia de Azure Purview](../purview/create-catalog-portal.md).

## <a name="run-pipeline-and-push-lineage-data-to-azure-purview"></a>Ejecución de la canalización e inserción de datos de linaje en Azure Purview

### <a name="step-1-connect-data-factory-to-your-purview-account"></a>Paso 1: Conexión de Data Factory a la cuenta de Purview

Puede establecer la conexión entre Data Factory y la cuenta de Purview siguiendo los pasos descritos en [Conexión de Data Factory a Azure Purview](connect-data-factory-to-azure-purview.md).

### <a name="step-2-run-pipeline-in-data-factory"></a>Paso 2: Ejecución de la canalización en Data Factory

Puede crear canalizaciones, actividades de copia y actividades de flujo de datos en Data Factory. No se requiere ninguna configuración adicional para la captura de datos de linaje. Los datos de linaje se capturarán automáticamente durante la ejecución de las actividades.

:::image type="content" source="./media/data-factory-purview/adf-activities-for-lineage.png" alt-text="Captura de pantalla de la actividad de copia y flujo de datos.":::

:::image type="content" source="./media/data-factory-purview/ssis-activities-for-lineage.png" alt-text="Captura de pantalla de la actividad de ejecución de paquetes de SSIS.":::

Obtenga más información sobre cómo crear actividades de copia, flujo de datos y ejecución de SSIS en [Copia de datos desde Azure Blob Storage hasta una base de datos de Azure SQL Database mediante Azure Data Factory](./tutorial-copy-data-portal.md), [Transformación de datos mediante flujos de datos de asignación](./tutorial-data-flow.md) y [Aprovisionamiento de Azure-SSIS Integration Runtime en Azure Data Factory](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-3-monitor-lineage-reporting-status"></a>Paso 3: Supervisión del estado de los informes de linaje

Después de ejecutar la canalización, en la [vista de supervisión de la canalización](monitor-visually.md#monitor-pipeline-runs), puede comprobar el estado de los informes de linaje haciendo clic en el botón **Lineage status** (Estado del linaje) que se muestra a continuación. La misma información también está disponible en la salida JSON de la actividad -> sección `reportLineageToPurvew`.

:::image type="content" source="./media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="Supervise el estado de los informes de linaje en la vista de supervisión de la canalización.":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Paso 4: Visualización de la información de linaje en la cuenta de Purview

En la interfaz de usuario de Purview, puede examinar los recursos y elegir el tipo "Azure Data Factory". También puede buscar Data Catalog mediante palabras clave.

:::image type="content" source="./media/data-factory-purview/view-dataset.png" alt-text="Captura de pantalla del examen de recursos en Purview." lightbox="./media/data-factory-purview/view-dataset.png":::

En el recurso de actividad, haga clic en la pestaña Lineage (Linaje) y podrá ver toda la información de linaje.

- Actividad de copia:

    :::image type="content" source="./media/data-factory-purview/copy-lineage.png" alt-text="Captura de pantalla del linaje de la actividad de copia en Purview." lightbox="./media/data-factory-purview/copy-lineage.png":::

- Actividad de flujo de datos:

    :::image type="content" source="./media/data-factory-purview/dataflow-lineage.png" alt-text="Captura de pantalla del linaje del flujo de datos en Purview." lightbox="./media/data-factory-purview/dataflow-lineage.png":::

    > [!NOTE] 
    > Para el linaje de la actividad de flujo de datos, solo se admiten el origen y el receptor. Todavía no se admite el linaje de transformación del flujo de datos.

- Actividad de ejecución del paquete de SSIS:

    :::image type="content" source="./media/data-factory-purview/ssis-lineage.png" alt-text="Captura de pantalla del linaje de ejecución de SSIS en Purview." lightbox="./media/data-factory-purview/ssis-lineage.png":::

    > [!NOTE] 
    > En cuanto al linaje de la actividad de ejecución de paquetes SSIS, solo se admiten el origen y el destino. Todavía no se admite el linaje de transformación.

## <a name="next-steps"></a>Pasos siguientes

[Guía de usuario del linaje de Data Catalog](../purview/catalog-lineage-user-guide.md)

[Conexión de Data Factory a Azure Purview](connect-data-factory-to-azure-purview.md)