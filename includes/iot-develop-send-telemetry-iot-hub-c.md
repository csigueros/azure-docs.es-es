---
title: Archivo de inclusión
description: Archivo de inclusión
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/10/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 91c4236c69eaae3e534f06ed0b4ae2f3fb8c7747
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094085"
---
[![Examinar el código](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

En este artículo de inicio rápido, se explica un flujo de trabajo básico de desarrollo de aplicaciones de Azure IoT. Vamos a utilizar la CLI de Azure e IoT Explorer para crear un centro de Azure IoT y un dispositivo. A continuación, utilizaremos un ejemplo de un SDK de dispositivo IoT de Azure para ejecutar un controlador de temperatura simulado, conectarlo de forma segura al centro y enviar datos de telemetría.

## <a name="prerequisites"></a>Prerrequisitos
Este inicio rápido se ejecuta en Windows, Linux y Raspberry Pi. Se ha probado en las siguientes versiones de sistema operativo y dispositivo:

- Windows 10
- Ubuntu 20.04 LTS que se ejecuta en Subsistema de Windows para Linux (WSL)
- Raspberry Pi SO versión 10 (Raspian) que se ejecuta en un Raspberry Pi 3 modelo B+

Instale los siguientes requisitos previos en la máquina de desarrollo, excepto donde se indique para Raspberry Pi:

- Si no tiene una suscripción de Azure, [cree una gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de comenzar.
- [Git](https://git-scm.com/downloads).
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): utilidad multiplataforma basada en GUI para supervisar y administrar Azure IoT. Si usa Raspberry Pi como plataforma de desarrollo, se recomienda instalar IoT Explorer en otro equipo. Si no desea instalar IoT Explorer, puede usar la CLI de Azure para realizar los mismos pasos. 
- CLI de Azure. Tiene dos opciones para ejecutar comandos de la CLI de Azure en este inicio rápido:
    - Use Azure Cloud Shell, un shell interactivo que ejecuta comandos de la CLI en el explorador. Esta opción se recomienda porque no es necesario instalar nada. Si usa Cloud Shell por primera vez, inicie sesión en [Azure Portal](https://portal.azure.com). Siga los pasos descritos en el [inicio rápido de Cloud Shell](../articles/cloud-shell/quickstart.md) para **iniciar Cloud Shell** y **seleccionar el entorno Bash**.
    - Opcionalmente, ejecute la CLI de Azure en su equipo local. Si la CLI de Azure ya está instalada, ejecute `az upgrade` para actualizar la CLI y las extensiones a la versión actual. Para instalar la CLI de Azure, consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli). Si usa Raspberry Pi como plataforma de desarrollo, se recomienda usar Azure Cloud Shell o instalar la CLI de Azure en otro equipo.

Instale los requisitos previos restantes para su sistema operativo.

### <a name="linux-or-raspberry-pi-os"></a>Linux o Raspberry Pi OS
Para realizar este inicio rápido en Linux o Raspberry Pi OS, instale el software siguiente:

Instale **GCC**, **Git**, **cmake** y las dependencias necesarias con el comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Compruebe que la versión de `cmake` es posterior a **2.8.12** y que la versión de **GCC** es posterior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows
Para completar este inicio rápido en Windows, instale Visual Studio 2019 y agregue los componentes necesarios para el desarrollo en C y C++.

1. Para los nuevos usuarios, instale [Visual Studio (Community, Professional o Enterprise) 2019](https://visualstudio.microsoft.com/downloads/). Descargue la edición que desea instalar e inicie el instalador.
    > [!NOTE]
    > Para los usuarios de Visual Studio 2019 existentes, seleccione **Inicio** de Windows, escriba *Instalador de Visual Studio* e inicie el instalador.
1. En la pestaña **Cargas de trabajo** del instalador, seleccione la carga de trabajo **Desarrollo para el escritorio con C++** .
1. Ejecución de la instalación.

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>Ejecución de un dispositivo simulado
En esta sección, usará el SDK de C para enviar mensajes desde un dispositivo simulado al centro de IoT. Ejecutará un ejemplo que implementa un controlador de temperatura con dos sensores de termostato.

### <a name="build-the-sample"></a>Compilación del ejemplo
1. Abra una nueva consola para instalar el SDK de dispositivo Azure IoT para C y ejecute el ejemplo de código. En Windows, seleccione **Inicio**, escriba *Símbolo del sistema para desarrolladores para VS 2019* y abra la consola. En Linux y Raspberry Pi OS, abra un terminal de comandos de Bash.

    > [!NOTE]
    > Si usa una instalación local de la CLI de Azure, es posible que ahora tenga dos ventanas de consola abiertas. Asegúrese de escribir los comandos de esta sección en la consola que acaba de abrir, en lugar de la que ha estado usando para la CLI.

1. Cambie a la carpeta local en la que quiere clonar el repositorio de ejemplo.

1. Clone el SDK del dispositivo de Azure IoT para C en la máquina local:
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-c.git
    ```
1. Navegue a la carpeta raíz del SDK y ejecute el siguiente comando para actualizar las dependencias:

    ```console
    cd azure-iot-sdk-c
    git submodule update --init
    ```
    Esta operación tarda unos minutos.

1. Ejecute los siguientes comandos para compilar el SDK y los ejemplos:

    ```console
    cmake -Bcmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF
    cmake --build cmake
    ```
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

### <a name="run-the-code"></a>Ejecución del código
1. Ejecute el código de ejemplo con el comando adecuado para la consola.

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/pnp_temperature_controller
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

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-c/iot-explorer-device-telemetry.png" alt-text="Captura de pantalla de la telemetría del dispositivo en IoT Explorer":::

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
      component: ''
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: mydevice
      payload: '{"workingSet":1251}'
    
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: mydevice
      payload: '{"temperature":22.00}'
    ```

