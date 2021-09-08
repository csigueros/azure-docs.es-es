---
title: Supervisión y solución de problemas de desconexión con Azure IoT Hub
description: Aprenda a supervisar y solucionar errores comunes de conectividad de los dispositivos para Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 22c1658740af7ef7eccbeca02c6f98485ec11222
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112378311"
---
# <a name="monitor-diagnose-and-troubleshoot-azure-iot-hub-disconnects"></a>Supervisión, diagnóstico y solución de problemas de desconexión de Azure IoT Hub 

Los problemas de conectividad de los dispositivos IoT pueden ser difíciles de solucionar porque existen varios puntos de error posibles. La lógica de aplicación, las redes físicas, los protocolos, el hardware, IoT Hub y otros servicio en la nube pueden causar problemas. La capacidad de detectar e identificar el origen de un problema es fundamental. Sin embargo, una solución de IoT a gran escala puede tener miles de dispositivos, por lo que no es práctico comprobar manualmente cada dispositivo. IoT Hub se integra con dos servicios de Azure para ayudarle:

* **Azure Monitor** Azure Monitor permite recopilar, analizar y actuar sobre los datos de telemetría de IoT Hub. Para que le sea más fácil detectar, diagnosticar y solucionar estos problemas a gran escala, use las funcionalidades de supervisión que IoT Hub proporciona mediante Azure Monitor. Esto incluye la configuración de alertas para desencadenar notificaciones y acciones cuando se producen desconexiones y la configuración de los registros, que puede usar para detectar las condiciones que provocaron las desconexiones.

* **Azure Event Grid** Para la infraestructura crítica y las desconexiones por dispositivo, use Azure Event Grid para suscribirse a los eventos de conexión y desconexión de dispositivos emitidos por IoT Hub. Azure Event Grid permite usar cualquiera de los siguientes controladores de eventos:

  - Azure Functions
  - Logic Apps
  - Azure Automation
  - WebHooks
  - Queue Storage
  - conexiones híbridas
  - Event Hubs

## <a name="event-grid-vs-azure-monitor"></a>Event Grid frente a Azure Monitor

Event Grid proporciona una solución de supervisión por dispositivo de baja latencia que puede usar para realizar un seguimiento de las conexiones de los dispositivos para la infraestructura y los dispositivos críticos. Azure Monitor proporciona una métrica, *Dispositivos conectados*, que puede usar para supervisar el número de dispositivos conectados a IoT Hub y desencadenar una alerta cuando ese número caiga por debajo de un umbral estático.

Tenga en cuenta lo siguiente a la hora de decidir si usar Event Grid o Azure Monitor para un escenario determinado:

* Latencia de las alertas: los eventos de conexión de IoT Hub se entregan mucho más rápidamente mediante Event Grid. Esto hace que Event Grid sea mejor opción para los escenarios en los que es deseable una notificación rápida.

* Notificaciones por dispositivo: Event Grid proporciona la capacidad de realizar un seguimiento de las conexiones y desconexiones para dispositivos individuales. Esto hace que Event Grid sea mejor opción para los escenarios en los que es necesario supervisar las conexiones de los dispositivos críticos.

* Configuración ligera: las alertas de métricas de Azure Monitor proporcionan una experiencia de configuración ligera que no requiere la integración con otros servicios para ofrecer notificaciones por correo electrónico, SMS, voz y otras notificaciones.  Con Event Grid, debe realizar una integración con otros servicios de Azure para entregar notificaciones. Ambos servicios se pueden integrar con otros servicios para desencadenar acciones más complejas.

## <a name="event-grid-monitor-connect-and-disconnect-events"></a>Event Grid: supervisión de los eventos de conexión y desconexión

Para supervisar los eventos de conexión y desconexión en producción, se recomienda suscribirse a los [eventos **DeviceConnected** y **DeviceDisconnected**](iot-hub-event-grid.md#event-types) en Event Grid para desencadenar alertas y supervisar el estado de conexión de los dispositivos. Event Grid proporciona una latencia de eventos mucho menor que Azure Monitor y se puede supervisar dispositivo a dispositivo. Estos factores hacen de Event Grid el método preferido para supervisar la infraestructura y los dispositivos críticos.

Al usar Event Grid para supervisar o desencadenar alertas en las desconexiones de dispositivos, asegúrese de que crea una manera de filtrar las desconexiones periódicas debido a la renovación de tokens de SAS en los dispositivos que usan los SDK de Azure IoT. Para más información, consulte [Comportamiento de desconexión de los dispositivos MQTT con los SDK de Azure IoT](#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Explore los temas siguientes para más información sobre la supervisión de eventos de conexión de los dispositivos con Event Grid:

* Para obtener información general sobre el uso de Event Grid con IoT Hub, consulte [Reacción a eventos de IoT Hub usando Event Grid para desencadenar acciones](iot-hub-event-grid.md). Preste especial atención a la sección [Limitaciones de los eventos de dispositivo conectado y dispositivo desconectado](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events).

* Para ver un tutorial sobre la ordenación de los eventos de conexión de los dispositivos, consulte [Ordenación de eventos de conexión de dispositivos de Azure IoT Hub mediante Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* Para ver un tutorial sobre el envío de notificaciones por correo electrónico, consulte [Tutorial: Envío de notificaciones por correo electrónico sobre eventos de Azure IoT Hub mediante Event Grid y Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) en la documentación de Event Grid.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: enrutamiento de los eventos de conexión a los registros

IoT Hub emite continuamente registros de recursos para varias categorías de operaciones. Sin embargo, para recopilar estos datos de registro debe crear una configuración de diagnóstico para enrutarla a un destino en el que se puedan analizar o archivar. Uno de estos destinos son los registros de Azure Monitor mediante un área de trabajo de Log Analytics ([consulte los precios](https://azure.microsoft.com/pricing/details/log-analytics/)), donde puede analizar los datos mediante consultas de Kusto.

La [categoría conexiones de registros de recursos](monitor-iot-hub-reference.md#connections) de IoT Hub emite operaciones y errores relacionados con las conexiones de los dispositivos. En la captura de pantalla siguiente se muestra una configuración de diagnóstico para enrutar estos registros a un área de trabajo de Log Analytics:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Configuración recomendada para enviar los registros de conectividad al área de trabajo de Log Analytics.":::

Se recomienda crear una configuración de diagnóstico tan pronto como sea posible después de crear la instancia de IoT Hub, porque, aunque IoT Hub siempre emite registros de recursos, Azure Monitor no los recopila hasta que se enrutan a un destino.

Para más información sobre el enrutamiento de los registros a un destino, consulte [Recopilación y enrutamiento](monitor-iot-hub.md#collection-and-routing). Para obtener instrucciones detalladas para crear una configuración de diagnóstico, consulte [Tutorial: Configuración y uso de métricas y registros de diagnóstico con una instancia de IoT Hub](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnects"></a>Azure Monitor: configuración de alertas de métricas para la desconexión de dispositivos

Puede configurar alertas basadas en las métricas de la plataforma emitidas por IoT Hub. Con las alertas de métricas, puede notificar a los usuarios que se ha producido una condición de interés y también desencadenar acciones que pueden responder a esa condición automáticamente.

La métrica [*Dispositivos conectados (versión preliminar)*](monitor-iot-hub-reference.md#device-metrics) indica cuántos dispositivos están conectados a IoT Hub. Puede crear alertas que se desencadenarán si esta métrica cae por debajo de un valor de umbral:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Configuración de la lógica de alertas para la métrica de dispositivos conectados.":::

Puede usar reglas de alertas de métricas para supervisar las anomalías de desconexión de los dispositivo a gran escala. Es decir, use alertas para determinar cuando se desconecta inesperadamente un número significativo de dispositivos. Cuando se detecta, puede examinar los registros para ayudar a solucionar el problema. Sin embargo, para supervisar las desconexiones por dispositivo y las desconexiones de dispositivos críticos casi en tiempo real, debe usar Event Grid.

Para más información sobre las alertas con IoT Hub, consulte la sección Alertas de [Supervisión de Azure IoT Hub](monitor-iot-hub.md#alerts). Para ver un tutorial sobre la creación de alertas en IoT Hub, consulte [Tutorial: Configuración y uso de métricas y registros de diagnóstico con una instancia de IoT Hub](tutorial-use-metrics-and-diags.md). Para obtener información general más detallada sobre las alertas, consulte [Información general sobre las alertas en Microsoft Azure](../azure-monitor/alerts/alerts-overview.md) en la documentación de Azure Monitor.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: uso de los registros para resolver errores de conectividad

Cuando se detectan desconexiones de dispositivos mediante alertas de métricas de Azure Monitor o Event Grid, puede usar los registros para ayudar a solucionar el motivo. En esta sección se describe cómo buscar problemas comunes en los registros de Azure Monitor. En los pasos siguientes se supone que ya ha creado una [configuración de diagnóstico](#azure-monitor-route-connection-events-to-logs) para enviar los registros de conexiones de IoT Hub a un área de trabajo de Log Analytics.

Después de crear una configuración de diagnóstico para enrutar los registros de recursos de IoT Hub a los registros de Azure Monitor, siga estos pasos para ver los registros en Azure Portal.

1. Vaya al centro de IoT en [Azure Portal](https://portal.azure.com).

1. En **Supervisión**, en el panel izquierdo del centro de IoT, seleccione **Registros**.

1. Para aislar los registros de errores de conectividad de IoT Hub, escriba la consulta siguiente en el editor de consultas y, a continuación, seleccione **Ejecutar**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Si hay resultados, busque `OperationName`, `ResultType` (código de error) y `ResultDescription` (mensaje de error) para obtener más detalles.

   ![Ejemplo de registro de errores](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Use las siguientes guías de resolución de problemas para lograr ayuda con los errores más comunes:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Comportamiento de desconexión de los dispositivos MQTT con los SDK de Azure IoT

Los SDK de dispositivo IoT de Azure se desconectan de IoT Hub y luego se vuelven a conectar cuando renuevan los tokens de SAS mediante el protocolo MQTT (y MQTT sobre WebSockets). En los registros, esto se muestra como eventos informativos de conexión y desconexión de dispositivos, a veces acompañados por eventos de error.

De forma predeterminada, la duración del token es de 60 minutos para todos los SDK; sin embargo, los desarrolladores pueden cambiarla en algunos de los SDK. En la tabla siguiente se resume la duración de los tokens, la renovación de tokens y el comportamiento de renovación de tokens para cada uno de los SDK:

| SDK | Duración del token | Renovación de tokens | Comportamiento de renovación |
|-----|----------|---------------------|---------|
| .NET | 60 minutos, configurable | 85 % de la duración, configurable | El SDK se desconecta y se vuelve a conectar cuando se agota la duración del token, más un período de gracia de 10 minutos. Se generan eventos informativos y errores en los registros. |
| Java | 60 minutos, configurable | 85 % de la duración, no configurable | El SDK se desconecta y se vuelve a conectar cuando se agota la duración del token, más un período de gracia de 10 minutos. Se generan eventos informativos y errores en los registros. |
| Node.js | 60 minutos, configurable | Configurable | El SDK se desconecta y vuelve a conectar al renovar el token. Solo se generan eventos informativos en los registros. |
| Python | 60 minutos, configurable | 120 segundos antes de la expiración | El SDK se desconecta y vuelve a conectar en la duración del token. |

Las capturas de pantallas siguientes muestran el comportamiento de renovación de tokens en los registros de Azure Monitor para los diferentes SDK. La duración del token y el umbral de renovación se han cambiado con respecto a sus valores predeterminados, tal como se indicó.

* SDK de dispositivo para .NET con un tiempo de duración del token de 1200 segundos (20 minutos) y la renovación establecida para que se produzca en el 90 % de la duración. Las desconexiones se producen cada 30 minutos:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Comportamiento de error para la renovación de tokens sobre MQTT en los registros de Azure Monitor con el SDK para .NET.":::

* SDK para Java con un tiempo de duración del token de 300 segundos (5 minutos) y la renovación establecida para que se produzca en el 85 % de la duración. Las desconexiones se producen cada 15 minutos:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Comportamiento de error para la renovación de tokens sobre MQTT en los registros de Azure Monitor con el SDK para Java.":::

* SDK para Node con un tiempo de duración del token de 300 segundos (5 minutos) y la renovación del token establecida para que se produzca a los 3 minutos. Las desconexiones se producen al renovar el token. Además, no hay ningún error, solo se emiten eventos informativos de conexión y desconexión:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Comportamiento de error para la renovación de tokens sobre MQTT en los registros de Azure Monitor con el SDK para Node.":::

Se ha utilizado la consulta siguiente para recopilar los resultados. La consulta extrae el nombre y la versión del SDK del contenedor de propiedades. Para más información, consulte [Versión del SDK en los registros de IoT Hub](monitor-iot-hub.md#sdk-version-in-iot-hub-logs).

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

Como desarrollador u operador de soluciones IoT, debe tener en cuenta este comportamiento con el fin de interpretar los eventos de conexión y desconexión y los errores relacionados en los registros. Si desea cambiar la duración del token o el comportamiento de renovación de los dispositivos, compruebe si el dispositivo implementa una configuración de dispositivo gemelo o un método de dispositivo que lo haga posible.

Si va a supervisar conexiones de dispositivos con Event Hubs, asegúrese de que crea una forma de filtrar las desconexiones periódicas debido a la renovación de tokens de SAS. Por ejemplo, no desencadene acciones basadas en desconexiones, siempre que al evento de desconexión lo siga un evento de conexión dentro de un intervalo de tiempo determinado.

> [!NOTE]
> IoT Hub solo admite una conexión MQTT activa por dispositivo. Todas las conexiones MQTT nuevas en nombre del mismo identificador de dispositivo provocarán que IoT Hub cierre la existente.
>
> Se registrará el error 400027 ConnectionForcefullyClosedOnNewConnection en los registros de IoT Hub.

## <a name="i-tried-the-steps-but-they-didnt-work"></a>He intentado estos pasos, pero no funcionan

Si los pasos anteriores no le son de utilidad, intente lo siguiente:

* Si tiene acceso a los dispositivos problemáticos, tanto físicamente como en remoto (por ejemplo, SSH), siga la [Guía de solución de problemas de dispositivos](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) para continuar solucionando los problemas.

* Compruebe que los dispositivos están **habilitados** en Azure Portal > su instancia de IoT Hub > Dispositivos IoT.

* Si el dispositivo usa el protocolo MQTT, compruebe que el puerto 8883 está abierto. Para más información, consulte [Conexión a IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Obtenga ayuda en la [Página de preguntas y respuestas de Microsoft sobre Azure IoT Hub](/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) o el [soporte técnico de Azure](https://azure.microsoft.com/support/options/).

Con el fin de mejorar la documentación para los usuarios, deje un comentario a continuación si esta guía no le ha ayudado.

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de cómo resolver problemas transitorios, consulte [Control de errores transitorios](/azure/architecture/best-practices/transient-faults).

* Para más información sobre el SDK de Azure IoT y la administración de reintentos, consulte [Administración de la conectividad y mensajería confiable mediante los SDK de dispositivo de Azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
