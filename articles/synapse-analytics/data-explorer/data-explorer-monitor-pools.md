---
title: 'Inicio rápido: Uso de Synapse Studio para supervisar los grupos de Data Explorer (versión preliminar)'
description: En este artículo se proporciona información sobre la supervisión de grupos de Data Explorer.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: d127abb6e8b493e0db3a281083d9ca17802cf529
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093112"
---
# <a name="quickstart-use-synapse-studio-to-monitor-your-data-explorer-pools-preview"></a>Inicio rápido: Uso de Synapse Studio para supervisar los grupos de Data Explorer (versión preliminar)

Con Azure Synapse Analytics, puede usar Data Explorer para ejecutar consultas, paneles y mucho más en grupos de Data Explorer del área de trabajo.

En este artículo se explica cómo supervisar los grupos de Data Explorer, lo que le permite vigilar el estado de los grupos, como el número de núcleos virtuales que usan los distintos usuarios del área de trabajo.

## <a name="access-data-explorer-pools-list"></a>Acceso a la lista de grupos de Data Explorer

Para ver la lista de grupos de Data Explorer del área de trabajo, primero [abra Synapse Studio](https://web.azuresynapse.net/) y seleccione el área de trabajo.

![Inicio de sesión en el área de trabajo](../monitoring/media/common/login-workspace.png)

Una vez que haya abierto el área de trabajo, seleccione la sección **Supervisar** a la izquierda.

![Selección del centro Supervisar](../monitoring/media/common/left-nav.png)

Seleccione **Data Explorer pools** (Grupos de data Explorer) para ver la lista de grupos de Data Explorer.

![Selección de grupos de Data Explorer](media/monitor-data-explorer-pools/monitor-hub-nav-data-explorer-pools.png)

## <a name="filter-your-data-explorer-pools"></a>Filtrado de los grupos de Data Explorer

Puede filtrar la lista de grupos de Data Explorer por los que le interesen. Los filtros en la parte superior de la pantalla le permiten especificar un campo por el que desea filtrar.

Por ejemplo, puede filtrar la vista para ver solo los grupos de Data Explorer que contienen el nombre "test":

![Filtro de ejemplo](media/monitor-data-explorer-pools/filter-example.png)

## <a name="view-details-about-a-specific-data-explorer-pool"></a>Visualización de los detalles sobre un grupo de Data Explorer específico

Para ver los detalles sobre uno de los grupos de Data Explorer, seleccione el grupo para ver los detalles.

![Detalles del grupo de Data Explorer](media/monitor-data-explorer-pools/data-explorer-pool-details.png)
