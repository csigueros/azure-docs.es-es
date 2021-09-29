---
title: Azure IoT Hub como origen de Event Grid
description: En este artículo se proporcionan las propiedades y los esquemas de los eventos de Azure IoT Hub. Enumera los tipos de eventos disponibles, un evento de ejemplo y las propiedades de evento.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: ac3cac72cc9998d4fb78ed0459e5e07df1125df0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606330"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Azure IoT Hub como origen de Event Grid
En este artículo se proporcionan las propiedades y los esquemas de los eventos de Azure IoT Hub. Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md). 

## <a name="available-event-types"></a>Tipos de eventos disponibles

Azure IoT Hub emite los siguientes tipos de eventos:

| Tipo de evento | Descripción |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Se publica cuando se registra un dispositivo en una instancia de IoT Hub. |
| Microsoft.Devices.DeviceDeleted | Se publica cuando se elimina un dispositivo de una instancia de IoT Hub. | 
| Microsoft.Devices.DeviceConnected | Se publica cuando se conecta un dispositivo a una instancia de IoT Hub. |
| Microsoft.Devices.DeviceDisconnected | Se publica cuando se desconecta un dispositivo de una instancia de IoT Hub. | 
| Microsoft.Devices.DeviceTelemetry | Se publica cuando se envía un mensaje de telemetría a un centro de IoT. |

## <a name="example-event"></a>Evento de ejemplo

# <a name="event-grid-event-schema"></a>[Esquema de eventos de Event Grid](#tab/event-grid-event-schema)

El esquema para los eventos de dispositivo conectado y dispositivo desconectado tiene la misma estructura. Este evento de ejemplo muestra el esquema de un evento que se genera cuando se conecta un dispositivo a una instancia de IoT Hub:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

El evento DeviceTelemetry se genera cuando se envía un evento de telemetría a IoT Hub. A continuación se muestra un esquema de ejemplo de este evento.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Los esquemas de los eventos DeviceCreated y DeviceDeleted tienen la misma estructura. Este evento de ejemplo muestra el esquema de un evento que se genera cuando se registra un dispositivo en una instancia de IoT Hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Esquema de eventos en la nube](#tab/cloud-event-schema)

El esquema para los eventos de dispositivo conectado y dispositivo desconectado tiene la misma estructura. Este evento de ejemplo muestra el esquema de un evento que se genera cuando se conecta un dispositivo a una instancia de IoT Hub:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceConnected", 
  "time": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "specversion": "1.0"
}]
```

El evento DeviceTelemetry se genera cuando se envía un evento de telemetría a IoT Hub. A continuación se muestra un esquema de ejemplo de este evento.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceTelemetry",
  "time": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "specversion": "1.0"
}]
```

Los esquemas de los eventos DeviceCreated y DeviceDeleted tienen la misma estructura. Este evento de ejemplo muestra el esquema de un evento que se genera cuando se registra un dispositivo en una instancia de IoT Hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "type": "Microsoft.Devices.DeviceCreated",
  "time": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>Propiedades de evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos de Event Grid](#tab/event-grid-event-schema)

Todos los eventos contienen los mismos datos de nivel superior: 

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| `id` | string | Identificador único para el evento |
| `topic` | string | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| `subject` | string | Ruta al asunto del evento definida por el anunciante. |
| `eventType` | string | Uno de los tipos de eventos registrados para este origen de eventos. |
| `eventTime` | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| `data` | object | Datos del evento de IoT Hub.  |
| `dataVersion` | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| `metadataVersion` | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

# <a name="cloud-event-schema"></a>[Esquema de eventos en la nube](#tab/cloud-event-schema)

Todos los eventos contienen los mismos datos de nivel superior: 


| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| `id` | string | Identificador único para el evento |
| `source` | string | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| `subject` | string | Ruta al asunto del evento definida por el anunciante. |
| `type` | string | Uno de los tipos de eventos registrados para este origen de eventos. |
| `time` | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| `data` | object | Datos del evento de IoT Hub.  |
| `specversion` | string | Versión de especificación del esquema CloudEvents. |

---

Para todos los eventos de IoT Hub, el objeto de datos contiene las siguientes propiedades:

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| `hubName` | string | Nombre de la instancia de IoT Hub en que se creó o eliminó el dispositivo. |
| `deviceId` | string | Identificador único del dispositivo. Una cadena que distingue mayúsculas de minúsculas puede tener una longitud de hasta 128 caracteres alfanuméricos ASCII de 7 bits más los siguientes caracteres especiales: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

El contenido del objeto de datos es diferente para cada publicador de eventos. 

Para los eventos de IoT Hub de **Dispositivo conectado** y **Dispositivo desconectado**, el objeto de datos contiene las siguientes propiedades:

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| `moduleId` | string | Identificador único del módulo. Este campo solo es la salida de los dispositivos del módulo. Una cadena que distingue mayúsculas de minúsculas puede tener una longitud de hasta 128 caracteres alfanuméricos ASCII de 7 bits más los siguientes caracteres especiales: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| `deviceConnectionStateEventInfo` | object | Información de evento del estado de conexión del dispositivo
| `sequenceNumber` | string | Un número que indica el orden de los eventos de dispositivo conectado o dispositivo desconectado. El evento más reciente tendrá un número de secuencia mayor que el del evento anterior. Este número puede variar en más de 1, pero el orden es siempre ascendente. Consulte [cómo usar el número de secuencia](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

En el caso del evento de Azure IoT Hub **Telemetría de dispositivo**, el objeto de datos contiene el mensaje del dispositivo a nube en [formato de mensaje de IoT Hub](../iot-hub/iot-hub-devguide-messages-construct.md) y tiene las siguientes propiedades:

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| `body` | string | El contenido del mensaje del dispositivo. |
| `properties` | string | Las propiedades de la aplicación son cadenas definidas por el usuario que se pueden agregar al mensaje. Estos campos son opcionales. |
| `system properties` | string | Las [propiedades del sistema](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) ayudan a identificar el contenido y el origen de los mensajes. El mensaje de telemetría del dispositivo debe estar en un formato JSON válido, el valor de contentType debe ser JSON y el de contentEncoding debe ser UTF-8 en las propiedades del sistema del mensaje. Si no es así, IoT Hub escribirá los mensajes en formato codificado de base 64.  |

Para los eventos de IoT Hub de **Dispositivo creado** y **Dispositivo eliminado**, el objeto de datos contiene las siguientes propiedades:

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| `twin` | object | Información acerca del dispositivo gemelo, que es la representación en la nube de los metadatos del dispositivo de la aplicación. | 
| `deviceID` | string | Identificador único del dispositivo gemelo. | 
| `etag` | string | Un validador para garantizar la coherencia de las actualizaciones de un dispositivo gemelo. Se garantiza que cada etiqueta de entidad sea exclusiva de cada dispositivo gemelo. |  
| `deviceEtag` | string | Un validador para garantizar la coherencia de las actualizaciones de un registro de dispositivo. Se garantiza que cada deviceEtag sea exclusiva de cada registro de dispositivo. |
| `status` | string | Si el dispositivo gemelo está habilitado o deshabilitado. | 
| `statusUpdateTime` | string | La marca de tiempo ISO8601 de la última actualización de estado del dispositivo gemelo. |
| `connectionState` | string | Si el dispositivo está conectado o desconectado. | 
| `lastActivityTime` | string | La marca de tiempo ISO8601 de la última actividad. | 
| `cloudToDeviceMessageCount` | integer | Número de mensajes de la nube al dispositivo enviados a este dispositivo. | 
| `authenticationType` | string | Tipo de autenticación usado para este dispositivo: `SAS`, `SelfSigned`o `CertificateAuthority`. |
| `x509Thumbprint` | string | La huella digital es un valor exclusivo del certificado x509, que suele usarse para buscar un certificado concreto en un almacén de certificados. La huella digital se genera de forma dinámica con el algoritmo SHA1 y no existe físicamente en el certificado. | 
| `primaryThumbprint` | string | Huella digital principal del certificado x509. |
| `secondaryThumbprint` | string | Huella digital secundaria del certificado x509. | 
| `version` | integer | Un entero que se incrementa en uno cada vez que se actualiza el dispositivo gemelo. |
| `desired` | object | Una parte de las propiedades que solo el back-end de la aplicación puede escribir y que el dispositivo puede leer. | 
| `reported` | object | Una parte de las propiedades que solo el dispositivo puede escribir y que el back-end de la aplicación puede leer. |
| `lastUpdated` | string | La marca de tiempo ISO8601 de la última actualización de propiedades del dispositivo gemelo. | 

## <a name="tutorials-and-how-tos"></a>Tutoriales y procedimientos
|Título  |Descripción  |
|---------|---------|
| [Envío de notificaciones por correo electrónico sobre eventos de Azure IoT Hub mediante Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Una aplicación lógica envía un correo electrónico de notificación cada vez que se agrega un dispositivo a IoT Hub. |
| [Reacción a eventos de IoT Hub usando Event Grid para desencadenar acciones](../iot-hub/iot-hub-event-grid.md) | Información general sobre la integración de IoT Hub con Event Grid. |
| [Ordenación de los eventos de dispositivo conectado y dispositivo desconectado](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Muestra cómo ordenar los eventos de estado de conexión del dispositivo. |

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información sobre cómo IoT Hub y Event Grid funcionan de forma conjunta, vea [Reacción a eventos de IoT Hub mediante Event Grid para desencadenar acciones](../iot-hub/iot-hub-event-grid.md).