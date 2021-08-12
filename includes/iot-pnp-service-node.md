---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: bd296000f95ee6a2bb73567a0de46472a7c05492
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403626"
---
IoT Plug and Play simplifica IoT, ya que permite interactuar con las funcionalidades de un dispositivo sin tener que conocer la implementación subyacente del mismo. En este inicio rápido se muestra cómo usar Node.js para conectarse y controlar un dispositivo IoT Plug and Play que esté conectado a una solución.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para completar este inicio rápido, necesita Node.js en la máquina de desarrollo. Puede descargar la última versión recomendada para varias plataformas desde [nodejs.org](https://nodejs.org).

Puede verificar la versión actual de Node.js en el equipo de desarrollo con el comando siguiente:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonación del repositorio de SDK con el código de ejemplo

Clone los ejemplos del [repositorio del SDK de Node.js](https://github.com/Azure/azure-iot-sdk-node). Abra una ventana de terminal en la carpeta que prefiera. Ejecute el siguiente comando para clonar el repositorio de GitHub del [SDK de Microsoft Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Ejecución del dispositivo de ejemplo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para más información sobre la configuración de ejemplo, consulte el [archivo Léame de ejemplo](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

En este inicio rápido, se usa un dispositivo termostato de ejemplo, que se ha escrito en Node.js, como dispositivo IoT Plug and Play. Para ejecutar el dispositivo de ejemplo:

1. Abra una ventana de terminal y vaya a la carpeta local que contiene el repositorio de SDK de Microsoft Azure IoT para Node.js que ha clonado de GitHub.

1. Esta ventana de terminal se usa ahora como terminal del **dispositivo**. Vaya a la carpeta del repositorio clonado y luego a la carpeta */azure-iot-sdk-node/device/samples/pnp*. Instale todas las dependencias, para lo que debe ejecutar el siguiente comando:

    ```cmd/sh
    npm install
    ```

1. Ejecute el dispositivo termostato de ejemplo con el siguiente comando:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Verá mensajes que señalan que el dispositivo ha enviado cierta información y ha indicado que está en línea. Estos mensajes indican que el dispositivo ha comenzado a enviar datos de telemetría al centro y que ya está listo para recibir comandos y actualizaciones de propiedades. No cierre este terminal, ya que lo necesita para confirmar que el ejemplo del servicio funciona.

## <a name="run-the-sample-solution"></a>Ejecución de la solución de ejemplo

En el artículo sobre la [configuración del entorno para los inicios rápidos y tutoriales de IoT Plug and Play](../articles/iot-develop/set-up-environment.md) se crearon dos variables de entorno para configurar el ejemplo para conectarse a su instancia de IoT Hub y al dispositivo:

* **IOTHUB_CONNECTION_STRING**: la cadena de conexión de IoT Hub que anotó anteriormente.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

En este inicio rápido, se usa una solución de IoT de ejemplo en Node.js para interactuar con el dispositivo de ejemplo que acaba de configurar.

1. Abra otra ventana de terminal para utilizarla como terminal de **servicio**.

1. En el repositorio del SDK de Node.js clonado, vaya a la carpeta */azure-iot-sdk-node/service/samples/javascript*. Instale todas las dependencias, para lo que debe ejecutar el siguiente comando:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Lectura de una propiedad

1. Al ejecutar el dispositivo termostato de ejemplo en el terminal del **dispositivo**, vio los siguientes mensajes que indicaban su estado en línea:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Vaya al terminal de **servicio** y use el siguiente comando para ejecutar el ejemplo para leer información del dispositivo:

    ```cmd/sh
    node twin.js
    ```

1. En la salida del terminal de **servicio**, observe la respuesta del gemelo. Se muestra el identificador de modelo del dispositivo y las propiedades asociadas notificadas:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. En el fragmento de código siguiente se muestra el código del archivo *twin.js*, que recupera el identificador de modelo del dispositivo gemelo:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

En este escenario, la salida es `Model Id: dtmi:com:example:Thermostat;1`.

> [!NOTE]
> En estos ejemplos de servicio se usa la clase **Registry** del **cliente del servicio IoT Hub**. Para más información sobre las API, incluida la API de gemelos digitales, consulte la [guía para desarrolladores de servicios](../articles/iot-develop/concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Actualización de una propiedad editable

1. Abra el archivo *twin.js* en un editor de código.

1. Examine el código de ejemplo, que muestra dos formas de actualizar el dispositivo. Para usar la primera, modifique la variable `twinPatch` como se indica a continuación:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    La propiedad `targetTemperature` se define como una propiedad grabable en el modelo del dispositivo termostato.

1. En el terminal de **servicio**, use el siguiente comando para ejecutar el ejemplo para actualizar la propiedad:

    ```cmd/sh
    node twin.js
    ```

1. En el terminal del **dispositivo**, se muestra que el dispositivo ha recibido la actualización:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. En el terminal de **servicio**, ejecute el siguiente comando para comprobar que la propiedad está actualizada:

    ```cmd/sh
    node twin.js
    ```

1. En la salida del terminal de **servicio**, en la sección de propiedades de `reported`, se ve que se ha notificado la actualización de la temperatura de destino. El dispositivo puede tardar cierto tiempo en finalizar la actualización. Repita este paso hasta que el dispositivo haya procesado la actualización de la propiedad:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Invocación de un comando

1. Abra *device_method.js* y examine el código.

1. Vaya al terminal de **servicio**. Use el siguiente comando para ejecutar el ejemplo para invocar el comando:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. La salida del terminal de **servicio** muestra la siguiente confirmación:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. En el terminal del **dispositivo**, se muestra que el comando se ha confirmado:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
