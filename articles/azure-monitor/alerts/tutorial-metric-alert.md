---
title: 'Tutorial: Creación de una alerta de métrica para un recurso de Azure'
description: Aprenda a crear un gráfico de métricas mediante el Explorador de métricas de Azure.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 431ef4418741a0406eeb4d350879dbc1be7ae737
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350003"
---
# <a name="tutorial-create-a-metric-alert-for-an-azure-resource"></a>Tutorial: Creación de una alerta de métrica para un recurso de Azure
Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Las reglas de alertas de métricas crean una alerta cuando el valor de métrica de un recurso de Azure supera un umbral.

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Crear una regla de alertas de métricas desde el Explorador de métricas.
> * Configurar los umbrales de alerta.
> * Crear un grupo de acciones para definir los detalles de la notificación.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesitará lo siguiente: 

- Un recurso de Azure para supervisar. Puede usar cualquier recurso de la suscripción de Azure que admita métricas. Para determinar si un recurso es compatible con las métricas, vaya a su menú en Azure Portal y busque la opción **Métricas** en la sección **Supervisión** del menú.
- Gráfico del Explorador de métricas con una o varias métricas sobre las que quiere realizar una alerta. Complete el [Tutorial: Análisis de métricas para un recurso de Azure](../essentials/tutorial-metrics.md).

## <a name="create-new-alert-rule"></a>Creación de una nueva regla de alertas
En el Explorador de métricas, haga clic en **Nueva regla de alertas**. La regla se preconfigurará con el objeto de destino y la métrica que seleccionó en el explorador de métricas.

:::image type="content" source="media/tutorial-metric-alert/new-alert-rule.png" alt-text="Nueva alerta de reglas" lightbox="media/tutorial-metric-alert/new-alert-rule.png":::

## <a name="configure-alert-logic"></a>Configuración de la lógica de alertas
El recurso ya estará seleccionado. Solo debe modificar la lógica de señales para especificar el valor del umbral y cualquier otro detalle de la regla de alerta. 

Haga clic en el **nombre de la condición** para ver esta configuración. 

:::image type="content" source="./media/tutorial-metric-alert/configuration.png" lightbox="./media/tutorial-metric-alert/configuration.png" alt-text="Configuración de reglas de alerta":::

El gráfico muestra el valor de la señal seleccionada a lo largo del tiempo para que pueda ver cuándo se habría desencadenado la alerta. Este gráfico se actualizará a medida que especifique la lógica de señal.

:::image type="content" source="./media/tutorial-metric-alert/signal-logic.png" lightbox="./media/tutorial-metric-alert/signal-logic.png" alt-text="Lógica de señal de la regla de alertas":::

La **lógica de alertas** se define mediante la condición y el tiempo de evaluación. La alerta se activa cuando esta condición es "true". Proporcione un **valor de umbral** para la regla de alerta y modifique los tipos **Operador** y **Agregación** para definir la lógica que necesita.

:::image type="content" source="./media/tutorial-metric-alert/alert-logic.png" lightbox="./media/tutorial-metric-alert/alert-logic.png" alt-text="Lógica de alerta de regla de alertas":::

Puede aceptar la granularidad de tiempo predeterminada o modificarla según sus requisitos. La **frecuencia de evaluación** define la frecuencia con la que se evalúa la lógica de alerta. La **granularidad de agregación** define el intervalo de tiempo durante el cual se agregan los valores recopilados.

Haga clic en **Listo** cuando haya terminado de configurar la lógica de señal.

## <a name="configure-actions"></a>Configuración de acciones
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>Configuración de detalles
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="./media/tutorial-metric-alert/alert-details.png" lightbox="./media/tutorial-metric-alert/alert-details.png" alt-text="Detalles de la regla de alertas":::


Haga clic en **Crear regla de alerta** para crear la regla de alerta.


## <a name="view-the-alert"></a>Visualización de la alerta
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a crear una alerta de métrica para un recurso de Azure, use uno de los siguientes tutoriales para recopilar datos de registro.

> [!div class="nextstepaction"]
> [Recopilación de registros de recursos desde un recurso de Azure](../essentials/tutorial-resource-logs.md).
> [!div class="nextstepaction"]
> [Recopilación de registros y métricas de invitado de una máquina virtual de Azure](../vm/tutorial-monitor-vm-guest.md).