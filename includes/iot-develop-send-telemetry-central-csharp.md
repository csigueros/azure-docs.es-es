---
title: Archivo de inclusión
description: Archivo de inclusión
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 10/08/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 1a61f369d0987caac2cc821e37c1e5e1ed7cda40
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861287"
---
[![Examinar el código](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples)

En este artículo de inicio rápido, se explica un flujo de trabajo básico de desarrollo de aplicaciones de Azure IoT. En primer lugar, cree una aplicación de Azure IoT Central para hospedar dispositivos. A continuación, usará un ejemplo del SDK de dispositivo IoT de Azure para crear un controlador de temperatura, conectarlo de forma segura a IoT Central y enviar datos de telemetría. La aplicación de ejemplo del controlador de temperatura se ejecuta en la máquina local y genera datos del sensor simulados para enviarlos a IoT Central.

## <a name="prerequisites"></a>Prerrequisitos

Este inicio rápido se ejecuta en Windows, Linux y Raspberry Pi. Se ha probado en las siguientes versiones de sistema operativo y dispositivo:

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi SO versión 10 (Raspian) que se ejecuta en un Raspberry Pi 3 modelo B+

Instale los siguientes requisitos previos en la máquina de desarrollo:

- Si no tiene una suscripción de Azure, [cree una gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de comenzar.
- [Git](https://git-scm.com/downloads).
- SDK de .NET Core 3.1. Asegúrese de instalar el SDK de .NET, no solo el runtime. Para comprobar la versión del SDK de .NET y del entorno de ejecución instalados en el equipo, ejecute `dotnet --info`.

  - Para Windows y Linux (excepto Raspberry Pi), siga las instrucciones para [instalar el SDK de .NET Core 3.1](/dotnet/core/install/) en su plataforma.
  - Para Raspberry Pi, deberá seguir las instrucciones para [instalar manualmente el SDK](/dotnet/core/install/linux-scripted-manual#manual-install). Esto se debe a que en Debian, las instalaciones del administrador de paquetes del SDK de .NET solo se admiten para la arquitectura x64.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>Ejecución del ejemplo de dispositivo
En esta sección se configura el entorno local, se instalan los ejemplos de C# de Azure IoT y se ejecuta un ejemplo que crea un controlador de temperatura.

### <a name="configure-your-environment"></a>Configurar su entorno

1. Abra una consola como CMD de Windows, PowerShell o Bash.

1. Establezca las siguientes variables de entorno con los comandos adecuados para la consola. El dispositivo usa estos valores para conectarse a IoT Central. Para `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` y `IOTHUB_DEVICE_DPS_DEVICE_ID`, use los valores de conexión del dispositivo que guardó anteriormente.

    **CMD (Windows)**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > En el caso del símbolo de comandos de Windows, no hay comillas alrededor de los valores de variable.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>Instalación del SDK y los ejemplos

1. Clone el repositorio [Ejemplos de Microsoft Azure IoT para C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) en el equipo local.

    ```console
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Vaya al directorio de ejemplo.

    **Windows**

    ```console
    cd azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController
    ```

    **Sistema operativo Linux o Raspberry Pi**

    ```console
    cd azure-iot-samples-csharp/iot-hub/Samples/device/PnpDeviceSamples/TemperatureController
    ```

1. Instale el SDK de Azure IoT para C# y las dependencias necesarias:

    ```console
    dotnet restore
    ```

    Este comando instala las dependencias adecuadas, como se especifica en el archivo *TemperatureController.csproj*.

### <a name="run-the-code"></a>Ejecución del código

1. En la consola, ejecute el ejemplo de código. El ejemplo crea un controlador de temperatura con sensores de termostato.

    ```console
    dotnet run
    ```

    Una vez que el dispositivo se conecta a la aplicación IoT Central, se conecta a la instancia del dispositivo que creó en la aplicación y comienza a enviar telemetría. En la consola se muestran los detalles de conexión y la salida de telemetría:

    ```output
    [10/09/2021 00:29:18]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Press Control+C to quit the sample.
    [10/09/2021 00:29:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set up the device client.
    [10/09/2021 00:29:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Initializing via DPS
    [10/09/2021 00:29:38]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler for 'reboot' command.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Connection status change registered - status=Connected, reason=Connection_Ok.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler for "getMaxMinReport" command.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler to receive 'targetTemperature' updates.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - component = 'deviceInformation', properties update is complete.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - { "serialNumber": "SR-123456" } is complete.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Telemetry: Sent - component="thermostat1", { "temperature": 23.7 } in °C.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 23.7 } in °C is complete.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Telemetry: Sent - component="thermostat2", { "temperature": 25.8 } in °C.
    ```