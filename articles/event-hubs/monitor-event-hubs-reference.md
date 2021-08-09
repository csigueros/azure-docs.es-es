---
title: Referencia sobre la supervisión de datos de Azure Event Hubs
description: Material de referencia importante necesario al supervisar Azure Event Hubs.
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 06/11/2021
ms.openlocfilehash: 3d68f84c3c58cd29951c2d51cc8d89e3573b7f7a
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063809"
---
# <a name="monitoring-azure-event-hubs-data-reference"></a>Referencia sobre la supervisión de datos de Azure Event Hubs
Consulte [Supervisión de Azure Event Hubs](monitor-event-hubs.md) para más información sobre la recopilación y el análisis de datos de supervisión para Azure Event Hubs.

## <a name="metrics"></a>Métricas
En esta sección se indican todas las métricas de la plataforma recopiladas automáticamente para Azure Event Hubs. El proveedor de recursos para estas métricas es **Microsoft.EventHub/clusters** o **Microsoft.EventHub/clusters**.

### <a name="request-metrics"></a>Solicitud de métricas
Cuenta el número de solicitudes de operaciones de datos y administración.

| Nombre de la métrica |  Se puede exportar con la configuración de diagnóstico. | Unidad | Tipo de agregación |  Descripción | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| Solicitudes entrantes| Sí | Count | Total | Número de solicitudes realizadas al servicio Event Hubs durante un período determinado. | Nombre de entidad| 
| Solicitudes correctas| No | Count | Total | Número de solicitudes correctas realizadas al servicio Event Hubs durante un período determinado. |  Nombre de entidad<br/><br/>Resultado de la operación | 
| Solicitudes limitadas| No | Count | Total |  Número de solicitudes que se han limitado porque se superó el uso. | Nombre de entidad<br/><br/>Resultado de la operación |

Los dos tipos de errores siguientes se clasifican como **errores de usuario**:

1. Errores en el cliente (en HTTP, serían errores 400).
2. Errores que se producen al procesar mensajes.


### <a name="message-metrics"></a>Métricas de mensaje
| Nombre de la métrica |  Se puede exportar con la configuración de diagnóstico. | Unidad | Tipo de agregación |  Descripción | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|Mensajes entrantes|  Sí | Count | Total | Número de eventos o mensajes enviados a Event Hubs durante un período determinado. | Nombre de entidad|
|Mensajes salientes| Sí | Count | Total | Número de eventos o mensajes recibidos de Event Hubs durante un período determinado. | Nombre de entidad | 
| Mensajes capturados| No | Count| Total | Número de mensajes capturados.  |  Nombre de entidad | 
|Bytes entrantes | Sí |  Bytes | Total | Bytes entrantes para un centro de eventos durante un período especificado.  | Nombre de entidad| 
|Bytes salientes | Sí |  Bytes | Total |Bytes salientes para un centro de eventos durante un período especificado.  | Nombre de entidad | 
| Size | No |  Bytes | Average |  Tamaño de un centro de eventos en bytes.|Nombre de entidad |


> [!NOTE]
> Estos valores son valores de un momento dado. Es posible que los mensajes entrantes que se consumieron inmediatamente después de ese momento dado no se reflejen en estas métricas. 

### <a name="capture-metrics"></a>Captura de métricas
| Nombre de la métrica |  Se puede exportar con la configuración de diagnóstico. | Unidad | Tipo de agregación |  Descripción | Dimensions | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
| Mensajes capturados| No | Count| Total | Número de mensajes capturados.  | Nombre de entidad |
| Bytes capturados | No | Bytes | Total | Bytes capturados para un centro de eventos. | Nombre de entidad | 
| Capture el trabajo pendiente | No | Count| Total | Captura del trabajo pendiente de un centro de eventos. | Nombre de entidad | 


### <a name="connection-metrics"></a>Métricas de conexión
| Nombre de la métrica |  Se puede exportar con la configuración de diagnóstico. | Unidad | Tipo de agregación |  Descripción | Dimensions | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Conexiones activas| No | Count | Average | Número de conexiones activas en un espacio de nombres y en una entidad (centro de eventos) en el espacio de nombres. El valor de esta métrica es un valor de un momento dado. Es posible que las conexiones que estaban activas inmediatamente después de ese momento dado no se reflejen en esta métrica.| Nombre de entidad | 
|Conexiones abiertas | No | Count | Average |  Número de conexiones abiertas. | Nombre de entidad | 
|Conexiones cerradas | No | Count | Average|  Número de conexiones cerradas. | Nombre de entidad | 

### <a name="error-metrics"></a>Métricas de error
| Nombre de la métrica |  Se puede exportar con la configuración de diagnóstico. | Unidad | Tipo de agregación |  Descripción | Dimensions |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Errores del servidor| No | Count | Total | Número de solicitudes no procesadas debido a un error en el servicio Event Hubs durante un período determinado. | Nombre de entidad<br/><br/>Resultado de la operación |
|Errores de usuario | No | Count | Total | Número de solicitudes no procesadas debido a errores de usuario durante un período determinado. | Nombre de entidad<br/><br/>Resultado de la operación|
|Cuota de errores superada | No |Count | Total | Número de errores causados por superar las cuotas durante un período especificado. | Nombre de entidad<br/><br/>Resultado de la operación|


## <a name="metric-dimensions"></a>Dimensiones de métricas

Azure Event Hubs admite las siguientes dimensiones para las métricas de Azure Monitor. La adición de dimensiones a las métricas es opcional. Si no agrega dimensiones, las métricas se especifican en el nivel de espacio de nombres. 

|Nombre de dimensión|Descripción|
| ------------------- | ----------------- |
|Nombre de entidad| Nombre del centro de eventos.|

## <a name="resource-logs"></a>Registros del recurso
[!INCLUDE [event-hubs-diagnostic-log-schema](../../includes/event-hubs-diagnostic-log-schema.md)]



## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor
Azure Event Hubs usa tablas de Kusto de registros de Azure Monitor. Puede consultar estas tablas con Log Analytics. Para ver una lista de las tablas de Kusto que usa el servicio, consulte [Referencia de las tablas de Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype#event-hubs).


## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la supervisión de Azure Event Hubs, consulte [Supervisión de Azure Event Hubs](monitor-event-hubs.md).
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
