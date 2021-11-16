---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 5d31cea42dd51cee65e79c44b2a44615db027aff
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298032"
---
Los datos de registro de Azure Monitor se guardan en un área de trabajo de Log Analytics. Si ya ha creado un área de trabajo en su suscripción, puede usarla. También puede elegir usar el área de trabajo predeterminada que se crea en cada suscripción de Azure. 

Si quiere crear una nueva instancia de Log Analytics, puede usar el procedimiento siguiente. Si va a usar uno existente, pase a la sección siguiente.

En la opción **Todos los servicios** de Azure Portal, seleccione **Áreas de trabajo de Log Analytics**.

:::image type="content" source="media/azure-monitor-tutorial-workspace/azure-portal.png" lightbox="media/azure-monitor-tutorial-workspace/azure-portal.png" alt-text="Seleccione Áreas de trabajo de Log Analytics en Azure Portal":::

Haga clic en **Crear** para crear una área de trabajo nueva.

:::image type="content" source="media/azure-monitor-tutorial-workspace/create-workspace.png" lightbox="media/azure-monitor-tutorial-workspace/create-workspace.png" alt-text="Botón Crear área de trabajo":::

En la pestaña **Aspectos básicos**, seleccione una **suscripción**, un **grupo de recursos** y una **región** para el área de trabajo. No es necesario que sean iguales que los recursos que se supervisan. Proporcione un **nombre** que sea único globalmente en todas las suscripciones de Azure Monitor.

:::image type="content" source="media/azure-monitor-tutorial-workspace/workspace-basics.png" lightbox="media/azure-monitor-tutorial-workspace/workspace-basics.png" alt-text="Conceptos básicos del área de trabajo":::


Haga clic en **Revisar y crear** para crear el área de trabajo.