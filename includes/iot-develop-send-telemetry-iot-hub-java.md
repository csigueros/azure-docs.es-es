---
title: Archivo de inclusión
description: Archivo de inclusión
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 11/02/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 093a75b77be2c24de11e97fe7d1627623fb1e2f9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861371"
---
[![Examinar el código](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

En este artículo de inicio rápido, se explica un flujo de trabajo básico de desarrollo de aplicaciones de Azure IoT. Va a utilizar la CLI de Azure e IoT Explorer para crear un centro de Azure IoT y un dispositivo. A continuación, utilizará un ejemplo de un SDK de dispositivo IoT de Azure para ejecutar un controlador de temperatura, conectarlo de forma segura al centro y enviar datos de telemetría. La aplicación de ejemplo del controlador de temperatura se ejecuta en la máquina local y genera datos del sensor simulados para enviarlos a IoT Hub.

## <a name="prerequisites"></a>Prerrequisitos

Este inicio rápido se ejecuta en Windows, Linux y Raspberry Pi. Se ha probado en las siguientes versiones de sistema operativo y dispositivo:

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi SO versión 10 (Raspian) que se ejecuta en un Raspberry Pi 3 modelo B+

Instale los siguientes requisitos previos en la máquina de desarrollo, excepto donde se indique para Raspberry Pi:

- Si no tiene una suscripción de Azure, [cree una gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de comenzar.
- [Git](https://git-scm.com/downloads).
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): utilidad multiplataforma basada en GUI para supervisar y administrar Azure IoT. Si usa Raspberry Pi como plataforma de desarrollo, se recomienda instalar IoT Explorer en otro equipo. Si no desea instalar IoT Explorer, puede usar la CLI de Azure para realizar los mismos pasos. 
- CLI de Azure. Tiene dos opciones para ejecutar comandos de la CLI de Azure en este inicio rápido:
    - Use Azure Cloud Shell, un shell interactivo que ejecuta comandos de la CLI en el explorador. Esta opción se recomienda porque no es necesario instalar nada. Si usa Cloud Shell por primera vez, inicie sesión en [Azure Portal](https://portal.azure.com). Siga los pasos descritos en el [inicio rápido de Cloud Shell](../articles/cloud-shell/quickstart.md) para **iniciar Cloud Shell** y **seleccionar el entorno Bash**.
    - Opcionalmente, ejecute la CLI de Azure en su equipo local. Si la CLI de Azure ya está instalada, ejecute `az upgrade` para actualizar la CLI y las extensiones a la versión actual. Para instalar la CLI de Azure, consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli). Si usa Raspberry Pi como plataforma de desarrollo, se recomienda usar Azure Cloud Shell o instalar la CLI de Azure en otro equipo.

Instale los requisitos previos restantes para su sistema operativo.

### <a name="windows"></a>Windows

Para completar este inicio rápido en Windows, instale el software siguiente:

- Java SE Development Kit 8 o posterior. Puede descargar el JDK de Java 8 (LTS) para varias plataformas desde [Descargar compilaciones de Zulu de OpenJDK](https://www.azul.com/downloads/zulu-community/). En el instalador, seleccione la opción **Agregar a PATH**.

- [Apache Maven 3](https://maven.apache.org/download.cgi). Después de extraer la descarga en una carpeta local, agregue la ruta de acceso completa de la carpeta */bin* de Maven a la variable de entorno `PATH` de Windows.

### <a name="linux-or-raspberry-pi-os"></a>Linux o Raspberry Pi OS

Para realizar este inicio rápido en Linux o Raspberry Pi OS, instale el software siguiente:

> [!NOTE]
> Los pasos de esta sección se basan en distribuciones Ubuntu/Debian de Linux. (El sistema operativo Raspberry Pi se basa en Debian). Si usa una distribución de Linux diferente, deberá modificar los pasos como corresponda.

- OpenJDK (Open Java Development Kit) 8 o posterior. Puede usar el comando `java -version` para comprobar la versión de Java instalada en el sistema. Asegúrese de que JDK esté instalado, no solo Java Runtime Environment (JRE).

    1. Para instalar OpenJDK en el sistema, especifique los siguientes comandos:

        Para instalar la versión predeterminada de OpenJDK para el sistema (OpenJDK 11 para Ubuntu 20.04 y Raspberry Pi OS 10 en el momento de escribir este inicio rápido):

        ```bash
        sudo apt update
        sudo apt install default-jdk
        ```

        Como alternativa, puede especificar la versión del JDK que se va a instalar. Por ejemplo:

        ```bash
        sudo apt update
        sudo apt install openjdk-8-jdk
        ```

    1. Si el sistema tiene instaladas varias versiones de Java, puede usar los siguientes comandos para configurar las versiones predeterminadas (automáticas) de Java y el compilador de Java.

        ```bash
        update-java-alternatives --list          #list the Java versions installed
        sudo update-alternatives --config java   #set the default Java version
        sudo  update-alternatives --config javac #set the default Java compiler version
        ```

    1. Establezca la variable de entorno `JAVA_HOME` en la ruta de acceso de la instalación de JDK. (Por lo general, se trata de un subdirectorio con versiones en el directorio **/usr/lib/jvm**).

        ```bash
        export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")
        ```

        > [!IMPORTANT]
        > Este comando establece la variable `JAVA_HOME` en el entorno de shell actual. Se recomienda agregar el comando al archivo `~/.bashrc` o `/etc/profile` para que esté disponible cada vez que abra un nuevo shell.

    1. Compruebe la versión del JDK de Java (y de JRE) instalada, que la versión del compilador de Java coincide con la versión del JDK y que la variable de entorno `JAVA_HOME` está establecida correctamente.

        ```bash
        java -version
        javac -version
        echo $JAVA_HOME
        ```

- Apache Maven 3. Puede usar el comando `mvn --version` para comprobar la versión de Maven instalada en el sistema.

    1. Para instalar Maven, escriba los siguientes comandos:

        ```bash
        sudo apt-get update
        sudo apt-get install maven
        ```

    1. Ejecute el siguiente comando para comprobar la instalación.

        ```bash
        mvn --version
        ```

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-the-device-sample"></a>Ejecución del ejemplo de dispositivo

En esta sección, usará el SDK de Java para enviar mensajes desde un dispositivo hasta IoT Hub. Ejecutará un ejemplo que implementa un controlador de temperatura con dos sensores de termostato.

1. Abra una consola para instalar el SDK para dispositivos Java de Azure IoT y compile y ejecute el ejemplo de código. Usará esta consola en los pasos siguientes.

    > [!NOTE]
    > Si usa una instalación local de la CLI de Azure, es posible que ahora tenga dos ventanas de consola abiertas. Asegúrese de escribir los comandos de esta sección en la consola que acaba de abrir, no en la que ha estado usando para la CLI.

    **Linux and Raspberry Pi OS**

    Confirme que la variable de entorno JAVA_HOME (`echo $JAVA_HOME`) está establecida. Para más información sobre cómo establecer JAVA_HOME, consulte [Requisitos previos de Linux y Raspberry Pi](#linux-or-raspberry-pi-os).

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

1. Establezca las siguientes variables de entorno para que el dispositivo se pueda conectar a Azure IoT.

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

1. Vaya al directorio de ejemplo.

    **CMD**

    ```console
    cd device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample
    ```

    **Bash**

    ```bash
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```

1. Ejecute el ejemplo de código.

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

1. Ejecute el comando [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) para supervisar los eventos enviados desde el dispositivo al centro de IoT. Use los nombres que creó anteriormente en Azure IoT para el dispositivo IoT Hub.

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
    

