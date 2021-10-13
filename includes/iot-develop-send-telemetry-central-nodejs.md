---
title: Archivo de inclusión
description: Archivo de inclusión
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 233a5df91d744b393736a0c9483c3fcb2b52c57a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725776"
---
[![Examinar el código](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript/pnp)

En este artículo de inicio rápido, se explica un flujo de trabajo básico de desarrollo de aplicaciones de Azure IoT. En primer lugar, cree una aplicación de Azure IoT Central para hospedar dispositivos. A continuación, utilizaremos un ejemplo de un SDK de dispositivo IoT de Azure para ejecutar un controlador de temperatura simulado, conectarlo de forma segura a IoT Central y enviar datos de telemetría.

## <a name="prerequisites"></a>Requisitos previos
Este inicio rápido se ejecuta en Windows, Linux y Raspberry Pi. Se ha probado en las siguientes versiones de sistema operativo y dispositivo:

- Windows 10
- Ubuntu 20.04 LTS que se ejecuta en Subsistema de Windows para Linux (WSL)
- Raspberry Pi OS, versión 10 (buster), que se ejecuta en un dispositivo Raspberry Pi 3 Model B+

Instale los siguientes requisitos previos en la máquina de desarrollo:

- [Node.js](https://nodejs.org/), versión 6 o posterior. Para comprobar la versión, ejecute `node --version` en la aplicación de consola.
- [Git](https://git-scm.com/downloads).

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>Ejecución de un dispositivo simulado
En esta sección, configurará el entorno local, instalará el SDK de dispositivo de Azure IoT para Node.js y ejecutará un ejemplo que crea un controlador de temperatura simulado.

### <a name="configure-your-environment"></a>Configurar su entorno

1. Abra una consola como CMD de Windows, PowerShell o Bash.

1. Establezca las siguientes variables de entorno con los comandos adecuados para la consola. El dispositivo simulado usa estos valores para conectarse a IoT Central. Para `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` y `IOTHUB_DEVICE_DPS_DEVICE_ID`, use los valores de conexión del dispositivo que guardó anteriormente.

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

1. Copie el SDK de dispositivo de Azure IoT para Node.js en la máquina local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Vaya al directorio de ejemplo.

    **Windows**
    ```console
    cd azure-iot-sdk-node\device\samples\javascript\pnp
    ```

    **Sistema operativo Linux o Raspberry Pi**
    ```console
    cd azure-iot-sdk-node/device/samples/javascript/pnp
    ```

1. Instale el SDK de Azure IoT para Node.js y las dependencias necesarias:
    ```console
    npm install
    ```

### <a name="run-the-code"></a>Ejecución del código

1. En la consola, ejecute el siguiente ejemplo de código desde el SDK. El ejemplo crea un controlador de temperatura simulado con sensores termostatos.
    ```console
    node pnpTemperatureController.js
    ```

    Una vez que el dispositivo simulado se conecta a la aplicación IoT Central, se conecta a la instancia de dispositivo que creó en la aplicación y comienza a enviar datos de telemetría. En la consola se muestran los detalles de conexión y la salida de telemetría: 
    
    ```output
    registration succeeded
    assigned hub=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net
    deviceId=my-sdk-device
    payload=undefined
    Connecting using connection string: HostName=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net;DeviceId=my-sdk-device;SharedAccessKey=xxxxxxxxxxxxxxxxxxxxxxxxxxx
    Enabling the commands on the client
    Please enter q or Q to exit sample.
    The following properties will be updated for root interface.
    { serialNumber: 'alwinexlepaho8329' }
    The following properties will be updated for component: thermostat1
    {
      thermostat1: { maxTempSinceLastReboot: 40.53506261527863, __t: 'c' }
    }
    The following properties will be updated for component: thermostat2
    {
      thermostat2: { maxTempSinceLastReboot: 89.55136974144273, __t: 'c' }
    }
    The following properties will be updated for component: deviceInformation
    {
      deviceInformation: {
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
        __t: 'c'
      }
    }
    executed sample
    Received an update for device with value: {"$version":1}
    Properties have been reported for root interface.
    Properties have been reported for component: thermostat1
    Properties have been reported for component: thermostat2
    Properties have been reported for component: deviceInformation
    Sending telemetry message 0 from component: thermostat1
    Sending telemetry message 0 from component: thermostat2
    ```