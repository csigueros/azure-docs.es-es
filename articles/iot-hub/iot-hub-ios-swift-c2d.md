---
title: Mensajes de nube a dispositivo con Azure IoT Hub (iOS) | Microsoft Docs
description: Cómo enviar mensajes de la nube a un dispositivo desde una instancia de Azure IoT Hub mediante los SDK de IoT de Azure para iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: 627342bb4ee7a6192b6dd7b9fdce71a87158ec39
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287688"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Envío de mensajes de nube a dispositivo con IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

IoT Hub de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una solución de back-end. En el inicio rápido [Envío de telemetría desde un dispositivo a un centro de IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md) se muestra cómo crear un centro de IoT, aprovisionar en él la identidad del dispositivo y codificar una aplicación de dispositivo simulado que envíe mensajes del dispositivo a la nube.

En este tutorial se muestra cómo realizar las siguientes acciones:

* Desde el back-end de la nube de la aplicación, envíe mensajes de nube a dispositivo en un único dispositivo a través de IoT Hub.

* Reciba mensajes de nube a dispositivo en un dispositivo.

* Desde la solución de back-end, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde IoT Hub.

Encontrará más información sobre los mensajes de la nube al dispositivo en la [sección de mensajes de la guía para desarrolladores de IoT Hub](iot-hub-devguide-messaging.md).

Al final de este artículo, se ejecutan dos proyectos de iOS con Swift:

* **sample-device**, la aplicación que se creó en [Send telemetry from a device to an IoT hub](../iot-develop/quickstart-send-telemetry-iot-hub.md) (Envío de telemetría de un dispositivo a IoT Hub), que se conecta al centro de IoT y recibe mensajes de la nube al dispositivo.

* **sample-service**, que envía un mensaje de la nube a la aplicación de dispositivo simulado mediante IoT Hub y recibe la confirmación de entrega.

> [!NOTE]
> IoT Hub ofrece compatibilidad con SDK en muchas plataformas de dispositivos y lenguajes (entre los que se incluyen C, Java, Python y Javascript), mediante los SDK de dispositivo IoT de Azure. Visite el [Centro para desarrolladores de IoT de Azure](https://www.azure.com/develop/iot)para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure activa. (En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos).

* Una instancia de Azure IoT Hub activa en Azure.

* El código de ejemplo de [Ejemplos de Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* La versión más reciente de [XCode](https://developer.apple.com/xcode/) que, a su vez, ejecute la última versión del SDK de iOS. Esta guía de inicio rápido se probó con XCode 9.3 e iOS 11.3.

* La versión más reciente de [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Asegúrese de que el puerto 8883 está abierto en el firewall. En el ejemplo de dispositivo de este artículo se usa el protocolo MQTT, que se comunica mediante el puerto 8883. Este puerto puede estar bloqueado en algunos entornos de red corporativos y educativos. Para más información y saber cómo solucionar este problema, consulte [Conexión a IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Simulación de un dispositivo IoT

En esta sección se simula un dispositivo iOS que ejecuta una aplicación con Swift para recibir mensajes de la nube al dispositivo desde IoT Hub. 

Este es el dispositivo de ejemplo que se creó en el artículo [Enviar telemetría desde un dispositivo a IoT Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md). Si ya lo tiene en ejecución, puede omitir esta sección.

### <a name="install-cocoapods"></a>Instalación de CocoaPods

CocoaPods administra las dependencias de los proyectos de iOS que utilizan bibliotecas de terceros.

En una ventana del terminal, vaya a la carpeta Azure-IoT-Samples-iOS que descargó en los requisitos previos. Después, vaya a la carpeta del proyecto de ejemplo:

```sh
cd quickstart/sample-device
```

Asegúrese de que XCode está cerrado y ejecute el comando siguiente para instalar los pods de CocoaPods que se declararon en el archivo **podfile**:

```sh
pod install
```

Junto con la instalación de los pods necesarios para el proyecto, el comando de instalación también creó un archivo de área de trabajo de XCode que ya está configurado para usar los pods para las dependencias.

### <a name="run-the-sample-device-application"></a>Ejecución de la aplicación de ejemplo del dispositivo

1. Recupere la cadena de conexión del dispositivo. Puede copiarla de [Azure Portal](https://portal.azure.com), de la hoja de detalles del dispositivo, o recuperarla con el siguiente comando de la CLI:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Abra el área de trabajo de ejemplo en XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expanda el proyecto **MQTT Client Sample** (Ejemplo de cliente MQTT) y la carpeta del mismo nombre.  

3. Abra **ViewController.swift** para editar en XCode. 

4. Busque la variable **connectionString** y actualice el valor con la cadena de conexión del dispositivo que copió en el primer paso.

5. Guarde los cambios. 

6. Ejecute el proyecto en el emulador de dispositivos con el botón **Build and run** (Compilar y ejecutar) o la combinación de teclas **comando + r**.

   ![Captura de pantalla en la que se muestra el botón para compilar y ejecutar en el emulador de dispositivos.](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Obtención de la cadena de conexión de IoT Hub

En este artículo, creará un servicio de back-end para enviar mensajes de la nube a un dispositivo a través de la instancia de IOT Hub que creó en [Enviar telemetría desde un dispositivo a IoT Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md). Para enviar mensajes de nube a un dispositivo, el servicio necesita el permiso de **conexión de servicio**. De forma predeterminada, todas las instancias de IoT Hub se crean con una directiva de acceso compartido denominada **servicio** que concede este permiso.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simulación de un dispositivo de servicio

En esta sección se simula un segundo dispositivo de iOS con una aplicación con Swift que envía mensajes de la nube al dispositivo mediante IoT Hub. Esta configuración es útil en escenarios de IoT donde haya un iPhone o iPad que funcione como controlador de otros dispositivos de iOS conectados a una instancia de IoT Hub.

### <a name="install-cocoapods"></a>Instalación de CocoaPods

CocoaPods administra las dependencias de los proyectos de iOS que utilizan bibliotecas de terceros.

Vaya a la carpeta Azure-IoT-Samples-iOS que descargó en los requisitos previos. Después, vaya a la carpeta del proyecto de ejemplo:

```sh
cd quickstart/sample-service
```

Asegúrese de que XCode está cerrado y ejecute el comando siguiente para instalar los pods de CocoaPods que se declararon en el archivo **podfile**:

```sh
pod install
```

Junto con la instalación de los pods necesarios para el proyecto, el comando de instalación también creó un archivo de área de trabajo de XCode que ya está configurado para usar los pods para las dependencias.

### <a name="run-the-sample-service-application"></a>Ejecución de la aplicación de servicio de ejemplo

1. Abra el área de trabajo de ejemplo en XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Expanda el proyecto **AzureIoTServiceSample** y la carpeta del mismo nombre.  

3. Abra **ViewController.swift** para editar en XCode. 

4. Busque la variable **connectionString** y actualice el valor con la cadena de conexión del servicio que copió anteriormente en [Obtener la cadena de conexión de IoT Hub](#get-the-iot-hub-connection-string).

5. Guarde los cambios.

6. En Xcode, cambie las opciones del emulador a un dispositivo iOS diferente del que usó para ejecutar el dispositivo IoT. XCode no puede ejecutar varios emuladores del mismo tipo.

   ![Cambio del dispositivo emulador](media/iot-hub-ios-swift-c2d/change-device.png)

7. Ejecute el proyecto en el emulador de dispositivos con el botón **Build and run** (Compilar y ejecutar) o la combinación de teclas **comando + r**.

   ![Captura de pantalla en la que se muestra el botón para compilar y ejecutar.](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Envío de mensajes de nube a dispositivo

Ahora está listo para usar las dos aplicaciones para enviar y recibir mensajes de la nube al dispositivo.

1. En la aplicación **iOS App Sample** que se ejecuta en el dispositivo IoT simulado, haga clic en **Iniciar**. La aplicación comienza a enviar mensajes del dispositivo a la nube, pero también inicia la escucha de mensajes de la nube al dispositivo.

   ![Visualización de la aplicación de dispositivo IoT de ejemplo](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. En la aplicación **IoTHub Service Client Sample** que se ejecuta en el dispositivo de servicio simulado, escriba el identificador del dispositivo IoT al que quiera enviar un mensaje. 

3. Escriba un mensaje de texto simple y haga clic en **Enviar**.

    En cuanto haga clic en Enviar se producirán varias acciones. El ejemplo de servicio enviará el mensaje a la instancia de IoT Hub, a la cual tiene acceso la aplicación gracias a la cadena de conexión de servicio que ha proporcionado. La instancia de IoT Hub comprobará el identificador de dispositivo, y enviará el mensaje al dispositivo de destino y una notificación de recepción al dispositivo de origen. La aplicación que se ejecuta en el dispositivo IoT simulado comprobará si hay mensajes de la instancia de IoT Hub e imprimirá el texto del más reciente en la pantalla.

    La salida debe tener un aspecto similar al ejemplo siguiente:

   ![Visualización de mensajes de la nube al dispositivo](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo.

Para ver ejemplos de soluciones completas que usen IoT Hub, consulte la documentación sobre los [aceleradores de soluciones de IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía para desarrolladores de IoT Hub](iot-hub-devguide.md).