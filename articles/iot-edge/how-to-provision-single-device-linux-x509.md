---
title: 'Creación y aprovisionamiento de dispositivos IoT Edge en Linux mediante certificados X.509: Azure IoT Edge | Microsoft Docs'
description: Creación y aprovisionamiento de un dispositivo IoT Edge en IoT Hub para el aprovisionamiento manual con certificados X.509
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: kgremban
ms.openlocfilehash: 1f1d6a627baf4166a34680961e1601307292bb3b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845106"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-x509-certificates"></a>Creación y aprovisionamiento de un dispositivo IoT Edge en Linux mediante certificados X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporcionan instrucciones completas para registrar y aprovisionar un dispositivo IoT Edge en Linux, incluida la instalación de IoT Edge.

Todos los dispositivos que se conectan a IoT Hub tienen un identificador de dispositivo que se usa para realizar un seguimiento de las comunicaciones de la nube al dispositivo o del dispositivo a la nube. Configure un dispositivo con su información de conexión, que incluye el nombre de host del centro de IoT, el identificador de dispositivo y la información que el dispositivo usa para autenticarse en IoT Hub.

Los pasos de este artículo le guían a través de un proceso denominado aprovisionamiento manual, donde conecta un único dispositivo a su centro de IoT. Para el aprovisionamiento manual, tiene dos opciones para autenticar dispositivos IoT Edge:

* **Claves simétricas**: cuando se crea una identidad de dispositivo en IoT Hub, el servicio crea dos claves. Debe colocar una de las claves en el dispositivo y este presenta la clave a IoT Hub al autenticarse.

  Este método de autenticación es más rápido para comenzar, pero no es tan seguro.

* **X.509 autofirmado**: cree dos certificados de identidad X.509 y colóquelos en el dispositivo. Cuando se crea una identidad de dispositivo nueva en IoT Hub, se proporcionan huellas digitales desde ambos certificados. Cuando el dispositivo se autentica en IoT Hub, presenta un certificado, y IoT Hub comprueba que el certificado coincide con su huella digital.

  Este método de autenticación es más seguro y se recomienda para los escenarios de producción.

En este artículo se describe el uso de certificados X.509 como método de autenticación. Si quiere usar claves simétricas, consulte [Creación y aprovisionamiento de un dispositivo IoT Edge en Linux mediante claves simétricas](how-to-provision-single-device-linux-symmetric.md).

> [!NOTE]
> Si tiene muchos dispositivos para configurar y no quiere aprovisionar manualmente cada uno, siga uno de los artículos a continuación para obtener información sobre cómo funciona IoT Edge con IoT Hub Device Provisioning Service:
>
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante certificados X.509](how-to-provision-devices-at-scale-linux-x509.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM](how-to-provision-devices-at-scale-linux-tpm.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante claves simétricas](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se explica cómo registrar un dispositivo IoT Edge e instalar IoT Edge en él. Estas tareas tienen requisitos previos y utilidades diferentes que se usan para realizarlas. Asegúrese de que cumple todos los requisitos previos antes de continuar.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Dispositivo Linux X64, ARM32 o ARM64.

Microsoft ofrece paquetes de instalación para los sistemas operativos Ubuntu Server 18.04 y Raspberry Pi OS Stretch.

para la información más reciente sobre qué sistemas operativos se admiten actualmente en los escenarios de producción, consulte [Sistemas compatibles con Azure IoT Edge](support.md#operating-systems).

>[!NOTE]
>La compatibilidad con dispositivos ARM64 está en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!-- Generate device identity certificates H2 and content -->
[!INCLUDE [iot-edge-generate-device-identity-certs.md](../../includes/iot-edge-generate-device-identity-certs.md)]

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-x509.md](../../includes/iot-edge-register-device-x509.md)]

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

Busque la sección de configuraciones de aprovisionamiento del archivo y quite la marca de comentario de la sección **Manual provisioning configuration using an X.509 identity certificate** (Configuración de aprovisionamiento manual mediante un certificado de identidad X.509). Asegúrese de que las demás secciones de aprovisionamiento estén comentadas. Asegúrese de que la línea **provisioning:** no tiene ningún espacio en blanco delante y de que los elementos anidados muestran una sangría de dos espacios.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "REQUIRED_IOTHUB_HOSTNAME"
       device_id: "REQUIRED_DEVICE_ID_PROVISIONED_IN_IOTHUB"
       identity_cert: "REQUIRED_URI_TO_DEVICE_IDENTITY_CERTIFICATE"
       identity_pk: "REQUIRED_URI_TO_DEVICE_IDENTITY_PRIVATE_KEY"
   ```

Actualice los campos siguientes:

* **iothub_hostname**: nombre de host del centro de IoT al que se conectará el dispositivo. Por ejemplo, `{IoT hub name}.azure-devices.net`.
* **device_id**: el id. que proporcionó al registrar el dispositivo.
* **identity_cert**: URI a un certificado de identidad en el dispositivo. Por ejemplo, `file:///path/identity_certificate.pem`.
* **identity_pk**: URI al archivo de clave privada para el certificado de identidad proporcionado. Por ejemplo, `file:///path/identity_key.pem`.

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

Cree el archivo de configuración para el dispositivo basándose en un archivo de plantilla que se proporciona como parte de la instalación de IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Busque la sección **Provisioning** del archivo y quite las marcas de comentario de las líneas para el aprovisionamiento manual con el certificado de identidad X.509. Asegúrese de que las demás secciones de aprovisionamiento estén comentadas.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "REQUIRED_IOTHUB_HOSTNAME"
   device_id = "REQUIRED_DEVICE_ID_PROVISIONED_IN_IOTHUB"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "REQUIRED_URI_OR_POINTER_TO_DEVICE_IDENTITY_CERTIFICATE"

   identity_pk = "REQUIRED_URI_TO_DEVICE_IDENTITY_PRIVATE_KEY"
   ```

Actualice los campos siguientes:

* **iothub_hostname**: nombre de host del centro de IoT al que se conectará el dispositivo. Por ejemplo, `{IoT hub name}.azure-devices.net`.
* **device_id**: el id. que proporcionó al registrar el dispositivo.
* **identity_cert**: URI para un certificado de identidad en el dispositivo, por ejemplo: `file:///path/identity_certificate.pem`. O bien, emita el certificado de forma dinámica mediante EST o una entidad de certificación local.
* **identity_pk**: URI para el archivo de clave privada del certificado de identidad proporcionado, por ejemplo: `file:///path/identity_key.pem`. O bien, proporcione un URI de PKCS#11 y, a continuación, proporcione la información de configuración en la sección **PKCS#11** más adelante en el archivo de configuración.

Guarde y cierre el archivo.

   `CTRL + X`, `Y`, `Enter`

Después de escribir la información de aprovisionamiento en el archivo de configuración, aplique sus cambios:

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
