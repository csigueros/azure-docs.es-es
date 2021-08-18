---
title: Desarrollo para la plataforma Android Things con SDK de Azure IoT | Microsoft Docs
description: 'Guía para desarrolladores: aprenda a desarrollar en Android Things mediante los SDK de Azure IoT Hub.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 11f5e699651270b4dec898400cb0d2e756fdee1e
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516543"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Desarrollo para la plataforma Android Things con SDK de Azure IoT

Los [SDK de Azure IoT Hub](./iot-hub-devguide-sdks.md) proporcionan compatibilidad de primer nivel para plataformas populares, entre otras: Windows, Linux, OSX, MBED y plataformas móviles, como iOS y Android.  Como parte de nuestro compromiso para permitir más opciones y mayor flexibilidad en las implementaciones de IoT, el SDK de Java también admite la plataforma [Android Things](https://developer.android.com/things/).  Los desarrolladores pueden aprovechar las ventajas del sistema de operativo Android Things en el dispositivo, a la vez de usar [Azure IoT Hub](about-iot-hub.md) como central de mensajes que se puede escalar a millones de dispositivos conectados simultáneamente.

En este tutorial se describen los pasos para crear una aplicación de dispositivo en Android Things mediante el SDK de Java de Azure IoT.

## <a name="prerequisites"></a>Requisitos previos

* Hardware compatible con Android Things con sistema operativo Android Things.  Puede seguir la [documentación de Android Things](https://developer.android.com/things/get-started/kits#flash-at) sobre cómo instalar la imagen del sistema operativo Android Things.  Asegúrese de que el dispositivo Android Things esté conectado a Internet con periféricos esenciales, como un teclado, una pantalla y un mouse.  En este tutorial se usa Raspberry Pi 3.

* Versión más reciente de [Android Studio](https://developer.android.com/studio/)

* Versión más reciente de [Git](https://git-scm.com/)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar un dispositivo

Debe registrar un dispositivo con IoT Hub antes de poder conectarlo. En esta guía de inicio rápido, usará Azure Cloud Shell para registrar un dispositivo simulado.

1. Ejecute los siguientes comandos en Azure Cloud Shell para agregar la extensión de la CLI de IoT Hub y para crear la identidad del dispositivo.

   **YourIoTHubName**: reemplace este marcador de posición por el nombre que eligió para su centro de IoT.

   **MyAndroidThingsDevice**: es el nombre que se da al dispositivo registrado. Use MyAndroidDevice como se muestra. Si elige otro nombre para el dispositivo, tendrá que usarlo en todo el artículo y actualizar el nombre del dispositivo en las aplicaciones de ejemplo antes de ejecutarlas.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Ejecute los siguientes comandos en Azure Cloud Shell para obtener la *cadena de conexión del dispositivo* que acaba de registrar. Reemplace `YourIoTHubName` a continuación por el nombre que ha elegido para su centro de IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Anote la cadena de conexión del dispositivo, que se parecerá a esta:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor más adelante en este inicio rápido.

## <a name="building-an-android-things-application"></a>Compilación de una aplicación de Android Things

1. El primer paso para compilar una aplicación de Android Things es conectar los dispositivos con Android Things. Conecte el dispositivo con Android Things a una pantalla y conéctelo a Internet. Android Things ofrece [documentación](https://developer.android.com/things/get-started/kits) sobre cómo conectarse a Wi-Fi. Después de haberse conectado a Internet, anote la dirección IP que aparece en Networks (Redes).

2. Use la herramienta [adb](https://developer.android.com/studio/command-line/adb) para conectarse a su dispositivo con Android Things con la dirección IP que anotó anteriormente. Compruebe la conexión con este comando desde el terminal. Debería ver los dispositivos como "connected" (conectado).

   ```
   adb devices
   ```

3. Descargue nuestro ejemplo para Android/Android Things desde este [repositorio](https://github.com/Azure-Samples/azure-iot-samples-java) o use Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. En Android Studio, abra el proyecto de Android ubicado en "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Abra el archivo gradle.properties y reemplace "Device_connection_string" con la cadena de conexión del dispositivo que anotó anteriormente.
 
6. Haga clic en Run (Ejecutar) - Debug (Depurar) y seleccione el dispositivo para implementar este código en los dispositivos con Android Things.

7. Cuando la aplicación se inicie correctamente, podrá ver una aplicación que se ejecuta en el dispositivo con Android Things. Esta aplicación de ejemplo envía lecturas de temperatura generadas aleatoriamente.

## <a name="read-the-telemetry-from-your-hub"></a>Lectura de los datos de telemetría procedentes de su instancia de IoT Hub

También puede ver los datos a través del centro de IoT a medida que se reciben. La extensión de la CLI de IoT Hub se puede conectar al punto de conexión **Eventos** del lado servicio en su instancia de IoT Hub. La extensión recibe los mensajes del dispositivo a la nube enviados desde el dispositivo simulado. Normalmente, una aplicación back-end de IoT Hub se ejecuta en la nube para recibir y procesar los mensajes del dispositivo a la nube.

Ejecute los siguientes comandos en Azure Cloud Shell y reemplace `YourIoTHubName` por el nombre del centro de IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

* Información acerca de la [administración de la conectividad y la mensajería confiable](iot-hub-reliability-features-in-sdks.md) mediante los SDK de IoT Hub.
* Aprenda cómo [desarrollar para plataformas móviles](iot-hub-how-to-develop-for-mobile-devices.md) como iOS y Android.
