---
title: 'Visualización de métricas en Azure Database for PostgreSQL: Hiperescala (Citus)'
description: 'Cómo acceder a las métricas de base de datos en Azure Database for PostgreSQL: Hiperescala (Citus)'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/05/2021
ms.openlocfilehash: b698a69631ba2ad8aba58f6de17d8cbee1cbafaf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620874"
---
# <a name="how-to-view-metrics-in-azure-database-for-postgresql---hyperscale-citus"></a>Cómo ver métricas de Azure Database for PostgreSQL: Hiperescala (Citus)

Las métricas de recursos están disponibles para cada nodo de un grupo de servidores de Hiperescala (Citus) y, de forma agregada, en todos los nodos.

## <a name="view-metrics"></a>Visualización de métricas

Para acceder a las métricas de un grupo de servidores de Hiperescala (Citus), abra **Métricas** en **Supervisión** en Azure Portal.

:::image type="content" source="media/howto-hyperscale-monitoring/metrics.png" alt-text="Pantalla de métricas":::

Elija una dimensión y una agregación, por ejemplo, **porcentaje de CPU** y **Máx.** , para ver la métrica agregada de todos los nodos. Para ver una explicación de cada métrica, consulte [aquí](concepts-hyperscale-monitoring.md#list-of-metrics).

:::image type="content" source="media/howto-hyperscale-monitoring/dimensions.png" alt-text="Seleccionar dimensión":::

### <a name="view-metrics-per-node"></a>Visualización de métricas por nodo

La visualización de las métricas de cada nodo por separado en el mismo grafo se denomina "separación".
Para habilitarla, seleccione **Apply splitting** (Aplicar separación):

:::image type="content" source="media/howto-hyperscale-monitoring/splitting.png" alt-text="Botón Aplicar separación":::

Seleccione el valor por el que realizar la separación. Para los nodos de Hiperescala (Citus), elija **Nombre del servidor**.

:::image type="content" source="media/howto-hyperscale-monitoring/split-value.png" alt-text="Selección del valor de separación":::

Las métricas se trazarán ahora en una línea codificada por colores por cada nodo.

:::image type="content" source="media/howto-hyperscale-monitoring/split-chart.png" alt-text="Varias líneas de gráfico de nodo":::

## <a name="next-steps"></a>Pasos siguientes

* Revise los [conceptos de supervisión](concepts-hyperscale-monitoring.md) de Hiperescala (Citus).
