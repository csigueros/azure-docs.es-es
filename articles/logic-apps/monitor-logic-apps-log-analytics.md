---
title: Supervisión de aplicaciones lógicas con los registros de Azure Monitor
description: Solución de problemas de aplicaciones lógicas mediante la configuración de registros de Azure Monitor y la recopilación de datos de diagnóstico para Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: 5375649d8bf5bdf1a6a24babc6f05d30d40c93b9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215191"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Configuración de registros de Azure Monitor y recopilación de datos de diagnóstico para Azure Logic Apps

Si necesita información de depuración más completa sobre las aplicaciones lógicas durante el tiempo de ejecución, puede configurar y usar [registros de Azure Monitor](../azure-monitor/logs/data-platform-logs.md) para registrar y almacenar información sobre los eventos y datos en tiempo de ejecución, como eventos de desencadenador, eventos de ejecución y eventos de acción en un [área de trabajo de Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). [Azure Monitor](../azure-monitor/overview.md) le ayuda a supervisar los entornos locales y en la nube para que pueda mantener más fácilmente su disponibilidad y rendimiento. Mediante los registros de Azure Monitor, puede crear [consultas de registro](../azure-monitor/logs/log-query-overview.md) que le ayuden a recopilar y revisar esta información. También puede [usar estos datos de diagnóstico con otros servicios de Azure](#extend-data), como Azure Storage y Azure Event Hubs.

Para configurar el registro de la aplicación lógica, puede [habilitar Log Analytics al crear la aplicación lógica](#logging-for-new-logic-apps), o bien [instalar la solución de administración de Logic Apps](#install-management-solution) en el área de trabajo de Log Analytics para las aplicaciones lógicas existentes. Esta solución proporciona información agregada para las ejecuciones de aplicaciones lógicas e incluye detalles específicos, como el estado, el tiempo de ejecución, el estado de reenvío y los identificadores de correlación. Luego, para habilitar el registro y la creación de consultas con esta información, [configure los registros de Azure Monitor](#set-up-resource-logs).

En este artículo se muestra cómo habilitar en aplicaciones lógicas nuevas y existentes, cómo instalar y configurar la solución de administración de Logic Apps y cómo configurar y crear consultas para los registros de Azure Monitor.

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar, necesita un [área de trabajo de Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Si no tiene una, aprenda a [crear un área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Habilitación de Log Analytics para nuevas aplicaciones lógicas

Puede activar Log Analytics al crear la aplicación lógica.

1. En [Azure Portal](https://portal.azure.com), en el panel donde se proporciona la información para crear la aplicación lógica, siga estos pasos:

   1. En **Log Analytics**, seleccione **Activado**.

   1. En la lista **Área de trabajo de Log Analytics**, seleccione el área de trabajo donde quiera enviar los datos de las ejecuciones de aplicación lógica.

      ![Especificación de información de la aplicación lógica](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Después de realizar este paso, Azure crea la aplicación lógica, que ahora está asociada al área de trabajo de Log Analytics. Además, este paso instala automáticamente la solución Logic Apps Management en el área de trabajo.

1. Seleccione **Crear** cuando haya terminado.

1. Después de ejecutar la aplicación lógica, [siga estos pasos](#view-logic-app-runs) para ver las ejecuciones de la aplicación lógica.

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Instalación de la solución Logic Apps Management

Si ya activó Log Analytics cuando creó su aplicación lógica, omita este paso. Ya tiene la solución de administración de Logic Apps instalada en el área de trabajo de Log Analytics.

1. En el cuadro de búsqueda de [Azure Portal](https://portal.azure.com), escriba `log analytics workspaces` y, luego, seleccione **Áreas de trabajo de Log Analytics**.

   ![Selección de "Áreas de trabajo de Log Analytics"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. En **Áreas de trabajo de Log Analytics**, seleccione su área de trabajo.

   ![Selección del área de trabajo de Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. En el panel **Información general**, en **Introducción a Log Analytics** > **Configurar soluciones de supervisión**, elija **Ver soluciones**.

   ![Selección de "Ver soluciones" en el panel de información general](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. En **Información general**, seleccione **Agregar**.

   ![Incorporación de una solución nueva en el panel de información general](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Después de que se abre **Marketplace**, en el cuadro de búsqueda, escriba `logic apps management` y seleccione **Logic Apps Management**.

   ![Selección de "Logic Apps Management" en Marketplace](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. En el panel de descripción de la solución, seleccione **Crear**.

   ![Selección de "Crear" para agregar la solución "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Revise y confirme el área de trabajo de Log Analytics donde quiere instalar la solución y vuelva a seleccionar **Crear**.

   ![Selección de "Crear" para "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Una vez que Azure implemente la solución en el grupo de recursos de Azure que contiene el área de trabajo de Log Analytics, la solución aparecerá en el panel de resumen del área de trabajo.

   ![Panel de resumen del área de trabajo](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configuración de los registros de Azure Monitor

Al almacenar información sobre eventos y datos en tiempo de ejecución en los [registros de Azure Monitor](../azure-monitor/logs/data-platform-logs.md), puede crear [consultas de registro](../azure-monitor/logs/log-query-overview.md) que le ayuden a buscar y revisar esta información.

> [!NOTE]
> Después de habilitar la configuración de diagnóstico, es posible que los datos de diagnóstico no fluyan durante un periodo de hasta 30 minutos en el destino especificado, como Log Analytics, el centro de eventos o la cuenta de almacenamiento. Este retraso significa que es posible que los datos de diagnóstico de este período de tiempo no estén disponibles para que los pueda revisar. Los eventos completados y las [propiedades de las que se ha realizado un seguimiento](#extend-data) podrían no aparecer en el área de trabajo de Log Analytics durante 10-15 minutos.

1. En [Azure Portal](https://portal.azure.com), busque y seleccione la aplicación lógica.

1. En el menú de la aplicación lógica, en **Supervisión**, seleccione **Configuración de diagnóstico** > **Agregar configuración de diagnóstico**.

   ![En "Supervisión", seleccionar "Configuración de diagnóstico" > "Agregar configuración de diagnóstico"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Para crear la configuración, siga estos pasos:

   1. Proporcione un nombre para la configuración.

   1. Seleccione **Enviar a Log Analytics**.

   1. En **Suscripción**, seleccione la suscripción de Azure que está asociada al área de trabajo de Log Analytics.

   1. En **Área de trabajo de Log Analytics**, seleccione el área de trabajo que quiere usar.

   1. En **log**, seleccione la categoría **WorkflowRuntime**, que especifica la categoría de eventos que quiere registrar.

   1. Para seleccionar todas las métricas, en **metric**, seleccione **AllMetrics**.

   1. Cuando finalice, seleccione **Guardar**.

   Por ejemplo:

   ![Selección del área de trabajo de Log Analytics y los datos para el registro](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Visualización del estado de las ejecuciones de aplicación lógica

Una vez ejecutada la aplicación lógica, puede ver los datos sobre esas ejecuciones en el área de trabajo de Log Analytics.

1. En [Azure Portal](https://portal.azure.com), busque y abra el área de trabajo de Log Analytics.

1. En el menú del área de trabajo, seleccione **Resumen del área de trabajo** > **Logic Apps Management**.

   ![Estado y recuento de ejecuciones de aplicación lógica](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Si el icono de Logic Apps Management no muestra los resultados inmediatamente después de una ejecución, pruebe a seleccionar **Actualizar** o espere un poco antes de volver a intentarlo.

   En este caso, las ejecuciones de aplicación lógica se agrupan por nombre y estado de ejecución. En esta página, también se muestran detalles sobre errores en acciones o desencadenadores de las ejecuciones de aplicación lógica.

   ![Resumen de estado de las ejecuciones de aplicación lógica](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Para ver todas las ejecuciones de una aplicación lógica o un estado específicos, seleccione la fila correspondiente a la aplicación lógica o el estado.

   A continuación se muestra un ejemplo con todas las ejecuciones de una aplicación lógica específica:

   ![Vista de las ejecuciones y el estado de la aplicación lógica](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   En el caso de las acciones en las que [configura propiedades con seguimiento](#extend-data), también puede verlas seleccionando **Ver** en la columna **Propiedades controladas**. Para buscar las propiedades controladas, use el filtro de columna.

   ![Ver propiedades controladas de una aplicación lógica](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Para filtrar los resultados, puede aplicar un filtro en el cliente y en el servidor.

   * **Filtro del lado cliente**: por cada columna, seleccione los filtros que prefiera, por ejemplo:

     ![Ejemplo de filtros de columna](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtro del lado servidor**: para seleccionar un intervalo de tiempo específico o limitar el número de ejecuciones que se muestran, use el control de ámbito de la parte superior de la página. De forma predeterminada, solo aparecen 1000 registros a la vez.

     ![Cambio de la ventana de tiempo](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Para ver todas las acciones de una ejecución específica y sus detalles, seleccione la fila de una ejecución de aplicación lógica.

   A continuación, se muestra un ejemplo de todas las acciones y desencadenadores de una ejecución de aplicación lógica específica:

   ![Ver acciones de una ejecución de aplicación lógica](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Envío de datos de diagnóstico a Azure Storage y Azure Event Hubs

Además de con registros de Azure Monitor, puede usar los datos de diagnóstico de la aplicación lógica con otros servicios de Azure, por ejemplo:

* [Archivado de registros de recursos de Azure en la cuenta de almacenamiento](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Transmisión de registros de plataforma de Azure a Azure Event Hubs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Luego puede obtener supervisión en tiempo real mediante la telemetría y los análisis de otros servicios, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) y [Power BI](../azure-monitor/logs/log-powerbi.md). Por ejemplo:

* [Transmitir datos de Event Hubs a Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizar datos que se están transmitiendo con Stream Analytics y crear un panel de análisis en tiempo real en Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

En función de las ubicaciones en las que quiera enviar datos de diagnóstico, asegúrese primero de [crear una cuenta de almacenamiento de Azure](../storage/common/storage-account-create.md) o de [crear un centro de eventos de Azure](../event-hubs/event-hubs-create.md). Después, puede seleccionar los destinos a los que quiere enviar los datos. Solo se aplican períodos de retención cuando se usa una cuenta de almacenamiento.

![Envío de los datos a una cuenta de Azure Storage o a un centro de eventos](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Eventos de diagnóstico de Azure Monitor

Cada evento de diagnóstico incluye detalles sobre la aplicación lógica y ese evento, por ejemplo, el estado, la hora de inicio, la hora de finalización, etc. Para configurar mediante programación la supervisión, el seguimiento y el registro, puede usar esta información con la [API REST para Azure Logic Apps](/rest/api/logic) y la [API REST para Azure Monitor](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows). También puede usar las propiedades `clientTrackingId` y `trackedProperties`, que aparecen en 

* `clientTrackingId`: si no se ha proporcionado, Azure genera de forma automática este identificador y correlaciona eventos en una ejecución de aplicación lógica, incluidos los flujos de trabajo anidados que se llamen desde la aplicación lógica. Puede especificar manualmente este identificador desde un desencadenador si pasa un encabezado `x-ms-client-tracking-id` con el valor de identificador personalizado en la solicitud de desencadenador. Puede usar un desencadenador de solicitud, un desencadenador HTTP o un desencadenador de webhook.

* `trackedProperties`: Para realizar un seguimiento de las entradas o salidas de los datos de diagnóstico, puede agregar una sección `trackedProperties` a una acción mediante el Diseñador de aplicación lógica o directamente en la definición JSON de la aplicación lógica. Las propiedades controladas solo pueden realizar el seguimiento de entradas y salidas de acciones individuales, aunque puede usar las propiedades `correlation` de los eventos para crear correlaciones entre las acciones de una ejecución. Para realizar el seguimiento de más de una propiedad, agregue la sección `trackedProperties` y las propiedades que quiera a la definición de la acción.

  Este es un ejemplo en el que se muestra el modo en que la definición de acción de la **variable de inicialización** incluye propiedades controladas de la entrada de la acción donde la entrada es una matriz, no un registro.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  En este ejemplo se muestran varias propiedades controladas:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

En este ejemplo se muestra cómo el evento `ActionCompleted` incluye los atributos `clientTrackingId` y `trackedProperties`:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Creación de consultas de supervisión y seguimiento](../logic-apps/create-monitoring-tracking-queries.md)
* [Supervisión de mensajes B2B con los registros de Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)