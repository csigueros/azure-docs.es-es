---
title: Supervisión de referencia de datos de Azure Application Gateway
description: Material de referencia importante necesario al supervisar Application Gateway
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: application-gateway
ms.custom: subject-monitoring
ms.date: 06/10/2021
ms.openlocfilehash: 5ece5b220a59f562774fde7b093236ed5946f019
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836251"
---
<!-- VERSION 2.2
Template for monitoring data reference article for Azure services. This article is support for the main "Monitoring [servicename]" article for the service. -->

# <a name="monitoring-azure-application-gateway-data-reference"></a>Supervisión de referencia de datos de Azure Application Gateway

Consulte [Supervisión de Azure Application Gateway](monitor-application-gateway.md) para información detallada de cómo recopilar y analizar datos de supervisión para Azure Application Gateway.

## <a name="metrics"></a>Métricas

<!-- REQUIRED if you support Metrics. If you don't, keep the section but call that out. Some services are only onboarded to logs.
<!-- Please keep headings in this order -->

<!--  OPTION 2 -  Link to the metrics as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the metrics-supported link. For highly customized example, see [CosmosDB](../cosmos-db/monitor-cosmos-db-reference.md#metrics). They even regroup the metrics into usage type vs. resource provider and type.
-->

<!-- Example format. Mimic the setup of metrics supported, but add extra information -->

### <a name="application-gateway-v2-metrics"></a>Métricas de Application Gateway v2

Proveedor de recursos y tipo: [Microsoft.Compute/applicationGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkapplicationgateways)

#### <a name="timing-metrics"></a>Métricas de tiempo
Application Gateway proporciona varias métricas de temporización integradas relacionadas con la solicitud y la respuesta, que se miden en milisegundos.

> [!NOTE]
>
> Si Application Gateway tiene más de un cliente de escucha, filtre siempre por la dimensión *Cliente de escucha* al comparar las distintas métricas de latencia para obtener una inferencia significativa.


| Métrica | Unidad | Descripción|
|:-------|:-----|:------------|
|**Tiempo de conexión de back-end**|milisegundos|Tiempo empleado en establecer una conexión con la aplicación de back-end.<br><br>Esto incluye la latencia de red, así como el tiempo que tarda la pila TCP del servidor back-end en establecer nuevas conexiones. En el caso de TLS, incluye también el tiempo empleado en el protocolo de enlace.|
|**Tiempo de respuesta del primer byte de back-end**|milisegundos|Intervalo de tiempo entre el inicio del establecimiento de una conexión con el servidor back-end y la recepción del primer byte del encabezado de la respuesta.<br><br>Se aproxima a la suma de Tiempo de conexión de back-end, el tiempo que tarda la solicitud en alcanzar el back-end desde Application Gateway, el tiempo que tarda la aplicación de back-end en responder (el tiempo que el servidor tardó en generar contenido, y posiblemente capturar las consultas de la base de datos) y el tiempo que tarda el primer byte de la respuesta en llegar a Application Gateway desde el back-end.|
|**Tiempo de respuesta del último byte de back-end**|milisegundos|Intervalo de tiempo entre el inicio del establecimiento de una conexión con el servidor back-end y la recepción del último byte del cuerpo de la respuesta.<br><br>Esto corresponde aproximadamente a la suma del tiempo de respuesta de primer byte de back-end y el tiempo de transferencia de datos. Este número puede variar considerablemente en función del tamaño de los objetos solicitados y la latencia de la red del servidor.|
|**Tiempo total de Application Gateway**|milisegundos|Promedio de tiempo que se tarda en recibir y procesar una solicitud y en enviar la respuesta.<br><br>Este es el intervalo desde el momento en que Application Gateway recibe el primer byte de la solicitud HTTP hasta el momento en que se envía el último byte de respuesta al cliente. Esto incluye el tiempo de procesamiento que tarda Application Gateway, el Tiempo de respuesta del último byte de back-end, el tiempo que tarda Application Gateway en enviar toda la respuesta y el Cliente RTT.|
|**Cliente RTT**|milisegundos|Tiempo medio de ida y vuelta entre clientes y Application Gateway.|

Estas métricas se pueden usar para determinar si la ralentización observada se debe a la red del cliente, al rendimiento de Application Gateway, a la red de back-end y la saturación de la pila TCP del servidor back-end, al rendimiento de la aplicación de back-end o al tamaño de archivo grande.

Por ejemplo, si hay un pico en la tendencia de *Tiempo de respuesta del primer byte de back-end*, pero la tendencia de *Tiempo de conexión de back-end* es estable, se puede deducir que la latencia de Application Gateway al back-end y el tiempo necesario para establecer la conexión son estables, y el pico se debe a un aumento en el tiempo de respuesta de la aplicación de back-end. Por otro lado, si el pico en *Tiempo de respuesta del primer byte de back-end* se asocia a un pico correspondiente en *Tiempo de conexión de back-end*, se puede deducir que la red entre Application Gateway y el servidor back-end o la pila TCP del servidor back-end se han saturado. 

Si observa un pico en *Tiempo de respuesta del último byte de back-end*, pero el *Tiempo de respuesta del primer byte de back-end* es estable, se puede deducir que el pico se debe a que se está solicitando un archivo más grande.

Del mismo modo, si el *Tiempo total de Application Gateway* tiene un pico, pero el *Tiempo de respuesta del último byte de back-end* es estable, puede ser la señal de un cuello de botella de rendimiento en Application Gateway o de un cuello de botella en la red entre el cliente y Application Gateway. Además, si el *cliente RTT* también tiene un pico correspondiente, indica que la degradación se debe a la red entre el cliente y Application Gateway.

#### <a name="application-gateway-metrics"></a>Métricas de Application Gateway

| Métrica | Unidad | Descripción|
|:-------|:-----|:------------|
|**Bytes recibidos**|bytes|Recuento de bytes recibidos por Application Gateway de los clientes.|
|**Bytes enviados**|bytes|Recuento de bytes enviados por Application Gateway a los clientes.|
|**Protocolo TLS de cliente**|recuento|Recuento de solicitudes TLS y no TLS iniciadas por el cliente que establecieron la conexión con Application Gateway. Para ver la distribución del protocolo TLS, filtre por la dimensión del protocolo TLS.|
|**Unidades de capacidad actuales**|recuento|Recuento de unidades de capacidad consumidas para equilibrar la carga del tráfico. Hay tres determinantes para la unidad de capacidad: unidad de proceso, conexiones persistentes y rendimiento. Cada unidad de capacidad se compone a lo sumo de una unidad de proceso o 2500 conexiones persistentes o rendimiento de 2,22 Mbps.|
|**Unidades de proceso actuales**|recuento|Recuento de la capacidad de procesador consumida. Los factores que afectan a la unidad de proceso son las conexiones TLS/seg, los cálculos de reescritura de direcciones URL y el procesamiento de reglas de WAF.|
|**Conexiones actuales**|recuento|El número total de conexiones simultáneas activas de los clientes a Application Gateway.|
|**Unidades de capacidad facturadas estimadas**|recuento|Con el SKU de la versión 2, el modelo de precios está determinado por el consumo. Las unidades de capacidad miden el costo basado en el consumo, que se suma al costo fijo. Las *unidades de capacidad facturadas estimadas* indica el número de unidades de capacidad que se usa para estimar la facturación. Se calcula como el valor mayor entre las *unidades de capacidad actuales* (unidades de capacidad necesarias para equilibrar la carga del tráfico) y las *unidades de capacidad facturables fijas* (unidades de capacidad mínimas mantenidas).|
|**Solicitudes con error**|recuento|Número de solicitudes que ha servido Application Gateway con códigos de error del servidor 5xx. Esto incluye los códigos 5xx generados desde Application Gateway, así como los códigos 5xx que se generan desde el back-end. El recuento de solicitudes puede filtrarse aún más para mostrar el recuento por cada combinación de configuración de grupo de back-end o http específica.|
|**Unidades de capacidad facturables fijas**|recuento|El número mínimo de unidades de capacidad que se mantienen aprovisionadas según la configuración de *Unidades de escalado mínimas* (una instancia se traduce en 10 unidades de capacidad) en la configuración de Application Gateway.|
|**Nuevas conexiones por segundo**|recuento|Número promedio de conexiones TCP nuevas por segundo establecidas desde los clientes con Application Gateway y desde Application Gateway con los miembros de back-end.|
|**Estado de respuesta**|status code|Estado de la respuesta HTTP devuelta por Application Gateway. La distribución del código de estado de respuesta se puede categorizar aún más para mostrar las respuestas en categorías 2xx, 3xx, 4xx y 5xx.|
|**Rendimiento**|bytes/seg|Número de bytes por segundo que ha ofrecido Application Gateway.|
|**Total de solicitudes**|recuento|Recuento de solicitudes correctas que ha servido Application Gateway. El recuento de solicitudes puede filtrarse aún más para mostrar el recuento por cada combinación de configuración de grupo de back-end o http específica.|

#### <a name="backend-metrics"></a>Métricas de back-end

| Métrica | Unidad | Descripción|
|:-------|:-----|:------------|
|**Estado de respuesta de back-end**|recuento|Recuento de códigos de estado de respuesta HTTP devueltos por los back-ends. No se incluyen los códigos de respuesta generados por Application Gateway. La distribución del código de estado de respuesta se puede categorizar aún más para mostrar las respuestas en categorías 2xx, 3xx, 4xx y 5xx.|
|**Recuento de hosts con estado correcto**|recuento|El número de back-ends que el sondeo de Estado ha determinado que son correctos. También puede filtrar en función de grupos de back-end para mostrar el número de hosts en buen estado en un grupo de back-end específico.|
|**Recuento de hosts con estado incorrecto**|recuento|El número de back-ends que el sondeo de Estado ha determinado que son incorrectos. También puede filtrar en función de grupos de back-end para mostrar el número de hosts en estado incorrecto en un grupo de back-end específico.|
|**Solicitudes por minuto y host con estado correcto**|recuento|Número promedio de solicitudes recibidas por cada miembro correcto en un grupo de servidores de back-end en un minuto. Debe especificar el grupo de servidores de back-end mediante la dimensión *BackendPool Httpsettings*.|


### <a name="application-gateway-v1-metrics"></a>Métricas de Application Gateway v1

#### <a name="application-gateway-metrics"></a>Métricas de Application Gateway

| Métrica | Unidad | Descripción|
|:-------|:-----|:------------|
|**Uso de CPU**|percent|Muestra el uso de las CPU asignadas a Application Gateway. En condiciones normales, el uso de CPU no debe superar con regularidad el 90 %, ya que esto puede provocar latencia en los sitios web hospedados tras Application Gateway e interrumpir la experiencia del cliente. Para controlar o mejorar indirectamente el uso de CPU, puede modificar la configuración de Application Gateway aumentando el número de instancias o pasando a un tamaño de SKU mayor, o bien haciendo ambas cosas.|
|**Conexiones actuales**|recuento|Recuento de conexiones actuales establecidas con Application Gateway.|
|**Solicitudes con error**|recuento|Número de solicitudes erróneas debido a problemas de conexión. Este recuento incluye las solicitudes que devolvieron algún error debido a que se superó el *Tiempo de espera de la solicitud* de la configuración HTTP y las solicitudes que devolvieron algún error debido a problemas de conexión entre Application Gateway y el back-end. Sin embargo, este recuento no incluye los errores que se hayan producido debido a que no hay ningún back-end correcto disponible. las respuestas 4xx y 5xx del back-end tampoco se consideran como parte de esta métrica.|
|**Estado de respuesta**|status code|Estado de la respuesta HTTP devuelta por Application Gateway. La distribución del código de estado de respuesta se puede categorizar aún más para mostrar las respuestas en categorías 2xx, 3xx, 4xx y 5xx.|
|**Rendimiento**|bytes/seg|Número de bytes por segundo que ha ofrecido Application Gateway.|
|**Total de solicitudes**|recuento|Recuento de solicitudes correctas que ha servido Application Gateway. El recuento de solicitudes puede filtrarse aún más para mostrar el recuento por cada combinación de configuración de grupo de back-end o http específica.|
|**Web Application Firewall Blocked Requests Count** (Recuento de solicitudes bloqueadas por el firewall de aplicaciones web)|recuento|Número de solicitudes bloqueadas por WAF.|
|**Web Application Firewall Blocked Requests Rule Distribution** (Distribución de reglas de solicitudes bloqueadas por el firewall de aplicaciones web)|recuento|Número de solicitudes bloqueadas por WAF filtradas para mostrar el recuento por cada grupo de reglas de WAF específico o combinación de id. de regla de WAF.|
|**Web Application Firewall Total Rule Distribution** (Distribución de reglas totales del firewall de aplicaciones web)|recuento|Número de solicitudes recibidas por cada grupo de reglas de WAF específico o combinación de id. de regla de WAF.|


<!-- Keep this text as-is -->
Para obtener más información, vea una lista de [todas las métricas de la plataforma que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).



## <a name="metric-dimensions"></a>Dimensiones de métricas

<!-- REQUIRED. Please  keep headings in this order -->
<!-- If you have metrics with dimensions, outline it here. If you have no dimensions, say so.  Questions email azmondocs@microsoft.com -->

Para obtener más información sobre las dimensiones de métricas, consulte [Métricas multidimensionales](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).


<!-- See https://docs.microsoft.com/azure/storage/common/monitor-storage-reference#metrics-dimensions for an example. Part is copied below. -->

Azure Application Gateway admite dimensiones para algunas de las métricas de Azure Monitor. Cada métrica incluye una descripción que explica las dimensiones disponibles específicamente para esa métrica.


## <a name="resource-logs"></a>Registros del recurso
<!-- REQUIRED. Please  keep headings in this order -->

En esta sección se enumeran los tipos de registros de recursos que se pueden recopilar para Azure Application Gateway. 

<!-- List all the resource log types you can have and what they are for -->  

Como referencia, vea una lista de [todos los tipos de categorías de registros de recursos admitidos en Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

> [!NOTE]
> El registro de rendimiento solo está disponible para la SKU v1. En la SKU v2, use [Métricas](#metrics) para los datos de rendimiento.

Para más información, consulte [Mantenimiento del back-end y registros de diagnóstico para Application Gateway](application-gateway-diagnostics.md#access-log).

<!--  OPTION 2 -  Link to the resource logs as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the resource-log-categories link. You can group these sections however you want provided you include the proper links back to resource-log-categories article. 
-->

<!-- Example format. Add extra information -->

### <a name="application-gateway"></a>Application Gateway

Proveedor de recursos y tipo: [Microsoft.Network/applicationGateways](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkapplicationgateways)

| Category | Display Name (Nombre para mostrar) | Information|
|:---------|:-------------|------------------|
| **Activitylog**   | Registro de actividades | Las entradas del registro de actividad se recopilar de manera predeterminada. se puede usar el [registro de actividades de Azure](../azure-monitor/essentials/activity-log.md) (anteriormente conocido como registros operativos y registros de auditoría) para ver todas las operaciones enviadas a la suscripción de Azure, así como su estado. |
|**ApplicationGatewayAccessLog**|Registro de acceso| Puede usar este registro para ver los patrones de acceso de Application Gateway y analizar información importante. Esto incluye la dirección IP del autor de la llamada, la dirección URL solicitada, la latencia de la respuesta, el código de devolución y los bytes de entrada y salida. El registro de acceso se recopila cada 60 segundos. Este registro contiene un registro por cada instancia de Application Gateway. La instancia de Application Gateway se identifica por la propiedad instanceId.|
| **ApplicationGatewayPerformanceLog**|Registro de rendimiento|este registro se puede usar para ver el rendimiento de las instancias de Application Gateway. Este registro captura la información de rendimiento de cada instancia, incluida la cantidad total de solicitudes atendidas, el rendimiento en bytes, la cantidad de solicitudes con error y el número de instancias de back-end con un mantenimiento correcto o incorrecto. El registro de rendimiento se recopila cada 60 segundos. El registro de rendimiento solo está disponible para la SKU v1. En la SKU v2, use [Métricas](#metrics) para los datos de rendimiento.|
|**ApplicationGatewayFirewallLog**|Registro de firewall|este registro se puede usar para ver las solicitudes que se registran con el modo de detección o prevención de una puerta de enlace de aplicaciones que está configurada con el firewall de aplicaciones web. Los registros de firewall se recopilan cada 60 segundos.|


## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor
<!-- REQUIRED. Please keep heading in this order -->

En esta sección se hace referencia a todas las tablas de Kusto de los registros de Azure Monitor relacionadas con Azure Application Gateway y que están disponibles para la consulta mediante Log Analytics. 


<!-- OPTION 1 - Minimum -  Link to relevant bookmarks in https://docs.microsoft.com/azure/azure-monitor/reference/tables/tables-resourcetype where your service tables are listed. These files are auto generated from the REST API.   If this article is missing tables that you and the PM know are available, both of you contact azmondocs@microsoft.com.  
-->

<!-- Example format. There should be AT LEAST one Resource Provider/Resource Type here. -->

|Tipo de recurso | Notas |
|-------|-----|
| [Application Gateway](/azure/azure-monitor/reference/tables/tables-resourcetype#application-gateways) |Incluye AzureActivity, AzureDiagnostics y AzureMetrics |


Puede encontrar una referencia de todas las tablas de registros de Azure Monitor o de Log Analytics en [Referencia de la tabla de registros de Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).

### <a name="diagnostics-tables"></a>Tablas de diagnóstico
<!-- REQUIRED. Please keep heading in this order -->
<!-- If your service uses the AzureDiagnostics table in Azure Monitor Logs / Log Analytics, list what fields you use and what they are for. Azure Diagnostics is over 500 columns wide with all services using the fields that are consistent across Azure Monitor and then adding extra ones just for themselves.  If it uses service specific diagnostic table, refers to that table. If it uses both, put both types of information in. Most services in the future will have their own specific table. If you have questions, contact azmondocs@microsoft.com -->

Azure Application Gateway utiliza la tabla [Azure Diagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) para almacenar la información de registro de recursos. Las columnas siguientes son importantes.

**Diagnóstico de Azure**

| Propiedad | Descripción |
|:--- |:---|
requestUri_s | El URI de la solicitud de cliente|
Message | Mensajes informativos como "Ataque por inyección de código SQL"|
userAgent_s | Detalles del agente de usuario de la solicitud de cliente|
ruleName_s | Regla de enrutamiento de solicitudes que se usa para atender esta solicitud|
httpMethod_s | Método HTTP de la solicitud de cliente|
instanceId_s | Instancia de Appgw a la que se enruta la solicitud de cliente para su evaluación|
httpVersion_s | Versión HTTP de la solicitud de cliente|
clientIP_s | Dirección IP desde la que se realiza la solicitud|
host_s | Encabezado host de la solicitud cliente|
requestQuery_s | Cadena de consulta como parte de la solicitud de cliente|
sslEnabled_s | Si la solicitud de cliente tiene SSL habilitado|


## <a name="see-also"></a>Consulte también

<!-- replace below with the proper link to your main monitoring service article -->
- Consulte [Supervisión de Azure Application Gateway](monitor-application-gateway.md) para una descripción de la supervisión de Azure Application Gateway.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).