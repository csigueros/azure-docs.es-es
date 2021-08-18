---
title: Información de Digital Twins de IoT Plug and Play
description: Información de cómo IoT Plug and Play usa los gemelos digitales
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 5623192cd713f06be24ce6c3f78b91756868db86
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406561"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Información de Digital Twins de IoT Plug and Play

Un dispositivo IoT Plug and Play implementa un modelo descrito por el esquema de [Lenguaje de definición de Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Un modelo describe el conjunto de componentes, las propiedades, los comandos y los mensajes de telemetría que puede tener un dispositivo determinado.

IoT Plug and Play usa DTDL versión 2. Para más información sobre esta versión, consulte la especificación [Lenguaje de definición de Digital Twins (DTDL): versión 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) en GitHub.

> [!NOTE]
> DTDL no es exclusivo de IoT Plug and Play. Otros servicios de IoT, como [Azure Digital Twins](../digital-twins/overview.md), lo usan para representar entornos completos, como edificios y redes energéticas.

Los SDK de servicios IoT de Azure incluyen API que permiten que un servicio interactúe con el gemelo digital de un dispositivo. Por ejemplo, un servicio puede leer las propiedades del dispositivo del gemelo digital o usar el gemelo digital para llamar a un comando en un dispositivo. Para obtener más información, consulte [Ejemplos de gemelos digitales de IoT Hub](concepts-developer-guide-service.md#iot-hub-digital-twin-examples).

El dispositivo IoT Plug and Play de ejemplo de este artículo implementa un [modelo de controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) con componentes de [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json).

## <a name="device-twins-and-digital-twins"></a>Dispositivo gemelos y gemelos digitales

Además de un gemelo digital, Azure IoT Hub también mantiene un *dispositivo gemelo* para cada dispositivo conectado. Un dispositivo gemelo es similar a un gemelo digital, ya que es una representación de las propiedades de un dispositivo. Los SDK de servicios IoT de Azure incluyen API para interactuar con dispositivos gemelos.

Un centro de IoT inicializa un gemelo digital y un gemelo digital la primera vez que un dispositivo IoT Plug and Play se conecta.

Los dispositivos gemelos son documentos JSON que almacenan información acerca del estado del dispositivo, incluidos metadatos, configuraciones y condiciones. Para obtener más información, consulte [Ejemplos de cliente de servicio de IoT Hub](concepts-developer-guide-service.md#iot-hub-service-client-examples). Los creadores de dispositivos y soluciones pueden seguir usando el mismo conjunto de API y SDK de dispositivo gemelo para implementar dispositivos y soluciones con las convenciones de IoT Plug and Play.

Las API de gemelo digital funcionan en construcciones DTDL de nivel superior, como componentes, propiedades y comandos. Las API de gemelo digital facilitan la creación de soluciones de IoT Plug and Play.

En un dispositivo gemelo, el estado de una propiedad grabable se divide entre las secciones *propiedades deseadas* y *propiedades notificadas*. Todas las propiedades de solo lectura están disponibles en la sección de propiedades notificadas.

En un gemelo digital, hay una vista unificada del estado actual y deseado de la propiedad. El estado de sincronización de una propiedad determinada se almacena en la sección `$metadata` del componente predeterminado.

### <a name="device-twin-json-example"></a>Ejemplo de JSON de dispositivo gemelo

En el fragmento de código siguiente se muestra un dispositivo gemelo IoT Plug and Play con formato de objeto JSON:

```json
{
  "deviceId": "sample-device",
  "modelId": "dtmi:com:example:TemperatureController;1",
  "version": 15,
  "properties": {
    "desired": {
      "thermostat1": {
        "__t": "c",
        "targetTemperature": 21.8
      },
      "$metadata": {...},
      "$version": 4
    },
    "reported": {
      "serialNumber": "alwinexlepaho8329",
      "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "__t": "c",
        "targetTemperature": {
          "value": 21.8,
          "ac": 200,
          "ad": "Successfully executed patch",
        }
      },
      "$metadata": {...},
      "$version": 11
    }
  }
}
```

### <a name="digital-twin-example"></a>Ejemplo de gemelo digital

En el fragmento de código siguiente se muestra un gemelo digital con formato de objeto JSON:

```json
{
  "$dtId": "sample-device",
  "serialNumber": "alwinexlepaho8329",
  "thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
      "targetTemperature": {
        "desiredValue": 21.8,
        "desiredVersion": 4,
        "ackVersion": 4,
        "ackCode": 200,
        "ackDescription": "Successfully executed patch",
        "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
      },
      "maxTempSinceLastReboot": {
         "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:example:TemperatureController;1",
    "serialNumber": {
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

En la siguiente tabla se describen los campos del objeto JSON del gemelo digital:

| Nombre del campo | Descripción |
| --- | --- |
| `$dtId` | Cadena proporcionada por el usuario que representa el id. del gemelo digital. |
| `{propertyName}` | Valor de una propiedad en JSON. |
| `$metadata.$model` | [Opcional] Identificador de la interfaz del modelo que caracteriza al gemelo digital. |
| `$metadata.{propertyName}.desiredValue` | [Solo para propiedades grabables] Valor deseado de la propiedad especificada. |
| `$metadata.{propertyName}.desiredVersion` | [Solo para propiedades grabables] Versión del valor deseado que IoT Hub mantiene.|
| `$metadata.{propertyName}.ackVersion` | [Obligatorio, solo para propiedades grabables] Versión confirmada por el dispositivo que implementa el gemelo digital, debe ser mayor o igual que la versión deseada. |
| `$metadata.{propertyName}.ackCode` | [Obligatorio, solo para propiedades grabables] Código `ack` devuelto por la aplicación del dispositivo que implementa el gemelo digital. |
| `$metadata.{propertyName}.ackDescription` | [Opcional, solo para propiedades grabables] Descripción `ack` devuelta por la aplicación del dispositivo que implementa el gemelo digital. |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub conserva la marca de tiempo de la última actualización de la propiedad por parte del dispositivo. Las marcas de tiempo están en formato UTC y se codifican con el formato ISO8601 AAAA-MM-DDTHH:MM:SS.mmmZ. |
| `{componentName}` | Objeto JSON que contiene los valores de propiedad y los metadatos del componente. |
| `{componentName}.{propertyName}` | Valor de la propiedad del componente en formato JSON. |
| `{componentName}.$metadata` | Información de metadatos del componente. |

### <a name="properties"></a>Propiedades

Las propiedades son campos de datos que representan el estado de una entidad (como las propiedades de muchos lenguajes de programación orientados a objetos).

#### <a name="read-only-property"></a>Propiedad de solo lectura

Esquema DTDL:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

En este ejemplo, `alwinexlepaho8329` es el valor actual de la propiedad de solo lectura `serialNumber` que el dispositivo indica.
En los fragmentos de código siguientes se muestra la representación JSON en paralelo de la propiedad `serialNumber`:

:::row:::
   :::column span="":::
      **Dispositivo gemelo**

```json
"properties": {
  "reported": {
    "serialNumber": "alwinexlepaho8329"
  }
}
```

   :::column-end:::
   :::column span="":::
      **Gemelo digital**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Propiedad grabable

En los siguientes ejemplos se muestra una propiedad grabable en el componente predeterminado.

DTDL:

```json
{
  "@type": "Property",
  "name": "fanSpeed",
  "displayName": "Fan Speed",
  "writable": true,
  "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Dispositivo gemelo**

```json
{
  "properties": {
    "desired": {
      "fanSpeed": 2.0,
    },
    "reported": {
      "fanSpeed": {
        "value": 3.0,
        "ac": 200,
        "av": 1,
        "ad": "Successfully executed patch version 1"
      }
    }
  },
}
```

   :::column-end:::
   :::column span="":::
      **Gemelo digital**

```json
{
  "fanSpeed": 3.0,
  "$metadata": {
    "fanSpeed": {
      "desiredValue": 2.0,
      "desiredVersion": 2,
      "ackVersion": 1,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch version 1",
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

En este ejemplo, `3.0` es el valor actual de la propiedad `fanSpeed` que el dispositivo indica. `2.0` es el valor deseado establecido por la solución. El valor deseado y el estado de sincronización de una propiedad de nivel raíz se establecen dentro del elemento `$metadata` de nivel de raíz para un gemelo digital. Cuando el dispositivo está en línea, puede aplicar esta actualización e informar del valor actualizado.

### <a name="components"></a>Componentes

Los componentes permiten compilar una interfaz del modelo como un ensamblado de otras interfaces.
Por ejemplo, la interfaz de [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) puede incorporarse como componentes `thermostat1` y `thermostat2` en el [modelo de controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

En un dispositivo gemelo, un componente se identifica mediante el marcador `{ "__t": "c"}`. En un gemelo digital, la presencia de `$metadata` marca un componente.

En este ejemplo, `thermostat1` es un componente con dos propiedades:

- `maxTempSinceLastReboot` es una propiedad de solo lectura.
- `targetTemperature` es una propiedad grabable que el dispositivo ha sincronizado correctamente. El valor deseado y el estado de sincronización de estas propiedades se encuentran en el elemento `$metadata` del componente.

En los fragmentos de código siguientes se muestra la representación JSON en paralelo del componente `thermostat1`:

:::row:::
   :::column span="":::
      **Dispositivo gemelo**

```json
"properties": {
  "desired": {
    "thermostat1": {
      "__t": "c",
      "targetTemperature": 21.8
    },
    "$metadata": {
    },
    "$version": 4
  },
  "reported": {
    "thermostat1": {
      "maxTempSinceLastReboot": 25.3,
      "__t": "c",
      "targetTemperature": {
        "value": 21.8,
        "ac": 200,
        "ad": "Successfully executed patch",
        "av": 4
      }
    },
    "$metadata": {
    },
    "$version": 11
  }
}
```

   :::column-end:::
   :::column span="":::
      **Gemelo digital**

```json
"thermostat1": {
  "maxTempSinceLastReboot": 25.3,
  "targetTemperature": 21.8,
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 21.8,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch",
      "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
    },
    "maxTempSinceLastReboot": {
       "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>API de gemelo digital

Las API de gemelo digital incluyen las operaciones **Obtener gemelo digital**, **Actualizar gemelo digital**, **Invocar comando de componente** e **Invocar comando** para administrar un gemelo digital. Puede usar las [API de REST](/rest/api/iothub/service/digitaltwin) directamente o a través de un [SDK de servicio](../iot-develop/libraries-sdks.md).

## <a name="digital-twin-change-events"></a>Eventos de cambio de gemelo digital

Cuando se habilitan los eventos de cambio de gemelo digital, se desencadena un evento cada vez que cambia el valor actual o el deseado del componente o propiedad. Los eventos de cambio de gemelo digital se generan en formato de [revisión de JSON](http://jsonpatch.com/). Los eventos correspondientes se generan en el formato del dispositivo gemelo si se habilitan los eventos de cambio de gemelo.

Para información sobre cómo habilitar el enrutamiento para eventos de dispositivo digital y de gemelo digital, consulte [Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Para comprender el formato de los mensajes, consulte [Creación y lectura de mensajes de IoT Hub](../iot-hub/iot-hub-devguide-messages-construct.md).

Por ejemplo, se desencadena el evento de cambio de gemelo digital siguiente cuando `targetTemperature` se establece en la solución:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

Se desencadena el evento de cambio de gemelo digital siguiente cuando el dispositivo informa de que se ha aplicado el cambio deseado anterior:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> Los mensajes de notificación de cambios de gemelo se duplican cuando se activan en la notificación de cambio de dispositivo y de gemelo digital.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido acerca de los gemelos digitales, estos son algunos recursos adicionales:

- [Uso de las API de gemelo digital de IoT Plug and Play](howto-manage-digital-twin.md)
- [Interacción con un dispositivo desde su solución](tutorial-service.md)
- [API de REST de gemelo digital de IoT](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](../iot-fundamentals/howto-use-iot-explorer.md)