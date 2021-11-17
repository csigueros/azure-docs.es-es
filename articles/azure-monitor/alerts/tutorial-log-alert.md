---
title: 'Tutorial: Creación de una alerta de consulta de registro para un recurso de Azure'
description: Tutorial para la creación de una alerta de consulta de registro para un recurso de Azure.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 09/16/2021
ms.openlocfilehash: 526abdb9a5e3c16fe58551c2aa8784caf329d1c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350164"
---
# <a name="tutorial-create-a-log-query-alert-for-an-azure-resource"></a>Tutorial: Creación de una alerta de consulta de registro para un recurso de Azure
Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Las reglas de alertas de consulta de registro crean una alerta cuando una consulta de registro devuelve un resultado determinado. Por ejemplo, si recibe una alerta cuando se crea un evento determinado en una máquina virtual o se envía una advertencia cuando se realizan solicitudes anónimas excesivas a una cuenta de almacenamiento.

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Acceso a consultas de registro precompilado diseñadas para admitir reglas de alertas para diferentes tipos de recursos
> * Creación de una regla de alerta de consulta de registro
> * Creación de un grupo de acciones para definir los detalles de la notificación


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará lo siguiente: 

- Un recurso de Azure para supervisar. Puede usar cualquier recurso de la suscripción de Azure que admita la configuración de diagnóstico. Para determinar si un recurso es compatible con la configuración de diagnóstico, vaya a su menú en Azure Portal y busque la opción **Configuración de diagnóstico** en la sección **Supervisión** del menú.


Si usa cualquier recurso de Azure que no sea una máquina virtual:

- Una configuración de diagnóstico para enviar los registros de recursos desde el recurso de Azure a un área de trabajo de Log Analytics. Consulte [Tutorial: Crear un área de trabajo de Log Analytics en Azure Monitor](../essentials/tutorial-resource-logs.md).

Si va a usar una máquina virtual de Azure:

- Una regla de recopilación de datos para enviar registros y métricas de invitado a un área de trabajo de Log Analytics. Consulte [Tutorial: Recopilación de registros y métricas de invitado de una máquina virtual de Azure](../vm/tutorial-monitor-vm-guest.md).

   
 
 ## <a name="select-a-log-query-and-verify-results"></a>Selección de una consulta de registro y comprobación de los resultados
Los datos se recuperan de un área de trabajo de Log Analytics mediante una consulta de registro escrita en el lenguaje de consulta de Kusto (KQL). La información y las soluciones de Azure Monitor proporcionarán consultas de registro para recuperar datos de un servicio determinado, pero puede trabajar directamente con las consultas de registro y sus resultados en Azure Portal con Log Analytics. 

Seleccione **Registros** en el menú del recurso. Log Analytics se abre con la ventana **Consultas** que incluye consultas creadas previamente para el **tipo de recurso**. Seleccione **Alertas** para ver las consultas diseñadas específicamente para las reglas de alertas.

> [!NOTE]
> Si la ventana **Consultas** no se abre, haga clic en **Consultas** en la parte superior derecha. 

:::image type="content" source="media/tutorial-log-alert/queries.png" lightbox="media/tutorial-log-alert/queries.png"alt-text="Ventana de Log Analytics con consultas":::

Seleccione una consulta y haga clic en **Ejecutar** para cargarla en el editor de consultas y que devuelva resultados. Es posible que quiera modificar la consulta y volver a ejecutarla. Por ejemplo, a continuación se muestra la consulta **Mostrar solicitudes anónimas** para cuentas de almacenamiento. Es posible que quiera modificar el valor de **authenticationType** o filtrar por una columna diferente.

:::image type="content" source="media/tutorial-log-alert/query-results.png" lightbox="media/tutorial-log-alert/query-results.png"alt-text="Resultados de la consulta":::


## <a name="create-alert-rule"></a>Crear regla de alertas
Una vez que compruebe la consulta, puede crear la regla de alerta. Seleccione **Nueva regla de alertas** para crear una nueva regla de alertas basada en la consulta de registro actual. El **ámbito** ya estará establecido en el recurso actual. No es necesario que cambie este valor.

:::image type="content" source="media/tutorial-log-alert/create-alert-rule.png" lightbox="media/tutorial-log-alert/create-alert-rule.png"alt-text="Crear regla de alertas":::
## <a name="configure-condition"></a>Configuración de condiciones

En la pestaña **Condición**, la **Consulta de registro** ya estará completa. La sección **Medida** define cómo se medirán los registros de la consulta del registro. Si la consulta no realiza un resumen, la única opción será **contar** el número de **filas de tabla**. Si la consulta incluye una o varias columnas resumidos, tendrá la opción de usar el número de **filas de tabla** o un cálculo basado en cualquiera de las columnas resumidas. La **granularidad de agregación** define el intervalo de tiempo durante el cual se agregan los valores recopilados. 

:::image type="content" source="media/tutorial-log-alert/alert-rule-condition.png" lightbox="media/tutorial-log-alert/alert-rule-condition.png"alt-text="Condición de regla de alerta":::

### <a name="configure-dimensions"></a>Configuración de dimensiones
**Dividir por dimensiones** le permite crear alertas independientes para distintos recursos. Esta configuración es útil cuando se crea una regla de alertas que se aplica a varios recursos. Con el ámbito establecido en un único recurso, esta configuración normalmente no se usa.

:::image type="content" source="media/tutorial-log-alert/alert-rule-dimensions.png" lightbox="media/tutorial-log-alert/alert-rule-dimensions.png"alt-text="Dimensiones de regla de alerta":::


## <a name="configure-alert-logic"></a>Configuración de la lógica de alertas
En la lógica de alertas, configure los valores **Operador** y **Umbral** para compararlos con el valor que devuelva la medida.  Se crea una alerta cuando este valor es "true". Seleccione un valor para la **Frecuencia de evaluación** que defina la frecuencia con la que se ejecuta y evalúa la consulta de registro. El costo de la regla de alerta aumenta con una frecuencia menor. Al seleccionar una frecuencia, se muestra el costo mensual estimado además de una vista previa de los resultados de la consulta durante un período de tiempo.

Por ejemplo, si la medida es **Filas de tabla**, la lógica de alerta puede ser **Mayor que 0**, ya que indica que se devolvió al menos un registro. Si la medida es un valor de columnas, es posible que la lógica tenga que ser mayor o menor que un valor de umbral determinado. En el ejemplo siguiente, la consulta de registro busca solicitudes anónimas a una cuenta de almacenamiento. Si se ha realizado una solicitud anónima, debemos desencadenar una alerta. En este caso, una sola fila devuelta desencadenaría la alerta, por lo que la lógica de alerta debe ser **Mayor que 0**.

:::image type="content" source="media/tutorial-log-alert/alert-rule-alert-logic.png" lightbox="media/tutorial-log-alert/alert-rule-alert-logic.png"alt-text="Lógica de alerta":::



## <a name="configure-actions"></a>Configuración de acciones
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>Configuración de detalles
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="media/tutorial-log-alert/alert-rule-details.png" lightbox="media/tutorial-log-alert/alert-rule-details.png"alt-text="Detalles de la regla de alertas":::

Haga clic en **Crear regla de alerta** para crear la regla de alerta.

## <a name="view-the-alert"></a>Visualización de la alerta
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a crear una alerta de consulta de registro para un recurso de Azure, consulte los libros para crear visualizaciones interactivas de datos de supervisión.

> [!div class="nextstepaction"]
> [Libros de Azure Monitor](../visualize/workbooks-overview.md)