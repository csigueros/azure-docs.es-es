---
title: Creación de un flujo de datos de asignación
description: Cómo crear un flujo de datos de asignación de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 1c47e42f3186d573fb57f1ebaa89140e0c713c0e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614613"
---
# <a name="create-azure-data-factory-data-flows"></a>Creación de un flujo de datos de Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Los flujos de datos de asignación proporcionan una manera de transformar los datos a escala sin necesidad de programar. Puede diseñar un trabajo de transformación de datos en el diseñador de flujos de datos realizando una serie de transformaciones. Comience realice todas las transformaciones que desee en el origen y, después, continúe con los pasos de transformación de datos. A continuación, complete el flujo de datos con los receptores para enviar los resultados a un destino.

## <a name="steps-to-create-a-new-data-flow"></a>Pasos para crear un nuevo flujo de datos

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

Primero, [cree una factoría de datos V2](quickstart-create-data-factory-portal.md) en Azure Portal. Después de crear la factoría, seleccione el icono **Abrir Azure Data Factory Studio** en el portal para iniciar Data Factory Studio.

:::image type="content" source="media/data-flow-create/open-data-factory-studio-from-portal.png" alt-text="Muestra una captura de pantalla de cómo abrir Data Factory Studio desde Azure Portal.":::

Puede agregar flujos de datos de ejemplo desde la galería de plantillas. Para examinar la galería, seleccione la pestaña **Autor** en Data Factory Studio y haga clic en el signo más para elegir **Canalización** | **Galería de plantillas**.

:::image type="content" source="media/data-flow-create/open-template-gallery-from-data-factory.png" alt-text="Muestra cómo abrir la galería de plantillas en Data Factory.":::

Seleccione la categoría Data Flow para elegir entre las plantillas disponibles.

:::image type="content" source="media/data-flow-create/template-gallery-filtered-for-data-flow.png" alt-text="Muestra la galería de plantillas filtrada por flujos de datos.":::

También puede agregar flujos de datos directamente a la factoría de datos sin usar una plantilla. Seleccione la pestaña **Autor** en Data Factory Studio y haga clic en el signo más para elegir **Flujo de datos** | **Flujo de datos**.  

:::image type="content" source="media/data-flow-create/create-data-flow-directly.png" alt-text="Muestra una captura de pantalla de cómo crear directamente un flujo de datos vacío.":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

Primero, [cree un área de trabajo de Synapse](../synapse-analytics/quickstart-create-workspace.md) desde Azure Portal. Después de crear la nueva área de trabajo, seleccione el icono **Abrir Azure Studio** para iniciar la interfaz de usuario de Data Factory.
    
:::image type="content" source="media/data-flow-create/open-synapse-studio-from-portal.png" alt-text="Muestra una captura de pantalla de cómo abrir Synapse Studio desde Azure Portal.":::

Puede agregar flujos de datos de ejemplo desde la galería de plantillas.  Para examinar la galería, seleccione la pestaña **Integrar** en Synapse Studio y haga clic en el signo más para elegir **Examinar galería**.

:::image type="content" source="media/data-flow-create/open-template-gallery-from-synapse.png" alt-text="Muestra cómo abrir la galería de plantillas en Data Factory.":::

Filtre por "Categoría: Flujo de datos" para elegir entre las plantillas disponibles.

:::image type="content" source="media/data-flow-create/synapse-template-gallery-filtered-for-data-flow.png" alt-text="Muestra la galería de plantillas filtrada por flujos de datos.":::

También puede agregar flujos de datos directamente al área de trabajo sin usar una plantilla. Seleccione la pestaña **Integrar** en Synapse Studio y haga clic en el signo más para elegir **Canalización**.  A continuación, en la canalización, expanda la opción **Mover y transformar** de la sección Actividades y arrastre un **flujo de datos** al lienzo de la canalización.

:::image type="content" source="media/data-flow-create/create-pipeline-in-synapse.png" alt-text="Muestra una captura de pantalla de cómo crear directamente una canalización vacía.":::

:::image type="content" source="media/data-flow-create/add-data-flow-to-pipeline-synapse.png" alt-text="Muestra una captura de pantalla de cómo agregar un flujo de datos vacío a una canalización directamente.":::

---

## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Transformación de datos mediante flujos de datos de asignación](tutorial-data-flow.md)
* Empiece a crear la transformación de datos con una [transformación de origen](data-flow-source.md).
