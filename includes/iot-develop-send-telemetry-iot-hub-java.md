---
title: Archivo de inclusión
description: Archivo de inclusión
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 6c295a5148f4821e3bd395a95c5b98e298da5077
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610280"
---
[![Examinar el código](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample)

En este artículo de inicio rápido, se explica un flujo de trabajo básico de desarrollo de aplicaciones de Azure IoT. Vamos a utilizar la CLI de Azure y el explorador de IoT para crear un centro de Azure IoT y un dispositivo. A continuación, utilizaremos un ejemplo de un SDK de dispositivo IoT de Azure para ejecutar un controlador de temperatura simulado, conectarlo de forma segura al centro y enviar datos de telemetría.

## <a name="prerequisites"></a>Prerrequisitos
- Si no tiene una suscripción de Azure, [cree una gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de comenzar.
- [Git](https://git-scm.com/downloads).
- Una máquina de desarrollo con Java SE Development Kit 8 o posterior. Puede descargar el JDK de Java 8 (LTS) para varias plataformas desde [Descargar compilaciones de Zulu de OpenJDK](https://www.azul.com/downloads/zulu-community/). En el instalador, seleccione la opción **Agregar a PATH**.
- [Apache Maven 3](https://maven.apache.org/download.cgi). Después de extraer la descarga en una carpeta local, agregue la ruta de acceso completa de la carpeta */bin* de Maven a la variable PATH de Windows.
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): utilidad multiplataforma basado en GUI para supervisar y administrar Azure IoT. 
- CLI de Azure. Tiene dos opciones para ejecutar comandos de la CLI de Azure en este inicio rápido:
    - Use Azure Cloud Shell, un shell interactivo que ejecuta comandos de la CLI en el explorador. Esta opción se recomienda porque no es necesario instalar nada. Si usa Cloud Shell por primera vez, inicie sesión en [Azure Portal](https://portal.azure.com). Siga los pasos descritos en el [inicio rápido de Cloud Shell](../articles/cloud-shell/quickstart.md) para **iniciar Cloud Shell** y **seleccionar el entorno Bash**.
    - Opcionalmente, ejecute la CLI de Azure en su equipo local. Si la CLI de Azure ya está instalada, ejecute `az upgrade` para actualizar la CLI y las extensiones a la versión actual. Para instalar la CLI de Azure, consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>Ejecución de un dispositivo simulado
En esta sección, usará el SDK de Java para enviar mensajes desde un dispositivo simulado al centro de IoT. Ejecutará un ejemplo que implementa un controlador de temperatura con dos sensores de termostato.

### <a name="configure-your-environment"></a>Configurar su entorno
1. Abra una consola para instalar el SDK para dispositivos Java de Azure IoT y compile y ejecute el ejemplo de código. Usará esta consola en los pasos siguientes.

    > [!NOTE]
    > Si usa una instalación local de la CLI de Azure, es posible que ahora tenga dos ventanas de consola abiertas. Asegúrese de escribir los comandos de esta sección en la consola que acaba de abrir, no en la que ha estado usando para la CLI.

1. Establezca las siguientes variables de entorno para que el dispositivo simulado se pueda conectar a Azure IoT.
    * Establezca una variable de entorno llamada `IOTHUB_DEVICE_CONNECTION_STRING`. Como valor de la variable, use la cadena de conexión del dispositivo que ha guardado en la sección anterior.
    * Establezca una variable de entorno llamada `IOTHUB_DEVICE_SECURITY_TYPE`. Para la variable, use el valor de cadena literal `connectionString`.

    **CMD**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > En el caso del símbolo de comandos de Windows, no hay comillas alrededor de los valores de cadena de cada variable.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

### <a name="build-the-sample"></a>Compilación del ejemplo
1. Clone el SDK para dispositivos Java de Azure IoT en su máquina local:
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```
1. Vaya a la carpeta raíz del SDK y ejecute el siguiente comando para compilar el SDK y actualizar los ejemplos.
    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```
    Esta operación tarda varios minutos.

### <a name="run-the-code"></a>Ejecución del código
1. Vaya al directorio de ejemplos.
    ```console
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```
1. Ejecute el siguiente código de ejemplo.

    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```
    > [!NOTE]
    > En este ejemplo de código se usa Azure IoT Plug and Play, que le permite integrar dispositivos inteligentes en sus soluciones sin ninguna configuración manual.  De forma predeterminada, la mayoría de los ejemplos de esta documentación usan IoT Plug and Play. Para más información sobre las ventajas de IoT Plug and Play y los casos para su uso o no, consulte [¿Qué es IoT Plug and Play?](../articles/iot-develop/overview-iot-plug-and-play.md)

El ejemplo se conecta de forma segura al centro de IoT como el dispositivo que registró y comienza a enviar mensajes de telemetría. La salida de ejemplo se muestra en la consola.

## <a name="view-telemetry"></a>Ver datos de telemetría

Puede ver la telemetría del dispositivo con el explorador de IoT. También puede verla mediante la CLI de Azure.

Para ver la telemetría en Azure IoT Explorer:

1. En el centro de IoT del explorador de IoT, seleccione **View devices in this hub** (Ver dispositivos en este centro) y seleccione el dispositivo en la lista. 
1. En el menú de la izquierda del dispositivo, seleccione **Telemetría**.
1. Confirme que la opción **Use built-in event hub** (Usar centro de eventos integrado) esté establecida en *Sí* y, a continuación, seleccione **Iniciar**.
1. Vea la telemetría a medida que el dispositivo envía mensajes a la nube.

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-java/iot-explorer-device-telemetry.png" alt-text="Captura de pantalla de la telemetría del dispositivo en IoT Explorer":::

1. Seleccione **Detener** para dejar de recibir eventos.

Para leer la telemetría que envían los componentes individuales del dispositivo, puede utilizar las características de Plug and Play de IoT Explorer. Por ejemplo, el controlador de temperatura de este inicio rápido tiene dos termostatos: thermostat1 y thermostat2. Para ver la temperatura que notifica thermostat1: 

1. En el explorador de IoT del dispositivo, seleccione **IoT Plug and Play components** (Componentes de IoT Plug and Play) en el menú de la izquierda. A continuación, seleccione **thermostat1** en la lista de componentes.

1. En el panel del componente **thermostat1**, seleccione **Telemetría** en el menú superior.

1. En el panel **Telemetría**, siga los mismos pasos que realizó anteriormente. Asegúrese de que la opción **Use built-in event hub** (Usar centro de eventos integrado) esté establecida en *Sí* y, a continuación, seleccione **Iniciar**.

Para ver la telemetría del dispositivo con la CLI de Azure:

1. Ejecute el comando [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) para supervisar los eventos enviados desde el dispositivo simulado al centro de IoT. Use los nombres que creó anteriormente en Azure IoT para el dispositivo IoT Hub.

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```
1. Vea los detalles de la conexión y la salida de telemetría en la consola.

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 24.1
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 33.3
    ```
    

