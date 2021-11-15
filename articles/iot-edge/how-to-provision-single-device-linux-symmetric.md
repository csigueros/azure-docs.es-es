---
title: 'Creación y aprovisionamiento de un dispositivo IoT Edge en Linux con claves simétricas: Azure IoT Edge | Microsoft Docs'
description: Creación y aprovisionamiento de un dispositivo IoT Edge único en IoT Hub para el aprovisionamiento manual con claves simétricas
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: kgremban
ms.openlocfilehash: adb252ec5c9168b1184b841d91c8813c9c64507f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851957"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-symmetric-keys"></a>Creación y aprovisionamiento de un dispositivo IoT Edge en Linux con claves simétricas

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporcionan instrucciones completas para registrar y aprovisionar un dispositivo IoT Edge en Linux, incluida la instalación de IoT Edge.

Todos los dispositivos que se conectan a IoT Hub tienen un identificador de dispositivo que se usa para realizar un seguimiento de las comunicaciones de la nube al dispositivo o del dispositivo a la nube. Configure un dispositivo con su información de conexión, que incluye el nombre de host del centro de IoT, el identificador de dispositivo y la información que el dispositivo usa para autenticarse en IoT Hub.

Los pasos de este artículo le guían a través de un proceso denominado aprovisionamiento manual, donde conecta un único dispositivo a su centro de IoT. Para el aprovisionamiento manual, tiene dos opciones para autenticar dispositivos IoT Edge:

* **Claves simétricas**: cuando se crea una identidad de dispositivo en IoT Hub, el servicio crea dos claves. Debe colocar una de las claves en el dispositivo y este presenta la clave a IoT Hub al autenticarse.

  Este método de autenticación es más rápido para comenzar, pero no es tan seguro.

* **X.509 autofirmado**: cree dos certificados de identidad X.509 y colóquelos en el dispositivo. Cuando se crea una identidad de dispositivo nueva en IoT Hub, se proporcionan huellas digitales desde ambos certificados. Cuando el dispositivo se autentica en IoT Hub, presenta un certificado, y IoT Hub comprueba que el certificado coincide con su huella digital.

  Este método de autenticación es más seguro y se recomienda para los escenarios de producción.

En este artículo se describe el uso de claves simétricas como método de autenticación. Si desea usar certificados X.509, consulte [Creación y aprovisionamiento de un dispositivo IoT Edge en Linux mediante certificados X.509](how-to-provision-single-device-linux-x509.md).

> [!NOTE]
> Si tiene muchos dispositivos para configurar y no quiere aprovisionar manualmente cada uno, siga uno de los artículos a continuación para obtener información sobre cómo funciona IoT Edge con IoT Hub Device Provisioning Service:
>
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante certificados X.509](how-to-provision-devices-at-scale-linux-x509.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM](how-to-provision-devices-at-scale-linux-tpm.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante claves simétricas](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se explica cómo registrar un dispositivo IoT Edge e instalar IoT Edge en él. Estas tareas tienen requisitos previos y utilidades diferentes que se usan para realizarlas. Asegúrese de que cumple todos los requisitos previos antes de continuar.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Dispositivo Linux X64, ARM32 o ARM64.

Microsoft ofrece paquetes de instalación para los sistemas operativos Ubuntu Server 18.04 y Raspberry Pi OS Stretch.

para la información más reciente sobre qué sistemas operativos se admiten actualmente en los escenarios de producción, consulte [Sistemas compatibles con Azure IoT Edge](support.md#operating-systems).

>[!NOTE]
>La compatibilidad con dispositivos ARM64 está en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Ahora que el motor del contenedor y el entorno de ejecución de IoT Edge están instalados en el dispositivo, está preparado para el paso siguiente, que consiste en configurar el dispositivo con su información de identidad de nube y autenticación.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Busque las configuraciones de aprovisionamiento del archivo y quite la marca de comentario de la sección **Manual provisioning configuration using a connection string**, si aún no se ha hecho.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "ADD_DEVICE_CONNECTION_STRING_HERE"
   ```

Actualice el valor de **device_connection_string** con la cadena de conexión desde un dispositivo IoT Edge. Asegúrese de que las demás secciones de aprovisionamiento estén comentadas. Asegúrese de que la línea **provisioning:** no tiene ningún espacio en blanco delante y de que los elementos anidados muestran una sangría de dos espacios.

Para pegar el contenido del portapapeles en Nano: `Shift+Right Click` o presione `Shift+Insert`.

Guarde y cierre el archivo.

   `CTRL + X`, `Y`, `Enter`

Después de especificar la información de aprovisionamiento en el archivo de configuración, reinicie el demonio:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Puede configurar rápidamente el dispositivo IoT Edge con autenticación de clave simétrica mediante el siguiente comando:

   ```bash
   sudo iotedge config mp --connection-string 'PASTE_DEVICE_CONNECTION_STRING_HERE'
   ```

El comando `iotedge config mp` crea un archivo de configuración en el dispositivo, proporciona la cadena de conexión y aplica los cambios de configuración.

Si desea ver el archivo de configuración, puede abrirlo:

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Si realiza algún cambio en el archivo de configuración, use el comando `iotedge config apply` para aplicar los cambios:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Comprobación de configuración correcta

Compruebe que el runtime se ha instalado y configurado correctamente en el dispositivo de IoT Edge.

>[!TIP]
>Necesita privilegios elevados para ejecutar comandos `iotedge`. Cuando cierre la sesión en su máquina y la inicie de nuevo por primera vez después de instalar el entorno de ejecución de IoT Edge, sus permisos se actualizarán automáticamente. Hasta entonces, use `sudo` delante de los comandos.

Compruebe que el servicio del sistema IoT Edge esté en ejecución.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Una respuesta de estado correcta es `Ok`.

::: moniker-end

Si necesita solucionar problemas del servicio, recupere los registros del servicio.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Use la herramienta `check` para comprobar el estado de configuración y conexión del dispositivo.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Use siempre `sudo` para ejecutar la herramienta de comprobación, incluso después de que se actualicen los permisos. Para comprobar el estado de la configuración, la herramienta necesita privilegios elevados para acceder al archivo de configuración.

Vea todos los módulos que se ejecutan en el dispositivo IoT Edge. Cuando el servicio se inicia por primera vez, solo verá la ejecución del módulo **edgeAgent**. El módulo edgeAgent se ejecuta de forma predeterminada y le ayuda a instalar e iniciar todos los módulos adicionales que implemente en el dispositivo.

   ```bash
   sudo iotedge list
   ```

Al crear un nuevo dispositivo IoT Edge, se mostrará el código de estado `417 -- The device's deployment configuration is not set` en Azure Portal. Este estado es normal y significa que el dispositivo está listo para recibir una implementación de módulo.

## <a name="offline-or-specific-version-installation-optional"></a>Instalación sin conexión o de una versión específica (opcional)

Los pasos de esta sección corresponden a escenarios que no se tratan en los pasos de instalación estándar. Estos pueden incluir lo siguiente:

* Instalación de IoT Edge sin conexión
* Instalación de una versión candidata para lanzamiento

Siga los pasos de esta sección si quiere instalar una versión específica del entorno de ejecución de Azure IoT Edge que no esté disponible mediante `apt-get install`. La lista de paquetes de Microsoft solo contiene un conjunto limitado de versiones recientes y sus subversiones, por lo que estos pasos son para cualquiera que quiera instalar una versión anterior o una versión candidata para lanzamiento.

Con los comandos curl, puede dirigirse a los archivos de componentes directamente desde el repositorio de GitHub de IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Vaya a [Versiones de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) y busque la versión de lanzamiento que desea tener como destino.

2. Expanda la sección **Recursos** para esa versión.

3. Cada versión debe tener archivos nuevos para el demonio de seguridad de IoT Edge y hsmlib. Si va a instalar IoT Edge en un dispositivo sin conexión, descargue estos archivos con anterioridad. De lo contrario, use los comandos siguientes para actualizar esos componentes.

   1. Busque el archivo **libiothsm-std** que coincida con la arquitectura del dispositivo IoT Edge. Haga clic con el botón derecho en el vínculo del archivo y copie la dirección del vínculo.

   2. Use el vínculo copiado en el comando siguiente para instalar esa versión de hsmlib:

      ```bash
      curl -L libiothsm-std_link_here -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. Busque el archivo **iotedge** que coincida con la arquitectura del dispositivo IoT Edge. Haga clic con el botón derecho en el vínculo del archivo y copie la dirección del vínculo.

   4. Use el vínculo copiado en el comando siguiente para instalar esa versión del demonio de seguridad de IoT Edge.

      ```bash
      curl -L iotedge_link_here -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Si el dispositivo ejecuta actualmente IoT Edge versión 1.1 o anterior, desinstale los paquetes **iotedge** y **libiothsm-std** antes de seguir los pasos de esta sección. Para obtener más información, consulte [Actualización de 1.0 o 1.1 a 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Vaya a [Versiones de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) y busque la versión de lanzamiento que desea tener como destino.

2. Expanda la sección **Recursos** para esa versión.

3. Cada versión debe tener archivos nuevos para IoT Edge y el servicio de identidad. Si va a instalar IoT Edge en un dispositivo sin conexión, descargue estos archivos con anterioridad. De lo contrario, use los comandos siguientes para actualizar esos componentes.

   1. Busque el archivo **aziot-identity-service** que coincida con la arquitectura del dispositivo IoT Edge. Haga clic con el botón derecho en el vínculo del archivo y copie la dirección del vínculo.

   2. Use el vínculo copiado en el comando siguiente para instalar esa versión del servicio de identidad:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. Busque el archivo **aziot-edge** que coincida con la arquitectura del dispositivo IoT Edge. Haga clic con el botón derecho en el vínculo del archivo y copie la dirección del vínculo.

   4. Use el vínculo copiado en el comando siguiente para instalar esa versión de IoT Edge.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Ahora que el motor del contenedor y el entorno de ejecución de IoT Edge están instalados en el dispositivo, está preparado para el paso siguiente, que consiste en [aprovisionar el dispositivo con su identidad de nube](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Desinstalación de IoT Edge

Si quiere quitar la instalación de IoT Edge de su dispositivo, use los siguientes comandos.

Quite el entorno de ejecución de Azure IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Use la marca `--purge` si desea eliminar todos los archivos asociados a IoT Edge, incluidos los archivos de configuración. Excluya esta marca si desea reinstalar IoT Edge y usar la misma información de configuración en el futuro.

Al quitar el entorno de ejecución de IoT Edge, los contenedores que se hayan creado en él se detendrán, pero seguirán existiendo en el dispositivo. Observe todos los contenedores para ver cuáles permanecen.

```bash
sudo docker ps -a
```

Elimine los contenedores del dispositivo, incluidos los dos contenedores del entorno en tiempo de ejecución.

```bash
sudo docker rm -f <container name>
```

Por último, quite el entorno en tiempo de ejecución del contenedor del dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Pasos siguientes

Siga [implementando módulos de IoT Edge](how-to-deploy-modules-portal.md) para información sobre cómo implementar módulos en el dispositivo.
