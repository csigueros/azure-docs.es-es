---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: faf4bd0bd87f71af8f62064297f8288b3977ec8d
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114405044"
---
Los recursos siguientes también están disponibles:

- [Documentación de referencia del SDK de Python](/python/api/azure-iot-hub/azure.iot.hub)
- [Ejemplos de cliente de servicio](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Ejemplos de Digital Twins](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Ejemplos de cliente de servicio de IOT Hub

En esta sección se muestran ejemplos de uso de Python del cliente de servicio IoT Hub y las clases **IoTHubRegistryManager** y **CloudToDeviceMethod**. Utilice la clase **IoTHubRegistryManager** para interactuar con el estado del dispositivo mediante el dispositivo gemelo. También puede utilizar la clase **IoTHubRegistryManager** para [consultar los registros de dispositivos](../articles/iot-hub/iot-hub-devguide-query-language.md) en IoT Hub. Use la clase **CloudToDeviceMethod** para llamar a comandos en el dispositivo. El modelo [DTDL](../articles/iot-develop/concepts-digital-twin.md) del dispositivo define las propiedades y los comandos que implementa el dispositivo. En los fragmentos de código, la variable `device_id` contiene el id. de dispositivo del dispositivo de IoT Plug and Play registrado en el centro de IoT.

### <a name="get-the-device-twin-and-model-id"></a>Obtención del dispositivo gemelo y el id. del modelo

Para obtener el dispositivo gemelo y el id. de modelo del dispositivo de IoT Plug and Play que se conectó a su centro de IoT:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Actualizar dispositivo gemelo

En el siguiente fragmento de código se muestra cómo actualizar la propiedad `targetTemperature` en el dispositivo. En el ejemplo se muestra qué debe hacer para obtener el `etag` del gemelo antes de actualizarlo. La propiedad se define en el componente predeterminado del dispositivo:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

En el siguiente fragmento se muestra cómo actualizar la propiedad `targetTemperature` en el componente. En el ejemplo se muestra qué debe hacer para obtener el `ETag` del gemelo antes de actualizarlo. La propiedad se define en el componente **thermostat1**:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

En el caso de una propiedad de un componente, la revisión de la propiedad es similar al siguiente ejemplo:

```json
{
"thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Llamar al comando

En el siguiente fragmento de código se muestra cómo invocar el comando `getMaxMinReport` definido en un componente predeterminado:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

En el siguiente fragmento de código se muestra cómo llamar al comando `getMaxMinReport` de un componente. El comando se define en el componente **thermostat1**:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>Ejemplos de gemelos digitales de IoT Hub

Utilice la clase **DigitalTwinClient** para interactuar con el estado del dispositivo mediante Digital Twins. El modelo [DTDL](../articles/iot-develop/concepts-digital-twin.md) del dispositivo define las propiedades y los comandos que implementa el dispositivo.

La variable `device_id` contiene el id. de dispositivo del dispositivo de IoT Plug and Play registrado en el centro de IoT.

### <a name="get-the-digital-twin-and-model-id"></a>Obtención del gemelo digital y el id. del modelo

Para obtener el gemelo digital y el id. de modelo del dispositivo de IoT Plug and Play que se conectó a su centro de IoT:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Actualización del gemelo digital

En el siguiente fragmento de código se muestra cómo actualizar la propiedad `targetTemperature` en el dispositivo. La propiedad se define en el componente predeterminado del dispositivo:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

En el siguiente fragmento se muestra cómo actualizar la propiedad `targetTemperature` en el componente. La propiedad se define en el componente **thermostat1**:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Llamar al comando

En el siguiente fragmento de código se muestra cómo invocar el comando `getMaxMinReport` definido en un componente predeterminado:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

En el siguiente fragmento de código se muestra cómo llamar al comando `getMaxMinReport` de un componente. El comando se define en el componente **thermostat1**:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Leer la telemetría de dispositivo

Los dispositivos de IoT Plug and Play envían la telemetría definida en el modelo DTDL a IoT Hub. De manera predeterminada, IoT Hub enruta la telemetría a un punto de conexión de Event Hubs en el que se puede consumir. Para más información, consulte [Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

En el siguiente fragmento de código se muestra cómo leer la telemetría del punto de conexión de Event Hubs predeterminado. El código de este fragmento de código se toma de la guía de inicio rápido de IoT Hub [Enviar telemetría desde un dispositivo a una instancia de IoT Hub y leerlo con una aplicación de back-end](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

En el siguiente resultado del código anterior se muestra la telemetría de temperatura enviada por el dispositivo sin componentes **Thermostat** de IoT Plug and Play que solo tiene el componente predeterminado. La propiedad `dt-dataschema` del sistema muestra el id. del modelo:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

En el siguiente resultado del código anterior se muestra la telemetría de temperatura enviada por el dispositivo de varios componentes **TemperatureController** de IoT Plug and Play. La propiedad `dt-subject` del sistema muestra el nombre del componente que envió la telemetría. En este ejemplo, los dos componentes son `thermostat1` y `thermostat2`, tal y como se define en el modelo DTDL. La propiedad `dt-dataschema` del sistema muestra el id. del modelo:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Leer las notificaciones de cambios de gemelo digital

Puede configurar IoT Hub para generar notificaciones de cambio dispositivo gemelo para enrutar a un punto de conexión compatible. Para más información, consulte [Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión > Eventos no telemétricos](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

El código que se muestra en el fragmento de código anterior (Python) genera el siguiente resultado cuando IoT Hub genera notificaciones de cambio de dispositivo gemelo para un dispositivo termostato sin componente. Las propiedades de la aplicación `iothub-message-schema` y `opType` proporcionan información sobre el tipo de notificación de cambio:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

El código que se muestra en el fragmento de código anterior (Python) genera el siguiente resultado cuando IoT Hub genera notificaciones de cambio de dispositivo gemelo para un dispositivo con componente. En este ejemplo se muestra el resultado cuando un dispositivo de sensor de temperatura con un componente termostato genera notificaciones. Las propiedades de la aplicación `iothub-message-schema` y `opType` proporcionan información sobre el tipo de notificación de cambio:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Leer las notificaciones de cambios de gemelo digital

Puede configurar IoT Hub para generar notificaciones de cambio gemelo digital para enrutar a un punto de conexión compatible. Para más información, consulte [Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión > Eventos no telemétricos](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

El código que se muestra en el fragmento de código anterior (Python) genera el siguiente resultado cuando IoT Hub genera notificaciones de cambio de gemelo digital para un dispositivo termostato sin componente. Las propiedades de la aplicación `iothub-message-schema` y `opType` proporcionan información sobre el tipo de notificación de cambio:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

El código que se muestra en el fragmento de código anterior (Python) genera el siguiente resultado cuando IoT Hub genera notificaciones de cambio de gemelo digital para un dispositivo con componente. En este ejemplo se muestra el resultado cuando un dispositivo de sensor de temperatura con un componente termostato genera notificaciones. Las propiedades de la aplicación `iothub-message-schema` y `opType` proporcionan información sobre el tipo de notificación de cambio:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```