---
title: 'Tutorial: Alerta cuando Azure Virtual está sin servicio'
description: Cree una regla de alerta en Azure Monitor para notificarle proactivamente si una máquina virtual no está disponible.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: af60844e6aced51989cbee07a787deba01a379f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350140"
---
# <a name="tutorial-create-alert-when-azure-virtual-machine-is-unavailable"></a>Tutorial: Creación de una alerta cuando la máquina virtual de Azure no está disponible
Una de las condiciones de alerta más comunes para una máquina virtual es si la máquina virtual se está ejecutando. Una vez que se habilita la supervisión con información de máquina virtual Azure Monitor para la máquina virtual, se envía un latido a Azure Monitor cada minuto. Puede crear una regla de alertas de consulta de registro que envíe una alerta si no se detecta un latido. Este método no solo alerta si la máquina virtual no se está ejecutando, sino también si no responde.


En este tutorial aprenderá a:

> [!div class="checklist"]
> * Ver los datos de registro recopilados por la información de máquina virtual en Azure Monitor para una máquina virtual.
> * Cree una regla de alerta a partir de datos de registro que le notifique de forma proactiva si la máquina virtual no está disponible.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesitará lo siguiente: 

- Una máquina virtual de Azure para supervisar.
- Supervisión con información de máquina virtual habilitada para la máquina virtual. Consulte el [tutorial: Habilitación de la supervisión para la máquina virtual de Azure](tutorial-monitor-vm-enable.md).



## <a name="create-a-heartbeat-query"></a>Creación de una consulta de latido
Hay varias maneras de crear una regla de alerta de consulta de registro. Para este tutorial, comenzaremos desde la pestaña **Eventos de registro** en la vista **Mapa**. Esto proporciona un resumen de los datos de registro recopilados para la máquina virtual. 

:::image type="content" source="media/tutorial-monitor-vm/map-logs.png" lightbox="media/tutorial-monitor-vm/map-logs.png" alt-text="Vista de mapa con la pestaña eventos de registro":::

Haga clic en **Latido**. Se abre el Análisis de registros, que es la herramienta principal para analizar los datos de registro recopilados de la máquina virtual, con una consulta simple para eventos de latido. Si hace clic en **Hora en la que se generó para** ordenar por esa columna, puede ver que se crea un latido cada minuto.

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat.png" alt-text="Alerta de consulta de registro con eventos de latido.":::


Para la alerta, quiere devolver solamente los registros de latido en los últimos 5 minutos. Si no se devuelve ningún registro, puede suponer que la máquina virtual está fuera de servicio.

Añada una línea a la consulta para filtrar los resultados a los registros creados en los últimos 5 minutos. Esto usa la [función «hace x tiempo»](/azure/data-explorer/kusto/query/agofunction) que resta un intervalo de tiempo determinado de la hora actual.

```
Heartbeat
| where Computer == 'computer-name'
| where TimeGenerated > ago(5m)
```

Haga clic en **Ejecutar** para ver los resultados de esta consulta, que ahora debería incluir solamente los latidos de los últimos 5 minutos.

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" alt-text="Alerta de consulta de registro con eventos de latido utilizando el filtro de tiempo.":::

## <a name="create-alert-rule"></a>Crear regla de alertas
Ahora que tiene la consulta de registro, puede crear una regla de alerta que enviará una alerta cuando esa consulta no devuelva ningún registro. Si no se devuelven registros de latido de los últimos 5 minutos, podemos suponer que la máquina no tenía capacidad de respuesta en ese momento. 

Haga clic en **Nueva regla de alertas** para crear una regla a partir de la consulta actual.

:::image type="content" source="media/tutorial-monitor-vm/new-alert-rule.png" lightbox="media/tutorial-monitor-vm/new-alert-rule.png" alt-text="Nueva regla de alertas.":::


La regla de alerta ya tendrá rellenada la **consulta de registro**. La **Medida** también es correcta, ya que queremos contar el número de filas de tabla devueltas de la consulta. Si el número de filas es cero, entonces queremos crear una alerta.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-01.png" lightbox="media/tutorial-monitor-vm/alert-rule-01.png" alt-text="Condición de regla de alerta 1.":::

Desplácese hacia abajo hasta **lógica de Alerta** y cambie el **Operador** a **Igual a** e indique un **valor de umbral** de **0**. Esto significa que queremos crear una alerta cuando no se devuelva ningún registro o cuando el recuento de registros de la consulta sea igual a cero.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-02.png" lightbox="media/tutorial-monitor-vm/alert-rule-02.png" alt-text="Condición de regla de alerta 2.":::

## <a name="configure-action-group"></a>Configuración del grupo de acciones
La página **Acciones** permite agregar uno o varios [grupos de acciones](../alerts/action-groups.md) a la regla de alerta. Los grupos de acciones definen un conjunto de acciones que se deben realizar cuando se desencadena una alerta, como el envío de un correo electrónico o un mensaje SMS.

Si ya tiene un grupo de acciones, haga clic en **Añadir grupo de acciones** para añadir un grupo existente a la regla de alerta.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-actions.png" lightbox="media/tutorial-monitor-vm/alert-rule-actions.png" alt-text="Adición de un grupo de acciones a la regla de alerta.":::

Si aún no tiene un grupo de acciones en la suscripción para seleccionar, haga clic en **Crear grupo de acciones** para crear uno nuevo. Seleccione una **suscripción** y un **grupo de recursos** para el grupo de acciones y asígnele un **nombre de grupo de acciones** que aparecerá en el portal y un **nombre para mostrar** que aparecerá en las notificaciones por correo electrónico y SMS.

:::image type="content" source="media/tutorial-monitor-vm/action-group-basics.png" lightbox="./media/tutorial-monitor-vm/action-group-basics.png" alt-text="Conceptos básicos del grupo de acciones":::

Seleccione la pestaña **Notificaciones** y agregue uno o varios métodos para notificar a las personas adecuadas cuando se active la alerta.

:::image type="content" source="media/tutorial-monitor-vm/action-group-notifications.png" lightbox="./media/tutorial-monitor-vm/action-group-notifications.png" alt-text="Notificaciones del grupo de acciones":::



## <a name="configure-details"></a>Configuración de detalles
La página **Detalles** le permite configurar diferentes ajustes para la regla de alerta.

- **Suscripción** y **Grupo de recursos** donde se almacenará la regla de alerta. No es necesario que esté en el mismo grupo de recursos que el recurso que está supervisando.
- **Gravedad** de la alerta. La gravedad le permite agrupar alertas con una importancia relativa similar. Una gravedad de **Error** es adecuada para una máquina virtual que no responde.
- Mantenga activada la casilla **Habilitar la alerta tras la creación**.
- Mantenga activada la casilla **Resolver automáticamente las alertas**. Esto resolverá automáticamente la alerta cuando la máquina virtual vuelva a estar en línea y se vuelvan a detectar los registros de latido.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-details.png" lightbox="media/tutorial-monitor-vm/alert-rule-details.png" alt-text="Detalles de la regla de alerta.":::

Haga clic en **Revisar y crear** para crear la regla de alerta.

## <a name="view-the-alert"></a>Visualización de la alerta
Para probar la regla de alerta, detenga la máquina virtual. Si configuró una notificación en el grupo de acciones, debería recibirla en unos minutos. También verá una alerta indicada en el resumen que se muestra en la página **Alertas** de la máquina virtual.

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="Resumen de alertas":::

Haga clic en **Gravedad** ver la lista de esas alertas. Haga clic en la propia alerta para ver sus detalles.

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="Lista de alertas":::

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe cómo crear una alerta a partir de datos de registro, recopile registros adicionales y datos de rendimiento de la máquina virtual con una regla de recopilación de datos.

> [!div class="nextstepaction"]
> [Recopilación de registros y métricas de invitado de una máquina virtual de Azure](tutorial-monitor-vm-guest.md)

