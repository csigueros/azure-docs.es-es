---
title: Archivo de inclusión
description: Archivo de inclusión
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 11/02/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 6e3a7b7b37e02f6f351caf985fd997d7daafd70c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860861"
---
[![Examinar el código](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

En este artículo de inicio rápido, se explica un flujo de trabajo básico de desarrollo de aplicaciones de Azure IoT. En primer lugar, cree una aplicación de Azure IoT Central para hospedar dispositivos. A continuación, usará un ejemplo del SDK de dispositivo IoT de Azure para crear un controlador de temperatura, conectarlo de forma segura a IoT Central y enviar datos de telemetría. La aplicación de ejemplo del controlador de temperatura se ejecuta en la máquina local y genera datos del sensor simulados para enviarlos a IoT Central.

## <a name="prerequisites"></a>Prerrequisitos

Este inicio rápido se ejecuta en Windows, Linux y Raspberry Pi. Se ha probado en las siguientes versiones de sistema operativo y dispositivo:

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi OS, versión 10 (buster), que se ejecuta en un dispositivo Raspberry Pi 3 Model B+

Instale los siguientes requisitos previos en la máquina de desarrollo:

- [Git](https://git-scm.com/downloads).

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

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>Ejecución del ejemplo de dispositivo

En esta sección se configura el entorno local, se instala el SDK de dispositivo IoT de Azure para Java y se ejecuta un ejemplo que crea un controlador de temperatura.

### <a name="configure-your-environment"></a>Configurar su entorno

1. Abra una consola como CMD de Windows o Bash.

    **Linux and Raspberry Pi OS**

    Confirme que la variable de entorno JAVA_HOME (`echo $JAVA_HOME`) está establecida. Esta variable de entorno debe establecerse para compilar correctamente el SDK y los ejemplos. Para más información sobre cómo establecer JAVA_HOME, consulte [Requisitos previos de Linux y Raspberry Pi](#linux-or-raspberry-pi-os).

1. Establezca las siguientes variables de entorno con los comandos adecuados para la consola. El dispositivo usa estos valores para conectarse a IoT Central. Para `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` y `IOTHUB_DEVICE_DPS_DEVICE_ID`, use los valores de conexión del dispositivo que guardó anteriormente.

    **Línea de comandos de Windows**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > En el caso del símbolo de comandos de Windows, no hay comillas alrededor de los valores de variable.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="build-and-run-the-code"></a>Compilación y ejecución del código

1. Clone el SDK para dispositivos Java de Azure IoT en la máquina local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```

1. Vaya a la carpeta raíz del SDK y ejecute el siguiente comando para compilar el SDK y actualizar los ejemplos.

    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```

    Esta operación tarda varios minutos.

1. Vaya al directorio de ejemplo.

    **Windows**

    ```console
    cd device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample
    ```

    **Sistema operativo Linux o Raspberry Pi**

    ```bash
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```

1. Ejecute el siguiente ejemplo de código desde el SDK. El ejemplo crea un controlador de temperatura con sensores de termostato.

    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```

    Una vez que el dispositivo se conecta a la aplicación IoT Central, se conecta a la instancia del dispositivo creado en la aplicación y comienza a enviar telemetría. Después de algunos detalles de aprovisionamiento iniciales, la consola comienza a generar la telemetría del controlador de temperatura.

    ```output
    2021-05-13 15:39:26.411 DEBUG Mqtt:253 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
    2021-05-13 15:39:26.428 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.432 DEBUG TemperatureController:427 - Property: Update - component = "deviceInformation" is COMPLETED.
    2021-05-13 15:39:26.436 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.438 DEBUG TemperatureController:438 - Telemetry: Sent - {"workingSet": 1024.0KiB }
    2021-05-13 15:39:26.439 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.439 DEBUG TemperatureController:446 - Property: Update - {"serialNumber": SR-123456} is COMPLETED
    2021-05-13 15:39:26.447 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.447 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 44.4░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```