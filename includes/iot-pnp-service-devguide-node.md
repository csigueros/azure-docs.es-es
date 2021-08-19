---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 974dc0488e358581bc6c0ee1798c079026d75cca
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114405045"
---
Los recursos siguientes también están disponibles:

- [Documentación de referencia del SDK de Node.js](/javascript/api/azure-iothub)
- [Ejemplos de cliente de servicio](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Ejemplos de Digital Twins](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Ejemplos de cliente de servicio de IOT Hub

En esta sección se muestran ejemplos de JavaScript en los que se usa el cliente de servicio de IoT Hub y las clases **Registry** y **Client**. Use la clase **Registry** para interactuar con el estado del dispositivo mediante dispositivos gemelos. También puede usar la clase **Registry** para [consultar los registros de dispositivos](../articles/iot-hub/iot-hub-devguide-query-language.md) en el centro de IoT. Use la clase **Client** para llamar a comandos en el dispositivo. El modelo [DTDL](../articles/iot-develop/concepts-digital-twin.md) del dispositivo define las propiedades y los comandos que implementa el dispositivo. En los fragmentos de código, la variable `deviceId` contiene el id. de dispositivo del dispositivo de IoT Plug and Play registrado en el centro de IoT.

### <a name="get-the-device-twin-and-model-id"></a>Obtención del dispositivo gemelo y el id. del modelo

Para obtener el dispositivo gemelo y el id. de modelo del dispositivo de IoT Plug and Play que se conectó a su centro de IoT:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Actualizar dispositivo gemelo

En el siguiente fragmento de código se muestra cómo actualizar la propiedad `targetTemperature` en el dispositivo. En el ejemplo se muestra cómo debe obtener el gemelo antes de actualizarlo. La propiedad se define en el componente predeterminado del dispositivo:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

En el siguiente fragmento se muestra cómo actualizar la propiedad `targetTemperature` en el componente. En el ejemplo se muestra cómo debe obtener el gemelo antes de actualizarlo. La propiedad se define en el componente **thermostat1**:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
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

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

En el siguiente fragmento de código se muestra cómo llamar al comando `getMaxMinReport` de un componente. El comando se define en el componente **thermostat1**:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>Ejemplos de gemelos digitales de IoT Hub

Utilice la clase **DigitalTwinClient** para interactuar con el estado del dispositivo mediante Digital Twins. El modelo [DTDL](../articles/iot-develop/concepts-digital-twin.md) del dispositivo define las propiedades y los comandos que implementa el dispositivo.

En esta sección se muestran ejemplos de JavaScript en los que se usa la API Digital Twins.

La variable `digitalTwinId` contiene el id. de dispositivo del dispositivo de IoT Plug and Play registrado en el centro de IoT.

### <a name="get-the-digital-twin-and-model-id"></a>Obtención del gemelo digital y el id. del modelo

Para obtener el gemelo digital y el id. de modelo del dispositivo de IoT Plug and Play que se conectó a su centro de IoT:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Actualización del gemelo digital

En el siguiente fragmento de código se muestra cómo actualizar la propiedad `targetTemperature` en el dispositivo. La propiedad se define en el componente predeterminado del dispositivo:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

En el siguiente fragmento se muestra cómo actualizar la propiedad `targetTemperature` en el componente. La propiedad se define en el componente **thermostat1**:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Llamar al comando

En el siguiente fragmento de código se muestra cómo invocar el comando `getMaxMinReport` definido en un componente predeterminado:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

En el siguiente fragmento de código se muestra cómo llamar al comando `getMaxMinReport` de un componente. El comando se define en el componente **thermostat1**:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Leer la telemetría de dispositivo

Los dispositivos de IoT Plug and Play envían la telemetría definida en el modelo DTDL a IoT Hub. De manera predeterminada, IoT Hub enruta la telemetría a un punto de conexión de Event Hubs en el que se puede consumir. Para más información, consulte [Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

En el siguiente fragmento de código se muestra cómo leer la telemetría del punto de conexión de Event Hubs predeterminado. El código de este fragmento de código se toma de la guía de inicio rápido de IoT Hub [Enviar telemetría desde un dispositivo a una instancia de IoT Hub y leerlo con una aplicación de back-end](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

En el siguiente resultado del código anterior se muestra la telemetría de temperatura enviada por el dispositivo de varios componentes **TemperatureController** de IoT Plug and Play. La propiedad `dt-subject` del sistema muestra el nombre del componente que envió la telemetría. En este ejemplo, los dos componentes son `thermostat1` y `thermostat2`, tal y como se define en el modelo DTDL. La propiedad `dt-dataschema` del sistema muestra el id. del modelo:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Leer las notificaciones de cambios de gemelo digital

Puede configurar IoT Hub para generar notificaciones de cambio dispositivo gemelo para enrutar a un punto de conexión compatible. Para más información, consulte [Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión > Eventos no telemétricos](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

El código que se muestra en el fragmento de código de JavaScript anterior genera la salida siguiente cuando IoT Hub genera notificaciones de cambio de dispositivo gemelo para un dispositivo de termostato sin componente. Las propiedades de la aplicación `iothub-message-schema` y `opType` proporcionan información sobre el tipo de notificación de cambio:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

El código que se muestra en el fragmento de código de JavaScript anterior genera la salida siguiente cuando IoT Hub genera notificaciones de cambio de dispositivo gemelo para un dispositivo con componentes. En este ejemplo se muestra el resultado cuando un dispositivo de sensor de temperatura con un componente termostato genera notificaciones. Las propiedades de la aplicación `iothub-message-schema` y `opType` proporcionan información sobre el tipo de notificación de cambio:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Leer las notificaciones de cambios de gemelo digital

Puede configurar IoT Hub para generar notificaciones de cambio gemelo digital para enrutar a un punto de conexión compatible. Para más información, consulte [Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión > Eventos no telemétricos](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

El código que se muestra en el fragmento de código de JavaScript anterior genera la salida siguiente cuando IoT Hub genera notificaciones de cambio de gemelo digital para un dispositivo de termostato sin componente. Las propiedades de la aplicación `iothub-message-schema` y `opType` proporcionan información sobre el tipo de notificación de cambio:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

El código que se muestra en el fragmento de código de JavaScript anterior genera la salida siguiente cuando IoT Hub genera notificaciones de cambio de gemelo digital para un dispositivo con componentes. En este ejemplo se muestra el resultado cuando un dispositivo de sensor de temperatura con un componente termostato genera notificaciones. Las propiedades de la aplicación `iothub-message-schema` y `opType` proporcionan información sobre el tipo de notificación de cambio:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```