---
title: 'Creación y aprovisionamiento de dispositivos IoT Edge en Linux mediante certificados X.509: Azure IoT Edge | Microsoft Docs'
description: Creación y aprovisionamiento de un dispositivo IoT Edge en IoT Hub para el aprovisionamiento manual con certificados X.509
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: v-tcassi
ms.openlocfilehash: 5116774d0ea68f4d78dd0721a3546d9d82cbfdbd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269858"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-x509-certificates"></a>Creación y aprovisionamiento de un dispositivo IoT Edge en Linux mediante certificados X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporcionan instrucciones completas para registrar y aprovisionar un dispositivo IoT Edge en Linux, incluida la instalación de IoT Edge.

Todos los dispositivos que se conectan a IoT Hub tienen un identificador de dispositivo que se usa para realizar un seguimiento de las comunicaciones de la nube al dispositivo o del dispositivo a la nube. Configure un dispositivo con su información de conexión, que incluye el nombre de host del centro de IoT, el identificador de dispositivo y la información que el dispositivo usa para autenticarse en IoT Hub.

Los pasos de este artículo le guían a través de un proceso denominado aprovisionamiento manual, donde conecta un único dispositivo a su centro de IoT. Para el aprovisionamiento manual, tiene dos opciones para autenticar dispositivos IoT Edge:

* **Claves simétricas**: cuando se crea una identidad de dispositivo en IoT Hub, el servicio crea dos claves. Debe colocar una de las claves en el dispositivo y este presenta la clave a IoT Hub al autenticarse.

  Este método de autenticación es más rápido para comenzar, pero no es tan seguro.

* **X.509 autofirmado**: cree dos certificados de identidad X.509 y colóquelos en el dispositivo. Cuando se crea una identidad de dispositivo nueva en IoT Hub, se proporcionan huellas digitales desde ambos certificados. Cuando el dispositivo se autentica en IoT Hub, presenta un certificado, y IoT Hub comprueba que el certificado coincide con su huella digital.

  Este método de autenticación es más seguro y el más recomendado para los escenarios de producción.

En este artículo se describe el uso de certificados X.509 como método de autenticación. Si quiere usar claves simétricas, consulte [Creación y aprovisionamiento de un dispositivo IoT Edge en Linux mediante claves simétricas](how-to-provision-single-device-linux-symmetric.md).

> [!NOTE]
> Si tiene muchos dispositivos para configurar y no quiere aprovisionar manualmente cada uno, siga uno de los artículos a continuación para obtener información sobre cómo funciona IoT Edge con IoT Hub Device Provisioning Service:
>
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante certificados X.509](how-to-provision-devices-at-scale-linux-x509.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM](how-to-auto-provision-simulated-device-linux.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante claves simétricas](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se explica cómo registrar un dispositivo IoT Edge e instalar IoT Edge en él. Estas tareas tienen requisitos previos y utilidades diferentes que se usan para realizarlas. Asegúrese de que cumple todos los requisitos previos antes de continuar.

### <a name="device-registration"></a>Registro de dispositivos

Puede usar **Azure Portal**, **Visual Studio Code** o la **CLI de Azure** para registrar el dispositivo. Cada utilidad tiene sus propios requisitos previos:

# <a name="portal"></a>[Portal](#tab/azure-portal)

Un [centro de IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o estándar en la suscripción de Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Un [centro de IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o estándar en la suscripción de Azure
* Tener [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

> [!NOTE]
> Actualmente, la extensión de Azure IoT para Visual Studio Code no admite el registro de dispositivos con certificados X.509.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

* Un [centro de IoT](../iot-hub/iot-hub-create-using-cli.md) gratuito o estándar en la suscripción de Azure.
* La [CLI de Azure](/cli/azure/install-azure-cli) en su entorno. Como mínimo, la versión de la CLI de Azure debe ser la 2.0.70 o posterior. Use `az --version` para asegurarse. Esta versión admite comandos az extension e introduce la plataforma de comandos de Knack.

---

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Dispositivo Linux X64, ARM32 o ARM64.

Microsoft ofrece paquetes de instalación para los sistemas operativos Ubuntu Server 18.04 y Raspberry Pi OS Stretch.

Para obtener la información más reciente sobre qué sistemas operativos se admiten actualmente en los escenarios de producción, consulte [Sistemas compatibles con Azure IoT Edge](support.md#operating-systems).

>[!NOTE]
>La compatibilidad con dispositivos ARM64 está en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="generate-device-identity-certificates"></a>Generación de los certificados de identidad del dispositivo

El aprovisionamiento manual con certificados X.509 requiere IoT Edge, versión 1.0.10 o posterior.

Al aprovisionar un dispositivo IoT Edge con certificados X.509, se usa lo que se denomina un *certificado de identidad del dispositivo*. Este certificado solo se usa para aprovisionar un dispositivo IoT Edge y autenticarlo en Azure IoT Hub. Es un certificado de hoja que no firma otros certificados. El certificado de identidad del dispositivo es independiente de los certificados de la entidad de certificación (CA) que el dispositivo IoT Edge presenta a los módulos o dispositivos de nivel inferior para la verificación.

En el caso de la autenticación mediante certificados X.509, la información de autenticación de cada dispositivo se proporciona en forma de *huellas digitales* tomadas de los certificados de identidad del dispositivo. Estas huellas digitales se proporcionan a IoT Hub en el momento de registrar el dispositivo para que el servicio pueda reconocer el dispositivo al conectarse.

Para obtener más información sobre cómo se usan los certificados de CA en los dispositivos IoT Edge, consulte [Información sobre los certificados de Azure IoT Edge](iot-edge-certs.md).

Necesita los siguientes archivos para el aprovisionamiento manual con X.509:

* Dos certificados de identidad del dispositivo con sus certificados de clave privada coincidentes en los formatos .cer o .pem.

  Se proporciona un conjunto de archivos de certificado y clave al entorno de ejecución de IoT Edge. Al crear los certificados de identidad del dispositivo, establezca el nombre común del certificado (CN) en el id. de dispositivo que quiere que tenga el dispositivo en el centro de IoT.

* Huellas digitales tomadas de ambos certificados de identidad del dispositivo.

  Los valores de huella digital son 40 caracteres hexadecimales para hashes de tipo SHA-1 o 64 caracteres hexadecimales para hashes de tipo SHA-256. Ambas huellas digitales se proporcionan a IoT Hub en el momento del registro del dispositivo.

Si no tiene certificados disponibles, puede proceder a la [Creación de certificados de demostración para probar las características de dispositivo IoT Edge](how-to-create-test-certificates.md). Siga las instrucciones de ese artículo para configurar scripts de creación de certificados, crear un certificado de CA raíz y luego crear dos certificados de identidad del dispositivo IoT Edge.

Una manera de recuperar la huella digital de un certificado es con el siguiente comando openssl:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-your-device"></a>Registrar el dispositivo

Puede usar **Azure Portal**, **Visual Studio Code** o la **CLI de Azure** para registrar el dispositivo, según su preferencia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

En su centro de IoT en Azure Portal, los dispositivos IoT Edge se crean y administran por separado de los dispositivos IOT que no están habilitados para Edge.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a IoT Hub.

1. En el panel izquierdo, seleccione **IoT Edge** en el menú y, a continuación, seleccione **Agregar un dispositivo IoT Edge**.

   ![Agrear un dispositivo IoT Edge desde Azure Portal](./media/how-to-provision-single-device-linux-x509/portal-add-iot-edge-device.png)

1. En la página **Crear un dispositivo**, proporcione la información siguiente:

   * Cree un id. de dispositivo descriptivo. Anote este identificador de dispositivo, ya que lo usará más adelante.
   * Seleccione **X.509 autofirmado** como el tipo de autenticación.
   * Proporcione las huellas digitales de certificado de identidad principal y secundaria. Los valores de huella digital son 40 caracteres hexadecimales para hashes de tipo SHA-1 o 64 caracteres hexadecimales para hashes de tipo SHA-256.

1. Seleccione **Guardar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Actualmente, la extensión de Azure IoT para Visual Studio Code no admite el registro de dispositivos con certificados X.509.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) para crear una nueva identidad de dispositivo en su centro de IoT. Por ejemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Este comando incluye varios parámetros:

* `--device-id` o `-d`: proporcione un nombre descriptivo que sea único de su centro de IoT. Anote este id. de dispositivo porque lo usará en la sección siguiente.
* `hub-name` o `-n`: proporcione el nombre de su centro de IoT.
* `--edge-enabled` o `--ee`: declare que se trata de un dispositivo IoT Edge.
* `--auth-method` o `--am`: declare el tipo de autorización que usará el dispositivo. En este caso, vamos a usar huellas digitales de certificado X.509.
* `--primary-thumbprint` o `--ptp`: proporcione una huella digital de certificado X.509 que se usará como clave principal.
* `--secondary-thumbprint` o `--stp`: proporcione una huella digital de certificado X.509 que se usará como clave secundaria.

---

Ahora que tiene un dispositivo registrado en IoT Hub, recupere la información que usa para completar la instalación y el aprovisionamiento del entorno de ejecución de IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Visualización de dispositivos registrados y recuperación de la información de aprovisionamiento

Los dispositivos que usan la autenticación con certificados X.509 necesitan el nombre de su centro de IoT, su nombre de dispositivo y sus archivos de certificado para completar la instalación y el aprovisionamiento del entorno de ejecución de IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Todos los dispositivos habilitados para Edge que se conectan al centro de IoT se enumeran en la página **IoT Edge**.

![Uso de Azure Portal para visualizar todos los dispositivos IoT Edge del centro de IoT](./media/how-to-provision-single-device-linux-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aunque no se admite el registro de dispositivos con certificados X.509 mediante Visual Studio Code, puede ver los dispositivos IoT Edge si es necesario.

Todos los dispositivos que se conectan a IoT Hub se enumeran en la sección **Azure IoT Hub** del explorador de Visual Studio Code. Los dispositivos de IoT Edge se distinguen de los dispositivos que no son Edge con un icono diferente y por el hecho de que los módulos **$edgeAgent** y **$edgeHub** se implementan en cada dispositivo IoT Edge.

![Uso de VS Code para visualizar todos los dispositivos IoT Edge del centro de IoT](./media/how-to-provision-single-device-linux-x509/view-devices.png)

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) para ver todos los dispositivos en su centro de IoT. Por ejemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Los dispositivos que están registrados como dispositivos IoT Edge tendrán la propiedad **capabilities.iotEdge** establecida en **true**.

---

## <a name="install-iot-edge"></a>Instalación de IoT Edge

En esta sección, preparará su máquina virtual Linux o dispositivo físico para IoT Edge. A continuación, instalará IoT Edge.

Hay dos pasos que debe completar en el dispositivo antes de que esté listo para instalar el entorno de ejecución de IoT Edge. El dispositivo necesita acceso a los paquetes de instalación de Microsoft y requiere la instalación de un motor de contenedor.

### <a name="prepare-your-device-to-access-the-microsoft-installation-packages"></a>Prepare el dispositivo para acceder a los paquetes de instalación de Microsoft.

1. Instale la configuración del repositorio que coincida con el sistema operativo del dispositivo.

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. Copie la lista generada en el directorio sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Instale la clave pública de GPG de Microsoft.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Los paquetes de software de Azure IoT Edge están sujetos a los términos de licencia que se encuentran cada paquete (`usr/share/doc/{package-name}` o el directorio `LICENSE`). Lea los términos de licencia antes de usar el paquete. La instalación y el uso de un paquete constituyen la aceptación de estos términos. Si no acepta los términos de licencia, no utilice el paquete.

### <a name="install-a-container-engine-on-your-device"></a>Instalación de un motor de contenedor en el dispositivo

Azure IoT Edge utiliza un runtime de contenedor compatible con OCI. En los escenarios de producción, se recomienda utilizar el motor de Moby. El motor Moby es el único motor de contenedor compatible oficialmente con Azure IoT Edge. Las imágenes de contenedor de Docker CE/EE son totalmente compatibles con el entorno de ejecución de Moby.

Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale el motor de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Si recibe algún error al instalar el motor del contenedor Moby, compruebe la compatibilidad con Moby del kernel de Linux. Algunos fabricantes de dispositivos incrustados distribuyen imágenes de dispositivos que contienen kernels de Linux personalizados sin las características necesarias para la compatibilidad del motor del contenedor. Ejecute el siguiente comando, que usa el [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) suministrado por Moby, para comprobar la configuración del kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

En la salida del script, compruebe que todos los elementos que figuran en `Generally Necessary` y `Network Drivers` estén habilitados. Si faltan características, puede habilitarlas volviendo a generar el kernel a partir del origen y seleccionando los módulos asociados para incluirlos en el archivo .config del kernel adecuado. Igualmente, si usa un generador de configuración de kernel como `defconfig` o `menuconfig`, busque y habilite las características correspondientes y vuelva a generar el kernel como corresponda. Una vez que haya implementado el kernel recién modificado, vuelva a ejecutar el script check-config para comprobar que se han habilitado correctamente todas las características necesarias.

### <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

El demonio de seguridad de IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El demonio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de [Instalación sin conexión o de una versión específica](#offline-or-specific-version-installation-optional), más adelante en este artículo.

Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

Instale IoT Edge versión 1.1.* junto con el paquete **libiothsm-std**:

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge versión 1.1 es la rama de soporte técnico a largo plazo de IoT Edge. Si ejecuta una versión anterior, se recomienda instalar o actualizar a la revisión más reciente, ya que ya no se admiten las versiones anteriores.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

El servicio IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El servicio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

El servicio de identidad de IoT se introdujo junto con la versión 1.2 de IoT Edge. Este servicio controla el aprovisionamiento y la administración de identidades para IoT Edge y otros componentes de dispositivo que necesitan comunicarse con IoT Hub.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si tiene que instalar una versión específica, como una versión preliminar, o tiene que realizar la instalación mientras está sin conexión, siga los pasos de [Instalación sin conexión o de una versión específica](#offline-or-specific-version-installation-optional), más adelante en este artículo.

>[!NOTE]
>Los pasos de esta sección le muestran cómo instalar la versión 1.2 de IoT Edge.
>
>Si ya tiene un dispositivo IoT Edge que ejecuta una versión anterior y desea actualizar a 1.2, siga los pasos descritos en [Actualización del demonio de seguridad de IoT Edge y el entorno de ejecución](how-to-update-iot-edge.md). La versión 1.2 es suficientemente diferente de las versiones anteriores de IoT Edge para que sean necesarios pasos específicos para la actualización.

Actualice las listas de paquetes en el dispositivo.

   ```bash
   sudo apt-get update
   ```

Compruebe qué versiones de IoT Edge y el servicio de identidad de IoT están disponibles.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

Para instalar la versión más reciente de IoT Edge y el paquete de servicio de identidad de IoT, use el comando siguiente:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- end 1.2 -->
::: moniker-end

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
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
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
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
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
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. Busque el archivo **iotedge** que coincida con la arquitectura del dispositivo IoT Edge. Haga clic con el botón derecho en el vínculo del archivo y copie la dirección del vínculo.

   4. Use el vínculo copiado en el comando siguiente para instalar esa versión del demonio de seguridad de IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
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
