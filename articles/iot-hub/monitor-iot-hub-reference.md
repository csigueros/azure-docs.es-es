---
title: Supervisión de la referencia a datos de Azure IoT Hub
description: Material de referencia importante necesario al supervisar Azure IoT Hub
author: eross-msft
ms.author: lizross
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: 569d8aecd4b90fe7ad4cbe1513829c536682f8b4
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554491"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Supervisión de la referencia a datos de Azure IoT Hub

Para más información sobre la recopilación y el análisis de datos de supervisión para Azure IoT Hub, consulte [Supervisión de Azure IoT Hub](monitor-iot-hub.md).

## <a name="metrics"></a>Métricas

En esta sección se enumeran todas las métricas de la plataforma recopiladas automáticamente para Azure IoT Hub. El espacio de nombres del proveedor de recursos de las métricas de IoT Hub es **Microsoft.Devices** y el espacio de nombres del tipo es **IoTHubs**.

En las subsecciones siguientes se desglosan las métricas de la plataforma IoT Hub por categoría general y se enumeran por el nombre para mostrar con el que aparecen en Azure Portal. También se proporciona información relacionada con las métricas que aparecen en cada subsección.

Además, en la documentación de Azure Monitor, encontrará una sola tabla donde se muestran todas las métricas de la plataforma IoT Hub por el nombre de la métrica en [Microsoft.Devices/IotHubs](../azure-monitor/essentials/metrics-supported.md#microsoftdevicesiothubs). Tenga en cuenta que, en esta tabla, no se proporciona toda la información, como las [agregaciones admitidas](#supported-aggregations) en algunas métricas, disponible en este artículo.

Para información sobre las métricas admitidas por otros servicios de Azure, consulte [Métricas compatibles con Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

**Temas de esta sección**

- [Supervisión de la referencia a datos de Azure IoT Hub](#monitoring-azure-iot-hub-data-reference)
  - [Métricas](#metrics)
    - [Agregaciones compatibles](#supported-aggregations)
    - [Métricas de comandos de nube a dispositivo](#cloud-to-device-command-metrics)
    - [Métricas de métodos directos de nube a dispositivo](#cloud-to-device-direct-methods-metrics)
    - [Métricas de operaciones de nube a dispositivo gemelo](#cloud-to-device-twin-operations-metrics)
    - [Métricas de configuraciones](#configurations-metrics)
    - [Métricas de cuotas diarias](#daily-quota-metrics)
    - [Métricas de dispositivos](#device-metrics)
    - [Métricas de telemetría de dispositivos](#device-telemetry-metrics)
    - [Métricas de operaciones de dispositivo gemelo a nube](#device-to-cloud-twin-operations-metrics)
    - [Métricas de Event Grid](#event-grid-metrics)
    - [Métricas de trabajos](#jobs-metrics)
    - [Métricas de enrutamiento](#routing-metrics)
    - [Métricas de consultas de gemelos](#twin-query-metrics)
  - [Dimensiones de métricas](#metric-dimensions)
  - [Registros de recursos](#resource-logs)
    - [Conexiones](#connections)
    - [Telemetría del dispositivo](#device-telemetry)
    - [Comandos de nube a dispositivo](#cloud-to-device-commands)
    - [Operaciones de identidad de dispositivos](#device-identity-operations)
    - [Operaciones de carga de archivos](#file-upload-operations)
    - [Rutas](#routes)
    - [Operaciones gemelas del dispositivo a la nube](#device-to-cloud-twin-operations)
    - [Operaciones gemelas de la nube al dispositivo](#cloud-to-device-twin-operations)
    - [Consultas gemelas](#twin-queries)
    - [Operaciones de trabajos](#jobs-operations)
    - [Métodos directos](#direct-methods)
    - [Seguimiento distribuido (versión preliminar)](#distributed-tracing-preview)
      - [Registros D2C (dispositivo a nube) de IoT Hub](#iot-hub-d2c-device-to-cloud-logs)
      - [Registros de entrada de IoT Hub](#iot-hub-ingress-logs)
      - [Registros de salida de IoT Hub](#iot-hub-egress-logs)
    - [Configuraciones](#configurations)
    - [Flujos de dispositivos (versión preliminar)](#device-streams-preview)
  - [Tablas de registros de Azure Monitor](#azure-monitor-logs-tables)
  - [Vea también](#see-also)

### <a name="supported-aggregations"></a>Agregaciones compatibles

La columna **Tipo de agregación** de cada tabla corresponde a la agregación predeterminada que se usa cuando se selecciona la métrica de un gráfico o de una alerta.

   ![Captura de pantalla que muestra la agregación de métricas](./media/monitor-iot-hub-reference/aggregation-type.png)

Con la mayoría de las métricas, todos los tipos de agregación son válidos; sin embargo, con las métricas de recuento, aquellas con un valor en la columna **Unidad** de **Recuento**, solo algunas agregaciones son válidas. Las métricas de recuento pueden ser de dos tipos:

* Métricas de recuento de un **solo punto**: IoT Hub registra un solo punto de datos (básicamente, un 1) cada vez que se produce la operación medida. Luego, Azure Monitor suma estos puntos de datos a lo largo de la granularidad especificada. Ejemplos de métricas de un **solo punto** son *Telemetry messages sent* (Mensajes de telemetría enviados) y *C2D message deliveries completed* (Entregas de mensajes C2D completadas). En estas métricas, el único tipo de agregación de interés es Total (Suma). El portal le permite elegir los valores mínimo, máximo y promedio; sin embargo, estos valores siempre serán 1.

* Métricas de recuento de **instantáneas**: IoT Hub registra un recuento total cuando se produce la operación medida. Actualmente, hay tres métricas de **instantánea** emitidas por IoT Hub: *Total number of messages used* (Número total de mensajes utilizados), *Total devices (preview) (Dispositivos totales [versión preliminar])* y *Connected devices (preview)*  (Dispositivos conectados [versión preliminar]). Dado que estas métricas presentan una cantidad "total" cada vez que se emiten, no tiene sentido sumarlas a lo largo de la granularidad especificada. Azure Monitor le limita a seleccionar los valores promedio, mínimo y máximo del tipo de agregación de estas métricas.

### <a name="cloud-to-device-command-metrics"></a>Métricas de comandos de nube a dispositivo

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|C2D Messages Expired (preview) [Mensajes de C2D expirados (versión preliminar)]|C2DMessagesExpired|Count|Total|Número de mensajes de la nube al dispositivo expirados|None|
|C2D message deliveries completed (Entregas de mensajes de C2D completadas)|c2d.commands.egress.complete.success|Count|Total|Número de entregas de mensajes de la nube al dispositivo que el dispositivo ha completado correctamente.|None|
|C2D messages abandoned (Mensajes de C2D abandonados)|c2d.commands.egress.abandon.success|Count|Total|Número de mensajes de la nube al dispositivo que el dispositivo ha abandonado.|None|
|C2D messages rejected (Mensajes de C2D rechazados)|c2d.commands.egress.reject.success|Count|Total|Número de mensajes de la nube al dispositivo que el dispositivo ha rechazado.|None|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Métricas de métodos directos de nube a dispositivo

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Invocaciones al método directo con error|c2d.methods.failure|Count|Total|El número de todas las llamadas al método directo con error.|None|
|Tamaño de la solicitud de las invocaciones a métodos directos|c2d.methods.requestSize|Bytes|Average|El número de todas las solicitudes correctas del método directo.|None|
|Tamaño de la respuesta de las invocaciones a métodos directos|c2d.methods.responseSize|Bytes|Average|El número de todas las respuestas correctas del método directo.|None|
|Invocaciones correctas al método directo|c2d.methods.success|Count|Total|El número de todas las llamadas correctas al método directo.|None|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Métricas de operaciones de nube a dispositivo gemelo

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Lecturas gemelas con error del back-end|c2d.twin.read.failure|Count|Total|El recuento de todas las lecturas gemelas con error iniciadas por el back-end.|None|
|Actualizaciones gemelas con error del back-end|c2d.twin.update.failure|Count|Total|El recuento de todas las actualizaciones gemelas con error iniciadas por el back-end.|None|
|Tamaño de la respuesta de las lecturas gemelas del back-end|c2d.twin.read.size|Bytes|Average|El recuento de todas las lecturas gemelas correctas iniciadas por el back-end.|None|
|Tamaño de las actualizaciones gemelas del back-end|c2d.twin.update.size|Bytes|Average|El tamaño total de todas las actualizaciones gemelas correctas iniciadas por el back-end.|None|
|Lecturas gemelas correctas del back-end|c2d.twin.read.success|Count|Total|El recuento de todas las lecturas gemelas correctas iniciadas por el back-end.|None|
|Actualizaciones gemelas correctas del back-end|c2d.twin.update.success|Count|Total|El recuento de todas las actualizaciones gemelas correctas iniciadas por el back-end.|None|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="configurations-metrics"></a>Métricas de configuraciones

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Métricas de configuración|configuraciones|Count|Total|Número total de operaciones CRUD realizadas para la configuración de dispositivos y la implementación de IoT Edge en un conjunto de dispositivos de destino. Esto también incluye el número de operaciones que modifican el dispositivo gemelo o el módulo gemelo debido a estas configuraciones.|Ninguno|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Métricas de cuotas diarias

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Uso total de datos del dispositivo|deviceDataUsage|Bytes|Total|Bytes transferidos hacia y desde cualquier dispositivo conectado al Centro de IOT|None|
|Uso total de datos del dispositivo (versión preliminar)|deviceDataUsageV2|Bytes|Total|Bytes transferidos hacia y desde cualquier dispositivo conectado al Centro de IOT|None|
|Número total de mensajes usados|dailyMessageQuotaUsed|Count|Average|Número de mensajes totales usados hoy. Se trata de un valor acumulado que se restablece en cero a las 00:00 UTC cada día.|None|

En *Total number of messages used* (Número total de mensajes utilizados), solo se admiten agregaciones mínimas, máximas y promedio. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="device-metrics"></a>Métricas de dispositivos

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Total de dispositivos (en desuso)|devices.totalDevices|Count|Total|Número de dispositivos registrados en IoT Hub|None|
|Dispositivos conectados (en desuso) |devices.connectedDevices.allProtocol|Count|Total|Número de dispositivos conectados a IoT Hub|None|
|Total de dispositivos (versión preliminar)|totalDeviceCount|Count|Average|Número de dispositivos registrados en IoT Hub|None|
|Dispositivos conectados (versión preliminar)|connectedDeviceCount|Count|Average|Número de dispositivos conectados a IoT Hub|None|

En *Total devices (deprecated)* (Dispositivos totales [en desuso]) y *Connected devices (deprecated)* (Dispositivos conectados [en desuso]), solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

En *Total devices (preview)* (Dispositvos totales [versión preliminar]) y *Connected devices (preview)* (Dispositivos conectados [versión preliminar]), solo las agregaciones mínimas, máximas y promedio son válidas. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

Las métricas *Connected devices (preview)* (Dispositivos conectados [versión preliminar]) y *Total devices (preview)* (Dispositivos totales [versión preliminar]) no son exportables mediante la configuración de diagnóstico.

### <a name="device-telemetry-metrics"></a>Métricas de telemetría de dispositivos

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Número de errores de limitación|d2c.telemetry.ingress.sendThrottle|Count|Total|Número de errores de limitación debido a las limitaciones de rendimiento del dispositivo|None|
|Intentos de envío de mensajes de telemetría|d2c.telemetry.ingress.allProtocol|Count|Total|Número de mensajes de telemetría de dispositivo a la nube para enviar a IoT Hub|None|
|Mensajes de telemetría enviados|d2c.telemetry.ingress.success|Count|Total|Número de mensajes de telemetría de dispositivo a la nube enviados correctamente a IoT Hub|None|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Métricas de operaciones de dispositivo gemelo a nube

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Lecturas gemelas con error de los dispositivos|d2c.twin.read.failure|Count|Total|El recuento de todas las lecturas gemelas con error iniciadas por el dispositivo.|None|
|Actualizaciones gemelas con error de los dispositivos|d2c.twin.update.failure|Count|Total|El recuento de todas las actualizaciones gemelas con error iniciadas por el dispositivo.|None|
|Tamaño de la respuesta de las lecturas gemelas de dispositivos|d2c.twin.read.size|Bytes|Average|El número de todas las lecturas gemelas correctas iniciadas por el dispositivo.|None|
|Tamaño de las actualizaciones gemelas de los dispositivos|d2c.twin.update.size|Bytes|Average|El tamaño total de todas las actualizaciones gemelas correctas iniciadas por el dispositivo.|None|
|Lecturas gemelas correctas de los dispositivos|d2c.twin.read.success|Count|Total|El recuento de todas las lecturas gemelas correctas iniciadas por el dispositivo.|None|
|Actualizaciones gemelas correctas de los dispositivos|d2c.twin.update.success|Count|Total|El recuento de todas las actualizaciones gemelas correctas iniciadas por el dispositivo.|None|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="event-grid-metrics"></a>Métricas de Event Grid

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Entregas de Event Grid (versión preliminar)|EventGridDeliveries|Count|Total|El número de eventos de IoT Hub publicados en Event Grid. Use la dimensión de resultado para ver el número de solicitudes correctas e incorrectas. La dimensión EventType muestra el tipo de evento (https://aka.ms/ioteventgrid).|Result,<br/>EventType<br>*Para más información, consulte [Dimensiones de métricas](#metric-dimensions)* .|
|Latencia de Event Grid (versión preliminar)|EventGridLatency|Milisegundos|Average|La latencia media (milisegundos) desde el momento en que se generó el evento de IoT Hub hasta el momento en que se publicó el evento en Event Grid. Este número es un promedio entre todos los tipos de evento. Use la dimensión EventType para ver la latencia de un tipo específico de evento.|EventType<br>*Para más información, consulte [Dimensiones de métricas](#metric-dimensions)* .|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="jobs-metrics"></a>Métricas de trabajos

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Trabajos completados|jobs.completed|Count|Total|El recuento de todos los trabajos completados.|None|
|Llamadas con error para enumerar trabajos|jobs.listJobs.failure|Count|Total|El recuento de todas las llamadas con error para enumerar trabajos.|None|
|Creaciones con error de trabajos de invocación de método|jobs.createDirectMethodJob.failure|Count|Total|El recuento de todas las creaciones con error de los trabajos de invocación de método directos.|None|
|Creaciones con error de trabajos de actualización gemelos|jobs.createTwinUpdateJob.failure|Count|Total|El recuento de todas las creaciones con error de trabajos de actualización gemelos.|None|
|Cancelaciones de trabajos con error|jobs.cancelJob.failure|Count|Total|El recuento de todas las llamadas con error para cancelar un trabajo.|None|
|Consultas de trabajo con error|jobs.queryJobs.failure|Count|Total|El recuento de todas las llamadas con error para consultar trabajos.|None|
|Trabajos con error|jobs.failed|Count|Total|El recuento de todos los trabajos con error.|None|
|Llamadas correctas para enumerar trabajos|jobs.listJobs.success|Count|Total|El recuento de todas las llamadas correctas para enumerar trabajos.|None|
|Creaciones correctas de trabajos de invocación de método|jobs.createDirectMethodJob.success|Count|Total|El recuento de todas las creaciones correctas de los trabajos de invocación de método directos.|None|
|Creaciones correctas de trabajos de actualización gemelos|jobs.createTwinUpdateJob.<br>success|Count|Total|El recuento de todas las creaciones correctas de trabajos de actualización gemelos.|None|
|Cancelaciones de trabajos correctas|jobs.cancelJob.success|Count|Total|El recuento de todas las llamadas correctas para cancelar un trabajo.|None|
|Consultas de trabajo correctas|jobs.queryJobs.success|Count|Total|El recuento de todas las llamadas correctas para consultar trabajos.|None|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="routing-metrics"></a>Métricas de enrutamiento

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
| Intentos de entrega de enrutamiento (versión preliminar) |RoutingDeliveries | Count | Total |Esta es la métrica de entrega de enrutamiento. Use las dimensiones para identificar el estado de entrega para un punto de conexión específico o para un origen de enrutamiento específico.| Result,<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>EndpointName<br>*Para más información, consulte [Dimensiones de métricas](#metric-dimensions)* . |
| Tamaño de los datos de entrega de enrutamiento en bytes (versión preliminar)|RoutingDataSizeInBytesDelivered| Bytes | Total |Número total de bytes que IoT Hub enruta al punto de conexión personalizado y al punto de conexión integrado. Use las dimensiones para identificar el tamaño de los datos enrutados a un punto de conexión específico o para un origen de enrutamiento específico.| RoutingSource,<br>EndpointType<br>EndpointName<br>*Para más información, consulte [Dimensiones de métricas](#metric-dimensions)* .|
| Latencia de enrutamiento (versión preliminar) |RoutingDeliveryLatency| Milisegundos | Average |Esta es la métrica de latencia de entrega de enrutamiento. Use las dimensiones para identificar la latencia para un punto de conexión específico o para un origen de enrutamiento específico.| RoutingSource,<br>EndpointType,<br>EndpointName<br>*Para más información, consulte [Dimensiones de métricas](#metric-dimensions)* .|
|Enrutamiento: blobs entregados al almacenamiento|d2c.endpoints.egress.storage.blobs|Count|Total|Número de veces que el enrutamiento de IoT Hub ha entregado blobs a los puntos de conexión de almacenamiento.|None|
|Enrutamiento: datos entregados al almacenamiento|d2c.endpoints.egress.storage.bytes|Bytes|Total|Cantidad de datos (bytes) que el enrutamiento de IoT Hub ha entregado a los punto de conexión de almacenamiento.|None|
|Enrutamiento: latencia de mensajes para el centro de eventos|d2c.endpoints.latency.eventHubs|Milisegundos|Average|Latencia media (milisegundos) entre la entrada de mensajes a IoT Hub y la entrada de mensajes a puntos de conexión personalizados de tipo Centro de eventos. Esto no incluye las rutas de mensajes a un punto de conexión integrado (eventos).|None|
|Enrutamiento: latencia de mensajes de la cola de Service Bus|d2c.endpoints.latency.serviceBusQueues|Milisegundos|Average|Latencia media (milisegundos) entre la entrada de mensajes a IoT Hub y la entrada de mensajes a un punto de conexión de la cola de Service Bus.|None|
|Enrutamiento: latencia de mensajes del tema de Service Bus|d2c.endpoints.latency.serviceBusTopics|Milisegundos|Average|Latencia media (milisegundos) entre la entrada de mensajes a IoT Hub y la entrada de mensajes a un punto de conexión del tema de Service Bus.|None|
|Enrutamiento: latencia de mensajes para mensajes/eventos|d2c.endpoints.latency.builtIn.events|Milisegundos|Average|Latencia media (milisegundos) entre la entrada de mensajes a IoT Hub y la entrada de mensajes al punto de conexión integrado (mensajes/eventos) y a la ruta de reserva.|None|
|Enrutamiento: latencia de mensajes para almacenamiento|d2c.endpoints.latency.storage|Milisegundos|Average|Latencia media (milisegundos) entre la entrada de mensajes a IoT Hub y la entrada de mensajes a un punto de conexión de almacenamiento.|None|
|Enrutamiento: mensajes entregados al centro de eventos|d2c.endpoints.egress.eventHubs|Count|Total|Número de veces que el enrutamiento de IoT Hub ha entregado mensajes correctamente a los puntos de conexión personalizados de tipo Centro de eventos. Esto no incluye las rutas de mensajes a un punto de conexión integrado (eventos).|None|
|Enrutamiento: mensajes entregados a la cola de Service Bus|d2c.endpoints.egress.serviceBusQueues|Count|Total|Número de veces que el enrutamiento de IoT Hub ha entregado mensajes correctamente a los puntos de conexión de la cola de Service Bus.|None|
|Enrutamiento: mensajes entregados al tema de Service Bus|d2c.endpoints.egress.serviceBusTopics|Count|Total|Número de veces que el enrutamiento de IoT Hub ha entregado mensajes correctamente a los puntos de conexión del tema de Service Bus.|None|
|Enrutamiento: mensajes entregados a la reserva|d2c.telemetry.egress.fallback|Count|Total|Número de veces que el enrutamiento de IoT Hub ha entregado mensajes al punto de conexión asociado con la ruta de reserva.|None|
|Enrutamiento: mensajes entregados a mensajes/eventos|d2c.endpoints.egress.builtIn.events|Count|Total|Número de veces que el enrutamiento de IoT Hub ha entregado mensajes correctamente al punto de conexión integrado (mensajes/eventos) y a la ruta de reserva.|None|
|Enrutamiento: mensajes entregados al almacenamiento|d2c.endpoints.egress.storage|Count|Total|Número de veces que el enrutamiento de IoT Hub ha entregado mensajes correctamente a los puntos de conexión de almacenamiento.|None|
|Enrutamiento: mensajes de telemetría entregados|d2c.telemetry.egress.success|Count|Total|Número de veces que los mensajes se han entregado correctamente a todos los puntos de conexión mediante el enrutamiento de IoT Hub. Si un mensaje se enruta a varios puntos de conexión, este valor aumenta en uno por cada entrega correcta. Si un mensaje se enruta al mismo punto de conexión varias veces, este valor aumenta en uno por cada entrega correcta.|None|
|Enrutamiento: mensajes de telemetría quitados |d2c.telemetry.egress.dropped|Count|Total|Número de veces que se han quitado los mensajes con el enrutamiento de IoT Hub a causa de puntos de conexión inactivos. Este valor no cuenta los mensajes entregados a la ruta de reserva porque los mensajes quitados no se entregan allí.|None|
|Enrutamiento: mensajes de telemetría incompatibles|d2c.telemetry.egress.invalid|Count|Total|Número de veces que el enrutamiento de IoT Hub no ha podido entregar mensajes debido a una incompatibilidad con el punto de conexión. Un mensaje es incompatible con un punto de conexión cuando IoT Hub intenta entregar el mensaje a un punto de conexión y se produce un error no transitorio. Los mensajes no válidos no se vuelven a intentar. Este valor no incluye los reintentos.|None|
|Enrutamiento: mensajes de telemetría huérfanos |d2c.telemetry.egress.orphaned|Count|Total|Número de veces que los mensajes eran huérfanos a causa del enrutamiento de IoT Hub porque no coincidían con ninguna consulta de enrutamiento, cuando la ruta de reserva está deshabilitada.|None|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

### <a name="twin-query-metrics"></a>Métricas de consultas de gemelos

|Nombre de métrica para mostrar|Métrica|Unidad|Tipo de agregación|Descripción|Dimensions|
|---|---|---|---|---|---|
|Consultas gemelas con error|twinQueries.failure|Count|Total|El recuento de todas las consultas gemelas con error.|None|
|Consultas gemelas correctas|twinQueries.success|Count|Total|El recuento de todas las consultas gemelas correctas.|None|
|Tamaño de resultado de consultas gemelas|twinQueries.resultSize|Bytes|Average|El total del tamaño del resultado de todas las consultas gemelas correctas.|None|

En el caso de las métricas con un valor de **Unidad** de **Recuento**, solo la agregación total (suma) es válida. Las agregaciones mínimas, máximas y promedio siempre devuelven 1. Para más información, consulte las [agregaciones admitidas](#supported-aggregations).

## <a name="metric-dimensions"></a>Dimensiones de métricas

Azure IoT Hub tiene las siguientes dimensiones asociadas a algunas de sus métricas de enrutamiento y Event Grid.

|Nombre de dimensión | Descripción|
|---|---|
||
|**EndpointName**| El nombre del extremo.|
|**EndpointType**|Uno de los siguientes: **eventHubs**, **serviceBusQueues**, **cosmosDB**, **serviceBusTopics**. **builtin** o **blobStorage**.|
|**EventType**| Uno de los siguientes tipos de eventos de Event Grid: **Microsoft.Devices.DeviceCreated**. **Microsoft.Devices.DeviceDeleted**, **Microsoft.Devices.DeviceConnected**, **Microsoft.Devices.DeviceDisconnected** o **Microsoft.Devices.DeviceTelemetry**. Para más información, consulte [Tipos de eventos](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| Una de los siguientes: **invalid**, **dropped**, **orphaned** o **null**.|
|**Resultado**| **success** o **failure**.|
|**RoutingSource**| Mensajes del dispositivo<br>Eventos de cambio de gemelo<br>Eventos de ciclo de vida del dispositivo|

Para más información sobre las dimensiones de métricas, consulte [Métricas multidimensionales](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

## <a name="resource-logs"></a>Registros del recurso

En esta sección se enumeran todos los tipos y esquemas de categorías de registros de recursos recopilados para Azure IoT Hub. El proveedor de recursos y el tipo de todos los registros de IoT Hub es [Microsoft.Devices/IotHubs](../azure-monitor/essentials/resource-logs-categories.md#microsoftdevicesiothubs).

**Temas de esta sección**

- [Supervisión de la referencia a datos de Azure IoT Hub](#monitoring-azure-iot-hub-data-reference)
  - [Métricas](#metrics)
    - [Agregaciones compatibles](#supported-aggregations)
    - [Métricas de comandos de nube a dispositivo](#cloud-to-device-command-metrics)
    - [Métricas de métodos directos de nube a dispositivo](#cloud-to-device-direct-methods-metrics)
    - [Métricas de operaciones de nube a dispositivo gemelo](#cloud-to-device-twin-operations-metrics)
    - [Métricas de configuraciones](#configurations-metrics)
    - [Métricas de cuotas diarias](#daily-quota-metrics)
    - [Métricas de dispositivos](#device-metrics)
    - [Métricas de telemetría de dispositivos](#device-telemetry-metrics)
    - [Métricas de operaciones de dispositivo gemelo a nube](#device-to-cloud-twin-operations-metrics)
    - [Métricas de Event Grid](#event-grid-metrics)
    - [Métricas de trabajos](#jobs-metrics)
    - [Métricas de enrutamiento](#routing-metrics)
    - [Métricas de consultas de gemelos](#twin-query-metrics)
  - [Dimensiones de métricas](#metric-dimensions)
  - [Registros de recursos](#resource-logs)
    - [Conexiones](#connections)
    - [Telemetría del dispositivo](#device-telemetry)
    - [Comandos de nube a dispositivo](#cloud-to-device-commands)
    - [Operaciones de identidad de dispositivos](#device-identity-operations)
    - [Operaciones de carga de archivos](#file-upload-operations)
    - [Rutas](#routes)
    - [Operaciones gemelas del dispositivo a la nube](#device-to-cloud-twin-operations)
    - [Operaciones gemelas de la nube al dispositivo](#cloud-to-device-twin-operations)
    - [Consultas gemelas](#twin-queries)
    - [Operaciones de trabajos](#jobs-operations)
    - [Métodos directos](#direct-methods)
    - [Seguimiento distribuido (versión preliminar)](#distributed-tracing-preview)
      - [Registros D2C (dispositivo a nube) de IoT Hub](#iot-hub-d2c-device-to-cloud-logs)
      - [Registros de entrada de IoT Hub](#iot-hub-ingress-logs)
      - [Registros de salida de IoT Hub](#iot-hub-egress-logs)
    - [Configuraciones](#configurations)
    - [Flujos de dispositivos (versión preliminar)](#device-streams-preview)
  - [Tablas de registros de Azure Monitor](#azure-monitor-logs-tables)
  - [Vea también](#see-also)

### <a name="connections"></a>Conexiones

La categoría de conexiones realiza un seguimiento de eventos de conexión y desconexión de dispositivos desde un centro de IoT, y también de los errores. Esta categoría es útil para identificar intentos de conexión no autorizados o alertas cuando se pierde la conexión a dispositivos.

> [!NOTE]
> Para obtener el estado de conexión confiable de los dispositivos, consulte [Latido de dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Telemetría de dispositivo

La categoría de telemetría del dispositivo realiza el seguimiento de los errores que se producen en el centro de IoT y se relacionan con la canalización de telemetría. Esta categoría incluye los errores que se producen al enviar eventos de telemetría (por ejemplo, la limitación) y recibir eventos de telemetría (por ejemplo, un lector no autorizado). Esta categoría no puede detectar los errores causados por el código que se ejecuta en el propio dispositivo.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Comandos de nube a dispositivo

La categoría de comandos de nube a dispositivo realiza el seguimiento de los errores que se producen en el centro de IoT y se relacionan con la canalización de mensajes de nube a dispositivo. Esta categoría incluye errores que se producen a partir de:

* Envío de mensajes de la nube al dispositivo (como errores de remitente no autorizados),
* Recepción de mensajes de la nube al dispositivo (por ejemplo, errores al superarse el recuento de entregas) y
* Recepción de comentarios de mensajes de la nube al dispositivo (como errores por comentarios expirados).

Esta categoría no detecta errores cuando el mensaje de la nube al dispositivo se entrega correctamente, pero el dispositivo lo controla incorrectamente.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Operaciones de identidad de dispositivos

La categoría de operaciones de identidad de dispositivo supervisa los errores que se producen cuando se intenta crear, actualizar o eliminar una entrada en el registro de identidades de IoT Hub. El seguimiento de esta categoría resulta útil para los escenarios de aprovisionamiento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Operaciones de carga de archivos

La categoría de carga de archivos supervisa los errores que se producen en el centro de IoT y está relacionada con la funcionalidad de carga de archivos. Esta categoría incluye lo siguiente:

* Errores que se producen con el URI de SAS, como cuando caduca antes de que un dispositivo notifique al centro una carga completada.

* Cargas con errores notificadas por el dispositivo.

* Errores que se producen cuando no se encuentra un archivo en el almacenamiento durante la creación del mensaje de notificación de IoT Hub.

Esta categoría no puede detectar los errores que se producen directamente mientras el dispositivo está cargando un archivo a Storage.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Rutas

La categoría de [enrutamiento de mensajes](./iot-hub-devguide-messages-d2c.md) realiza un seguimiento de los errores que se producen durante la evaluación de este proceso y el estado del punto de conexión según lo que observa IoT Hub. Esta categoría incluye eventos tales como:

* Una regla se evalúa como "sin definir",
* IoT Hub marca un punto de conexión como fallido, o
* Cualquier error recibido desde un punto de conexión.

Esta categoría no incluye errores específicos de los mensajes (como los errores de limitación del dispositivo), que se notifican en la categoría de telemetría de dispositivo.

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Aquí encontrará más detalles sobre el enrutamiento de registros de recursos:

* [Lista de códigos de error del registro de recursos de enrutamiento](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lista de nombres de operaciones de registros de recursos](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Operaciones gemelas del dispositivo a la nube

La categoría de operaciones gemelas del dispositivo a la nube supervisa los eventos iniciados por el dispositivo en dispositivos gemelos. Estas operaciones pueden incluir la opción de obtener operaciones gemelas, actualizar las propiedades notificadas y suscribirse a las propiedades deseadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Operaciones gemelas de la nube al dispositivo

La categoría de operaciones gemelas de la nube al dispositivo supervisa los eventos iniciados por el servicio en dispositivos gemelos. Estas operaciones pueden incluir la opción de obtener operaciones gemelas y actualizar o reemplazar etiquetas y propiedades.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Consultas gemelas

La categoría de consultas gemelas informa sobre las solicitudes de consulta de los dispositivos gemelos que se inician en la nube.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Operaciones de trabajos

La categoría de operaciones de trabajos informa sobre las solicitudes de trabajo para actualizar los dispositivos gemelos o invocar métodos directos en varios dispositivos. Estas solicitudes se inician en la nube.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Métodos directos

La categoría de métodos directos realiza un seguimiento de las interacciones de solicitud y respuesta enviadas a dispositivos individuales. Estas solicitudes se inician en la nube.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Seguimiento distribuido (versión preliminar)

La categoría de seguimiento distribuido realiza un seguimiento de los identificadores de correlación correspondientes a los mensajes que contienen el encabezado de contexto de seguimiento. Para habilitar completamente estos registros, se debe actualizar el código del lado cliente mediante los pasos que se indican en [Análisis y diagnóstico de aplicaciones de IoT de un extremo a otro con el seguimiento distribuido de IoT Hub [versión preliminar]](iot-hub-distributed-tracing.md).

Observe que `correlationId` se ajusta a la propuesta de [contexto de seguimiento de W3C](https://github.com/w3c/trace-context), donde contiene un elemento `trace-id`, así como un elemento `span-id`.

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Registros D2C (dispositivo a nube) de IoT Hub

IoT Hub anota este registro cuando le llega un mensaje que contiene propiedades de seguimiento válidas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

En este caso, no se calcula `durationMs` dado que es posible que el reloj de IoT Hub no esté sincronizado con el reloj del dispositivo y, por tanto, el cálculo de la duración puede ser engañoso. Se recomienda escribir la lógica mediante las marcas de tiempo de la sección `properties` para capturar los picos en la latencia de dispositivo a nube.

| Propiedad | Tipo | Descripción |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Entero | Tamaño del mensaje de dispositivo a nube en bytes. |
| **deviceId** | Cadena de caracteres alfanuméricos ASCII de 7 bits. | Identidad del dispositivo |
| **callerLocalTimeUtc** | Marca de tiempo UTC | Hora de creación del mensaje según el reloj local del dispositivo. |
| **calleeLocalTimeUtc** | Marca de tiempo UTC | Hora de llegada del mensaje a la puerta de enlace de IoT Hub según el reloj del lado de servicio de IoT Hub. |

#### <a name="iot-hub-ingress-logs"></a>Registros de entrada de IoT Hub

IoT Hub anota este registro cuando un mensaje que contiene propiedades de seguimiento válidas se escribe en un centro de eventos interno o integrado.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

En la sección `properties`, este registro contiene información adicional sobre la entrada de mensajes.

| Propiedad | Tipo | Descripción |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Verdadero o falso, indica si está habilitado el enrutamiento de mensajes en IoT Hub. |
| **parentSpanId** | String | El valor de [span-id](https://w3c.github.io/trace-context/#parent-id) del mensaje principal, que, en este caso, sería el seguimiento de mensajes D2C. |

#### <a name="iot-hub-egress-logs"></a>Registros de salida de IoT Hub

IoT Hub anota este registro cuando está habilitado el [enrutamiento](iot-hub-devguide-messages-d2c.md) y el mensaje se escribe en un [punto de conexión](iot-hub-devguide-endpoints.md). Si no está habilitado el enrutamiento, IoT Hub no anota este registro.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

En la sección `properties`, este registro contiene información adicional sobre la entrada de mensajes.

| Propiedad | Tipo | Descripción |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | Nombre del punto de conexión de enrutamiento. |
| **endpointType** | String | Tipo del punto de conexión de enrutamiento. |
| **parentSpanId** | String | El valor de [span-id](https://w3c.github.io/trace-context/#parent-id) del mensaje principal, que, en este caso, sería el seguimiento de mensajes de entrada de IoT Hub. |

### <a name="configurations"></a>Configurations

Los registros de configuración de IoT Hub realizan el seguimiento de los eventos y errores del conjunto de características de administración automática de dispositivos.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Flujos de dispositivos (versión preliminar)

La categoría de flujos de dispositivos realiza el seguimiento de las interacciones de solicitud y respuesta enviadas a dispositivos individuales.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor
<!-- REQUIRED. Please keep heading in this order -->

En esta sección se hace referencia a todas las tablas de Kusto de registros de Azure Monitor relacionadas con Azure IoT Hub y que están disponibles para consulta por Log Analytics. Puede encontrar una lista de estas tablas y vínculos a más información sobre el tipo de recurso de IoT Hub en [IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) en la referencia de la tabla de registros de Azure Monitor.

Puede encontrar una referencia de todas las tablas de registros de Azure Monitor o de Log Analytics en [Referencia de la tabla de registros de Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Consulte también

* Consulte [Supervisión de Azure IoT Hub](monitor-iot-hub.md) para ver una descripción de la supervisión de Azure IoT Hub.
* Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
