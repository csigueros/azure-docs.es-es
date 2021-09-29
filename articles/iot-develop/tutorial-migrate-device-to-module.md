---
title: 'Tutorial: Conexión de un módulo de Azure IoT Plug and Play genérico | Microsoft Docs'
description: 'Tutorial: Uso de código de un dispositivo de ejemplo de C# de IoT Plug and Play en un módulo genérico.'
author: lizross
ms.author: lizross
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 3ff845e1ef74e8bf1f98da519d8d2d9657755e96
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091129"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Tutorial: Conexión de un módulo IoT Plug and Play (C#)

En este tutorial se muestra cómo conectar un [módulo](../iot-hub/iot-hub-devguide-module-twins.md) IoT Plug and Play genérico.

Se dice que un dispositivo es de IoT Plug and Play si publica su id. de modelo cuando se conecta a un centro de IoT e implementa las propiedades y los métodos descritos en el modelo de Digital Twins Definition Language (DTDL) que identifica el id. de modelo. Para obtener más información sobre cómo usan los dispositivos un id. de modelo y DTDL, consulte la [Guía para desarrolladores de IoT Plug and Play](./concepts-developer-guide-device.md). Los módulos usan los id. de modelo y los modelos de DTDL de la misma manera.

Para mostrar cómo implementar un módulo de IoT Plug and Play, en este tutorial se muestra cómo:

> [!div class="checklist"]
> * Agregar un dispositivo con un módulo a su centro de IoT.
> * Convertir el dispositivo C# del termostato de ejemplo en un módulo genérico.
> * Utilizar el SDK del servicio para interactuar con el módulo.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este tutorial en Windows, instale el siguiente software en el entorno Windows local:

* [Visual Studio 2019 (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

Use la herramienta del explorador de Azure IoT para agregar un nuevo dispositivo denominado **my-module-device** en el centro de IoT.

Agregue un módulo denominado **my-module** a **my-module-device**:

1. En la herramienta del explorador de Azure IoT, vaya hasta el dispositivo **my-module-device**.

1. Seleccione **Identidad del módulo** y, a continuación, seleccione **+ Agregar**.

1. Escriba **my-module** como nombre de la identidad de módulo y seleccione **Guardar**.

1. En la lista de identidades de módulo, seleccione **my-module**. Copie la cadena de conexión principal. Usará esta cadena de conexión del módulo más adelante en este tutorial.

1. Seleccione la pestaña **Módulo gemelo** y observe que no hay ninguna propiedad deseada o notificada:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Descargar el código

Si todavía no lo ha hecho, clone el repositorio de GitHub del SDK de C# del dispositivo de Azure IoT Hub en el equipo local:

Abra un símbolo del sistema en la carpeta de su elección. Use el siguiente comando para clonar el repositorio de GitHub de los [ejemplos de C# de Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp) en esta ubicación:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Preparación del proyecto

Para abrir y preparar el proyecto de ejemplo:

1. Abra el archivo de proyecto *azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* en Visual Studio 2019.

1. En Visual Studio, vaya a **Project > Thermostat Properties > Debug** (Proyecto > Propiedades del termostato > Depurar). Después, agregue las siguientes variables de entorno al proyecto:

    | Nombre | Value |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | Esta es la cadena de conexión del módulo que anotó anteriormente. |

    Para más información sobre la configuración de ejemplo, consulte el [archivo Léame de ejemplo](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md).

## <a name="modify-the-code"></a>Modificará el código

Para modificar el código para que funcione como un módulo en lugar de un dispositivo:

1. En Visual Studio, abra *Parameter.cs* y modifique la línea que establece la variable **PrimaryConnectionString** tal como se indica a continuación:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. En Visual Studio, abra *Program.cs* y reemplace las siete instancias de la clase `DeviceClient` por la clase `ModuleClient`.

    > [!TIP]
    > Use la característica de búsqueda y reemplazo de Visual Studio con las opciones **Coincidir mayúsculas y minúsculas** y **Solo palabras completas** habilitadas para reemplazar `DeviceClient` por `ModuleClient`.

1. En Visual Studio, abra *Thermostat.cs* y reemplace ambas instancias de la clase `DeviceClient` por la clase `ModuleClient`, tal como se indica a continuación.

1. Guarde los cambios en los archivos modificados.

Si ejecuta el código y, a continuación, usa el explorador de Azure IoT para ver el módulo gemelo actualizado, verá el dispositivo gemelo actualizado con el id. del modelo y la propiedad notificada del módulo:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
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
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Interacción con un módulo de dispositivo

Los SDK del servicio le permiten recuperar el id. de modelo de los dispositivos y módulos de IoT Plug and Play conectados. Puede usar los SDK del servicio para establecer propiedades de escritura y comandos de llamada:

1. En otra instancia de Visual Studio, abra el proyecto *azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. En Visual Studio, vaya a **Project > Thermostat Properties > Debug** (Proyecto > Propiedades del termostato > Depurar). Después, agregue las siguientes variables de entorno al proyecto:

    | Nombre | Value |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | El valor que anotó al completar la [configuración del entorno](set-up-environment.md). |

    > [!TIP]
    > También puede encontrar la cadena de conexión del centro de IoT en la herramienta del explorador de Azure IoT.

1. Abra el archivo *Program.cs* y modifique la línea que llama a un comando de la siguiente manera:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. En el archivo *Program.cs*, modifique la línea que recupera el dispositivo gemelo de la manera siguiente:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Asegúrese de que el ejemplo de cliente del módulo se esté ejecutando y, a continuación, ejecute este ejemplo de servicio. La salida del ejemplo de servicio muestra el id. de modelo del dispositivo gemelo y la llamada de comando:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    La salida del cliente del módulo muestra la respuesta del controlador de comandos:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Conversión a un módulo de IoT Edge

Para convertir este ejemplo y que así funcione como un módulo de IoT Edge de IoT Plug and Play, debe incluir la aplicación en un contenedor. No es necesario realizar más cambios en el código. El entorno de ejecución de IoT Edge se encarga de insertar la variable de entorno de la cadena de conexión en el inicio. Para obtener más información, consulte [Uso de Visual Studio 2019 para desarrollar y depurar módulos para Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md).

Para obtener información sobre cómo implementar un módulo en contenedor, consulte:

* [Ejecución de Azure IoT Edge en máquinas virtuales Ubuntu](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Instalación del entorno de ejecución de Azure IoT Edge en sistemas Linux basados en Debian](../iot-edge/how-to-install-iot-edge.md).

Puede usar la herramienta del explorador de Azure IoT para ver:

* El id. de modelo del dispositivo de IoT Edge en el módulo gemelo.
* La telemetría del dispositivo de IoT Edge.
* Las actualizaciones de propiedades del módulo gemelo IoT Edge que desencadenan notificaciones de IoT Plug and Play.
* El módulo IoT Edge que reacciona a los comandos de IoT Plug and Play.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a conectar un dispositivo IoT Plug and Play con módulos a un centro de IoT. Para más información acerca de los modelos de dispositivo IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Guía para desarrolladores de modelado de IoT Plug and Play](./concepts-developer-guide-device.md)