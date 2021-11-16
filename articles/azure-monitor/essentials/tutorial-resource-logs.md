---
title: 'Tutorial: Recopilación de registros de recursos desde un recurso de Azure'
description: Tutorial para configurar los valores de diagnóstico para enviar registros de recurso de un recurso de Azure en un área de trabajo de Log Analytics donde se puedan analizar con una consulta de registro.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 11/08/2021
ms.openlocfilehash: 90a1f193bf1f136e4d3501b68bfab8346604fbbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297984"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Tutorial: Recopilación y análisis de registros de recurso desde un recurso de Azure
Los registros de recurso proporcionan información minuciosa sobre los detalles de funcionamiento de un recurso de Azure y son útiles para supervisar su estado y disponibilidad. Los recursos de Azure generan registros de recursos automáticamente, pero debe crear una configuración de diagnóstico para recopilarlos. Este tutorial le guía por el proceso de creación de una configuración de diagnóstico para enviar registros de recursos a un área de trabajo de Log Analytics donde puede analizarlos con consultas de registro.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un área de trabajo de Log Analytics en Azure Monitor
> * Crear una configuración de diagnóstico para recopilar registros de recurso 
> * Crear una consulta de registro simple para analizar registros


## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial, necesitará lo siguiente: 

- Un recurso de Azure para supervisar. Puede usar cualquier recurso de la suscripción de Azure que admita la configuración de diagnóstico. Para determinar si un recurso es compatible con la configuración de diagnóstico, vaya a su menú en Azure Portal y busque la opción **Configuración de diagnóstico** en la sección **Supervisión** del menú.


> [!NOTE]
> Este procedimiento no se aplica a las máquinas virtuales de Azure, ya que el menú **Configuración de diagnóstico** se usa para configurar la extensión de diagnóstico.

## <a name="create-a-log-analytics-workspace"></a>Creación de un área de trabajo de Log Analytics
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]

## <a name="create-a-diagnostic-setting"></a>Creación de una configuración de diagnóstico
La [configuración de diagnóstico](../essentials/diagnostic-settings.md) define dónde se deben enviar los registros de recurso y de un recurso determinado. Un solo valor de configuración de diagnóstico puede tener varios [destinos](../essentials/diagnostic-settings.md#destinations), pero en este tutorial solo usaremos un área de trabajo de Log Analytics.

En la sección **Supervisión** del menú del recursos, seleccione **Configuración de diagnóstico** y haga clic en **Agregar configuración de diagnóstico**.

> [!NOTE]
> Algunos recursos pueden requerir selecciones adicionales. Por ejemplo, una cuenta de almacenamiento requiere que seleccione un recurso antes de que se muestre la opción **Agregar configuración de diagnóstico**. También puede observar una etiqueta de **versión preliminar** para algunos recursos, ya que su configuración de diagnóstico se encuentra actualmente en versión preliminar pública.

:::image type="content" source="media/tutorial-resource-logs/diagnostic-settings.png" lightbox="media/tutorial-resource-logs/diagnostic-settings.png"alt-text="Configuración de diagnóstico":::


Cada configuración de diagnóstico tiene tres partes básicas:
 
   - **Name**: no afecta en nada y debe ser simplemente descriptivo.
   - **Categorías**: categorías de registros que se van a enviar a cada uno de los destinos. Este conjunto de categorías variará para cada servicio de Azure.
   - **Destinos**: Uno o más destinos para enviar los registros. Todos los servicios de Azure comparten el mismo conjunto de destinos posibles. Cada configuración de diagnóstico puede definir uno o más destinos, pero no más de uno de un tipo determinado. 

Escriba un nombre para la configuración de diagnóstico y seleccione las categorías que desea recopilar. Consulte la documentación de cada servicio para una definición de sus categorías disponibles. **AllMetrics** enviará las mismas métricas de plataforma disponibles en las métricas de Azure Monitor del recurso al área de trabajo. Esto le permite analizar estos datos con consultas de registro junto con otros datos de supervisión. Seleccione **Enviar a Log Analytics** y el área de trabajo que ha creado. 

:::image type="content" source="media/tutorial-resource-logs/diagnostic-setting-details.png" lightbox="media/tutorial-resource-logs/diagnostic-setting-details.png"alt-text="Detalles de la configuración de diagnóstico":::

Haga clic en **Guardar** para guardar la configuración de diagnóstico.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Uso de una consulta de registro para recuperar registros
Los datos se recuperan de un área de trabajo de Log Analytics mediante una consulta de registro escrita en el lenguaje de consulta de Kusto (KQL). Hay disponible un conjunto de consultas creadas previamente para muchos servicios de Azure, y que así no necesite conocimientos de KQL para empezar a trabajar.

Seleccione **Registros** en el menú del recurso. Log Analytics se abre con la ventana **Consultas** que incluye consultas creadas previamente para el **tipo de recurso**. 

> [!NOTE]
> Si la ventana **Consultas** no se abre, haga clic en **Consultas** en la parte superior derecha. 

:::image type="content" source="media/tutorial-resource-logs/queries.png" lightbox="media/tutorial-resource-logs/queries.png"alt-text="Captura de pantalla que muestra consultas de ejemplo mediante registros de recursos.":::


Examine las consultas disponibles. Identifique la que quiera ejecutar y haga clic en **Ejecutar**. La consulta se agrega a la ventana de consultas y los resultados devueltos.

:::image type="content" source="media/tutorial-resource-logs/query-results.png" lightbox="media/tutorial-resource-logs/query-results.png"alt-text="Captura de pantalla que muestra los resultados de una consulta de registro de ejemplo.":::

## <a name="next-steps"></a>Pasos siguientes
Ahora que está recopilando registros de recursos, cree una alerta de consulta de registro para recibir una notificación proactiva cuando se identifiquen datos interesantes en los datos de registro.

> [!div class="nextstepaction"]
> [Creación de una alerta de consulta de registro para un recurso de Azure](../alerts/tutorial-log-alert.md)
