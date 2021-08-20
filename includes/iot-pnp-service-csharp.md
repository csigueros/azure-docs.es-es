---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 1716bafa93359c61781d9981bffabe9ba46067dc
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403631"
---
IoT Plug and Play simplifica IoT, ya que permite interactuar con las funcionalidades de un dispositivo sin tener que conocer la implementación subyacente del mismo. En este inicio rápido se muestra cómo usar C# para conectarse y controlar un dispositivo IoT Plug and Play que esté conectado a la solución.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para completar este inicio rápido en Windows, es preciso que el siguiente software esté instalado en la máquina de desarrollo:

* [Visual Studio 2019 (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonación del repositorio de SDK con el código de ejemplo

Si completó el [Tutorial: Conexión a IoT Hub (C#) de una aplicación de ejemplo de dispositivo IoT Plug and Play que se ejecuta en Windows](../articles/iot-develop/tutorial-connect-device.md), ya ha clonado el repositorio.

Clone los ejemplos de Azure IoT del repositorio de GitHub para C#. Abra un símbolo del sistema en la carpeta de su elección. Ejecute el siguiente comando para clonar el repositorio de GitHub que cuenta con los [ejemplos de Microsoft Azure IoT para .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp):

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Ejecución del dispositivo de ejemplo

En este inicio rápido, deberá usar un dispositivo termostato de ejemplo que se ha escrito en C#, como dispositivo IoT Plug and Play. Para ejecutar el dispositivo de ejemplo:

1. Abra el archivo de proyecto *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* en Visual Studio 2019.

1. En Visual Studio, vaya a **Project > Thermostat Properties > Debug** (Proyecto > Propiedades del termostato > Depurar). Después, agregue las siguientes variables de entorno al proyecto:

    | Nombre | Value |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | El valor que anotó al completar la [configuración del entorno](../articles/iot-develop/set-up-environment.md). |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | El valor que anotó al completar la [configuración del entorno](../articles/iot-develop/set-up-environment.md). |

1. Ahora puede compilar el ejemplo en Visual Studio y ejecutarlo en modo de depuración.

1. Verá mensajes que señalan que el dispositivo ha enviado cierta información y ha indicado que está en línea. Estos mensajes indican que el dispositivo ha comenzado a enviar datos de telemetría al centro y que ya está listo para recibir comandos y actualizaciones de propiedades. No cierre esta instancia de Visual Studio, ya que la necesitará para confirmar que el ejemplo del servicio funciona.

## <a name="run-the-sample-solution"></a>Ejecución de la solución de ejemplo

En el artículo sobre la [configuración del entorno para los inicios rápidos y tutoriales de IoT Plug and Play](../articles/iot-develop/set-up-environment.md) se crearon dos variables de entorno para configurar el ejemplo para conectarse a su instancia de IoT Hub y al dispositivo:

* **IOTHUB_CONNECTION_STRING**: la cadena de conexión de IoT Hub que anotó anteriormente.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

En este inicio rápido debe usar una solución de IoT de ejemplo en C# para interactuar con el dispositivo de ejemplo que acaba de configurar.

1. En otra instancia de Visual Studio, abra el proyecto *azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. En Visual Studio, vaya a **Project > Thermostat Properties > Debug** (Proyecto > Propiedades del termostato > Depurar). Después, agregue las siguientes variables de entorno al proyecto:

    | Nombre | Value |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | El valor que anotó al completar la [configuración del entorno](../articles/iot-develop/set-up-environment.md). |

1. Ahora puede compilar el ejemplo en Visual Studio y ejecutarlo en modo de depuración.

### <a name="get-device-twin"></a>Obtener dispositivo gemelo

En el fragmento de código siguiente se muestra la forma en que la aplicación de servicio recupera el dispositivo gemelo:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> En este ejemplo se usa el espacio de nombres **Microsoft.Azure.Devices.Client** del **cliente del servicio IoT Hub**. Para más información sobre las API, incluida la API de gemelos digitales, consulte la [guía para desarrolladores de servicios](../articles/iot-develop/concepts-developer-guide-service.md).

Este código genera el siguiente resultado:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

En el fragmento de código siguiente se muestra cómo usar una *revisión* para actualizar las propiedades a través del dispositivo gemelo:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Este código genera el siguiente resultado desde el dispositivo cuando el servicio actualiza la propiedad `targetTemperature`:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Invocación de un comando

En el fragmento de código siguiente se muestra cómo llamar a un comando:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Este código genera el siguiente resultado desde el dispositivo cuando el servicio llama al comando `getMaxMinReport`:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```
