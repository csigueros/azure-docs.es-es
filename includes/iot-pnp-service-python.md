---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 8cbfbc0f020504b17d9c2cbe3ca7d3f807ec28cd
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403637"
---
IoT Plug and Play simplifica IoT, ya que permite interactuar con el modelo de un dispositivo sin tener que conocer la implementación subyacente del dispositivo. En este inicio rápido se muestra cómo usar Python para conectarse y controlar un dispositivo IoT Plug and Play que esté conectado a la solución.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para completar este inicio rápido, necesita Python 3.7 en la máquina de desarrollo. Puede descargar la última versión recomendada para varias plataformas desde [python.org](https://www.python.org/). Puede comprobar la versión de Python con el siguiente comando:  

```cmd/sh
python --version
```

El paquete **azure-iot-device** se publica como un PIP.

En el entorno local de Python, instale el paquete como se indica a continuación:

```cmd/sh
pip install azure-iot-device
```

Para instalar el paquete **azure-iot-hub** ejecute el comando siguiente:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Ejecución del dispositivo de ejemplo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para más información sobre la configuración de ejemplo, consulte el [archivo Léame de ejemplo](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

En este inicio rápido, se usa un dispositivo termostato de ejemplo, que se ha escrito en Python, como dispositivo IoT Plug and Play. Para ejecutar el dispositivo de ejemplo:

1. Abra una ventana de terminal en la carpeta que prefiera. Ejecute el siguiente comando para clonar el repositorio de GitHub del [SDK de Python de IoT de Azure](https://github.com/Azure/azure-iot-sdk-python) en esta ubicación:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Esta ventana de terminal se usa ahora como terminal del **dispositivo**. Vaya a la carpeta del repositorio clonado y luego a la carpeta */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Ejecute el dispositivo termostato de ejemplo con el siguiente comando:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Verá mensajes que señalan que el dispositivo ha enviado cierta información y ha indicado que está en línea. Estos mensajes indican que el dispositivo ha comenzado a enviar datos de telemetría al centro y que ya está listo para recibir comandos y actualizaciones de propiedades. No cierre este terminal, ya que lo necesita para confirmar que el ejemplo del servicio funciona.

## <a name="run-the-sample-solution"></a>Ejecución de la solución de ejemplo

En este inicio rápido, se usa una solución de IoT de ejemplo en Python para interactuar con el dispositivo de ejemplo que acaba de configurar.

1. Abra otra ventana de terminal para utilizarla como terminal de **servicio**.

1. Vaya a la carpeta */azure-iot-sdk-python/azure-iot-hub/samples* del repositorio del SDK de Python clonado.

1. Abra el archivo *registry_manager_pnp_sample.py* y examine el código. En este ejemplo se muestra cómo usar la clase **IoTHubRegistryManager** para interactuar con un dispositivo IoT Plug and Play.

> [!NOTE]
> En estos ejemplos de servicio se usa la clase **IoTHubRegistryManager** del **cliente del servicio IoT Hub**. Para más información sobre las API, incluida la API de gemelos digitales, consulte la [guía para desarrolladores de servicios](../articles/iot-develop/concepts-developer-guide-service.md).

### <a name="get-the-device-twin"></a>Obtención del dispositivo gemelo

En el artículo sobre la [configuración del entorno para los inicios rápidos y tutoriales de IoT Plug and Play](../articles/iot-develop/set-up-environment.md) se crearon dos variables de entorno para configurar el ejemplo para conectarse a su instancia de IoT Hub y al dispositivo:

* **IOTHUB_CONNECTION_STRING**: la cadena de conexión de IoT Hub que anotó anteriormente.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Use el siguiente comando en el terminal de **servicio** para ejecutar este ejemplo:

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Si ejecuta el ejemplo en Linux, use `export` en lugar de `set`.

La salida muestra el dispositivo gemelo e imprime su identificador de modelo:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

En el fragmento de código siguiente se muestra el código de ejemplo de *registry_manager_pnp_sample.py*:

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Actualización de un dispositivo gemelo

En este ejemplo se muestra cómo actualizar la propiedad grabable `targetTemperature` en el dispositivo:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

Puede comprobar que la actualización está aplicada en el terminal del **dispositivo** que muestra la salida siguiente:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

El terminal de **servicio** confirma que la revisión se realizó correctamente:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Invocación de un comando

Luego, el ejemplo invoca el comando:

El terminal de **servicio** muestra un mensaje de confirmación del dispositivo:

```cmd/sh
The device method has been successfully invoked
```

En el terminal del **dispositivo**, puede ver que el dispositivo recibe el comando:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
