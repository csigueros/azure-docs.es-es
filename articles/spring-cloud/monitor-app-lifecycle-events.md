---
title: Supervisión de eventos del ciclo de vida de la aplicación mediante el registro de actividad de Azure y Azure Service Health
description: Supervise los eventos del ciclo de vida de la aplicación y configure alertas con el registro de actividad de Azure y Azure Service Health.
author: karlerickson
ms.author: shiqiu
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 77e6704808cf54f84f0261c07236bfa27d17a1f6
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868765"
---
# <a name="monitor-app-lifecycle-events-using-azure-activity-log-and-azure-service-health"></a>Supervisión de eventos del ciclo de vida de la aplicación mediante el registro de actividad de Azure y Azure Service Health

En este artículo se muestra cómo supervisar los eventos del ciclo de vida de la aplicación y configurar alertas con el registro de actividad de Azure y Azure Service Health.

Azure Spring Cloud proporciona herramientas integradas para supervisar el estado y el mantenimiento de las aplicaciones. Los eventos del ciclo de vida de la aplicación le ayudan a comprender los cambios que se realizaron en las aplicaciones para que pueda tomar medidas según sea necesario. 

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Una instancia de servicio Azure Spring Cloud implementada y al menos una aplicación ya creada en la instancia de servicio. Para obtener más información, consulte [Inicio rápido: implementación de la primera aplicación de Azure Spring Cloud](quickstart.md). 

## <a name="monitor-app-lifecycle-events-triggered-by-users-in-azure-activity-logs"></a>Supervisión de los eventos del ciclo de vida de la aplicación desencadenados por los usuarios en los registros de actividad de Azure

Los [registros de actividad de Azure](../azure-monitor/essentials/activity-log.md) contienen eventos de recursos emitidos por las operaciones realizadas en los recursos de la suscripción. Los siguientes detalles de los eventos del ciclo de vida de la aplicación (inicio, detenerse, reiniciar) se agregan a los registros de actividad de Azure:

- Cuándo tuvo lugar la operación
- El estado de la operación
- Qué instancias se crean al iniciar la aplicación
- Qué instancias se eliminan al detener la aplicación
- Qué instancias se eliminan y se crean al reiniciar la aplicación

Por ejemplo, al reiniciar la aplicación, puede encontrar las instancias afectadas en la página detalles del **registro de actividad** en Azure Portal.

:::image type="content" source="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" lightbox="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" alt-text="Captura de pantalla de Azure Portal de los detalles de reinicio en el registro de actividad":::

## <a name="monitor-app-lifecycle-events-in-azure-service-health"></a>Supervisión de eventos del ciclo de vida de la aplicación en Azure Service Health

[Azure Resource Health](../service-health/resource-health-overview.md) ayuda a diagnosticar y obtener soporte técnico para problemas que pueden afectar a la disponibilidad del servicio. Estos problemas incluyen incidentes de servicio, períodos de mantenimiento planeados e interrupciones regionales. Los eventos de reinicio de la aplicación se agregan a Azure Service Health. Incluyen incidentes inesperados (por ejemplo, un bloqueo de aplicación no planeado) y acciones programadas (por ejemplo, mantenimiento planeado).

### <a name="monitor-unplanned-app-lifecycle-events"></a>Supervisión de eventos del ciclo de vida de la aplicación no planeados

Cuando se reinicia la aplicación debido a eventos no planeados, la instancia de Azure Spring Cloud mostrará un estado de **Degradado** en la sección **Resource Health** de Azure Portal. Degradado significa que el recurso ha detectado una pérdida de rendimiento potencial, aunque sigue estando disponible para su uso. Algunos ejemplos de eventos no planeados son el bloqueo de la aplicación, el error de comprobación de estado y la interrupción del sistema.

:::image type="content" source="media/monitor-app-lifecycle-events/resource-health-detail.png" alt-text="Captura de pantalla del panel de Resource Health":::

Puede encontrar el estado más reciente, la causa principal y las instancias afectadas en la página del historial de estado.

:::image type="content" source="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" lightbox="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" alt-text="Captura de pantalla de registros de ejemplo para eventos del ciclo de vida de la aplicación no planeados":::


### <a name="monitor-planned-app-lifecycle-events"></a>Supervisión de eventos del ciclo de vida de la aplicación planeados

La aplicación se puede reiniciar durante el mantenimiento de la plataforma. Puede recibir una notificación de mantenimiento de antemano desde la página **Mantenimiento planeado** de Azure Service Health.

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" alt-text="Captura de pantalla de una notificación de ejemplo para el mantenimiento planeado":::

Cuando se produce el mantenimiento de la plataforma, la instancia de Azure Spring Cloud también mostrará un estado de **Degradado**. Si es necesario reiniciar durante el mantenimiento de la plataforma, Azure Spring Cloud realizará una actualización gradual para actualizar incrementalmente las aplicaciones. Las actualizaciones graduales están diseñadas para actualizar las cargas de trabajo sin tiempo de inactividad. Puede encontrar el estado más reciente en la página del historial de estado.

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" alt-text="Captura de pantalla de un registro de ejemplo para el mantenimiento planeado en curso":::

## <a name="set-up-alerts"></a>Configuración de alertas

Puede configurar alertas para eventos de ciclo de vida de la aplicación. Las notificaciones del estado del servicio también se almacenan en el registro de actividad de Azure. El registro de actividad almacena un gran volumen de información, por lo que existe una interfaz de usuario independiente que facilita la visualización y la configuración de alertas en las notificaciones del estado del servicio.

En la lista siguiente se describen los pasos clave necesarios para configurar una alerta: 

1. Configure un grupo de acciones con las acciones que se deben realizar cuando se desencadene una alerta. Entre los tipos de acciones de ejemplo se incluyen el envío de llamadas de voz, mensajes de texto o correo electrónico o el desencadenamiento de varios tipos de acciones automatizadas. Varias alertas pueden usar el mismo grupo de acciones o distintos grupos de acciones en función de los requisitos del usuario.
2. Configure las reglas de alerta. Las alertas usan grupos de acciones para notificar a los usuarios que se ha desencadenado una alerta para algún evento específico del ciclo de vida de la aplicación.

### <a name="set-up-alerts-on-activity-log"></a>Configuración de alertas del registro de actividad

Los pasos siguientes muestran cómo crear una regla de alertas del registro de actividad en Azure Portal:

1. Vaya a **Registro de actividad,** abra la página de detalles de cualquier registro de actividad y seleccione **New alert rule** (Nueva regla de alertas).

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert.png" alt-text="Captura de pantalla de una alerta del registro de actividad":::

2. Seleccione el **Ámbito** de la alerta.

3. Especifique la **Condición** de la alerta.

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" alt-text="Captura de pantalla de una condición de alerta del registro de actividad":::

4. Seleccione **Acciones** y agregue los **Alert rule details** (Detalles de la regla de alertas).

5. Seleccione **Crear regla de alertas**.

### <a name="set-up-alerts-to-monitor-app-lifecycle-events-in-azure-service-health"></a>Configuración de alertas para supervisar los eventos del ciclo de vida de la aplicación en Azure Service Health

Los pasos siguientes muestran cómo crear una regla de alertas para las notificaciones del estado del servicio en Azure Portal:

1. Vaya a **Resource Health** en **Service Health** y, a continuación, seleccione **Add Resource Health alert** (Agregar alerta de Resource Health).

   :::image type="content" source="media/monitor-app-lifecycle-events/add-resource-health-alert.png" alt-text="Captura de pantalla del panel de Resource Health con el botón &quot;Agregar alerta de Resource Health&quot; resaltado":::

2. Seleccione el valor de **Recurso** para la alerta.

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-target.png" alt-text="Captura de pantalla de un destino de alerta de Resource Health":::

3. Especifique la **Condición de la alerta**.

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-condition.png" alt-text="Captura de pantalla de una condición de alerta de Resource Health":::

4. Seleccione las **Acciones** y agregue los **Alert rule details** (Detalles de la regla de alertas).

5. Seleccione **Crear regla de alertas**.

### <a name="set-up-alerts-to-monitor-the-planned-maintenance-notification"></a>Configuración de alertas para supervisar la notificación de mantenimiento planeado

Los pasos siguientes muestran cómo crear una regla de alertas para las notificaciones de mantenimiento planeado en Azure Portal:

1. Vaya a **Alertas de estado** en **Service Health** y, a continuación, seleccione **Add Service Health alert** (Agregar alerta de estado del servicio).

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert.png" alt-text="Captura de pantalla del panel de alertas de estado con el botón &quot;Agregar alerta de estado del servicio&quot; resaltado":::

2. Proporcione valores para **Suscripción**, **Servicios**, **Regiones**, **Tipo de evento**, **Acciones** y **Alert rule details** (Detalles de la regla de alertas).

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" lightbox="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" alt-text="Captura de pantalla del panel &quot;Crear alerta de regla&quot; para Service Health":::

3. Seleccione **Crear regla de alertas**.

## <a name="next-steps"></a>Pasos siguientes

[Autodiagnóstico y solución de problemas en Azure Spring Cloud](how-to-self-diagnose-solve.md)
