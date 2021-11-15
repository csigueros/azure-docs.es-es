---
title: 'Creación y aprovisionamiento de dispositivos IoT Edge de Windows mediante certificados X.509: Azure IoT Edge | Microsoft Docs'
description: Creación y aprovisionamiento de un dispositivo IoT Edge único de Windows en IoT Hub mediante el aprovisionamiento manual con certificados X.509
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: v-tcassi
monikerRange: iotedge-2018-06
ms.openlocfilehash: b9b8a1e3c17ce441dd7469547da83791815dc330
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269899"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-x509-certificates"></a>Creación y aprovisionamiento de un dispositivo IoT Edge de Windows mediante certificados X.509

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones completas para registrar y aprovisionar un dispositivo IoT Edge de Windows.

>[!NOTE]
>Azure IoT Edge con contenedores Windows no se admitirá a partir de la versión 1.2 de Azure IoT Edge.
>
>Considere la posibilidad de usar el nuevo método para ejecutar IoT Edge en dispositivos Windows, [Azure IoT Edge para Linux en Windows](iot-edge-for-linux-on-windows.md).

Todos los dispositivos que se conectan a IoT Hub tienen un identificador de dispositivo que se usa para realizar un seguimiento de las comunicaciones de la nube al dispositivo o del dispositivo a la nube. Configure un dispositivo con su información de conexión, que incluye el nombre de host del centro de IoT, el identificador de dispositivo y la información que el dispositivo usa para autenticarse en IoT Hub.

Los pasos de este artículo le guían a través de un proceso denominado aprovisionamiento manual, donde conecta un único dispositivo a su centro de IoT. Para el aprovisionamiento manual, tiene dos opciones para autenticar dispositivos IoT Edge:

* **Claves simétricas**: cuando se crea una identidad de dispositivo en IoT Hub, el servicio crea dos claves. Debe colocar una de las claves en el dispositivo y este presenta la clave a IoT Hub al autenticarse.

  Este método de autenticación es más rápido para comenzar, pero no es tan seguro.

* **X.509 autofirmado**: cree dos certificados de identidad X.509 y colóquelos en el dispositivo. Cuando se crea una identidad de dispositivo nueva en IoT Hub, se proporcionan huellas digitales desde ambos certificados. Cuando el dispositivo se autentica en IoT Hub, presenta un certificado, y IoT Hub comprueba que el certificado coincide con su huella digital.

  Este método de autenticación es más seguro y se recomienda para los escenarios de producción.

En este artículo se describe el uso de certificados X.509 como método de autenticación. Si quiere usar claves simétricas, consulte [Creación y aprovisionamiento de un dispositivo IoT Edge en Windows mediante claves simétricas](how-to-provision-single-device-windows-symmetric.md).

> [!NOTE]
> Si tiene muchos dispositivos para configurar y no quiere aprovisionar manualmente cada uno, siga uno de los artículos a continuación para obtener información sobre cómo funciona IoT Edge con IoT Hub Device Provisioning Service:
>
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante certificados X.509](how-to-provision-devices-at-scale-windows-x509.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM](how-to-auto-provision-simulated-device-windows.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante claves simétricas](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se explica cómo registrar un dispositivo IoT Edge e instalar IoT Edge en él. Estas tareas tienen requisitos previos y utilidades diferentes que se usan para realizarlas. Asegúrese de que cumple todos los requisitos previos antes de continuar.

### <a name="device-registration"></a>Registro de dispositivos

Puede usar **Azure Portal**, **Visual Studio Code** o la **CLI de Azure** para obtener los pasos de registro del dispositivo. Cada utilidad tiene sus propios requisitos previos:

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

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Un dispositivo Windows

IoT Edge para contenedores de Windows requiere la versión 1809/compilación 17763 de Windows, que es la [compilación de compatibilidad con Windows a largo plazo](/windows/release-information/) más reciente. Asegúrese de examinar la [lista de sistemas compatibles](support.md#operating-systems) para obtener una lista de las SKU admitidas.

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

   ![Agrear un dispositivo IoT Edge desde Azure Portal](./media/how-to-provision-single-device-windows-x509/portal-add-iot-edge-device.png)

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
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA_thumbprint] --secondary-thumbprint [SHA_thumbprint]
   ```

Este comando incluye varios parámetros:

* `--device-id` o `-d`: proporcione un nombre descriptivo que sea único de su centro de IoT. Anote este id. de dispositivo porque lo usará en la sección siguiente.
* `hub-name` o `-n`: proporcione el nombre de su centro de IoT.
* `--edge-enabled` o `--ee`: declare que se trata de un dispositivo IoT Edge.
* `--auth-method` o `--am`: declare el tipo de autorización que usará el dispositivo. En este caso, vamos a usar huellas digitales de certificado X.509.
* `--primary-thumbprint` o `--ptp`: proporcione una huella digital de certificado X.509 que se usará como clave principal.
* `--secondary-thumbprint` o `--stp`: proporcione una huella digital de certificado X.509 que se usará como clave secundaria.

---

Ahora que tiene un dispositivo registrado en IoT Hub, recupere la información que usa para completar la instalación y el aprovisionamiento del entorno de ejecución de Azure IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Visualización de dispositivos registrados y recuperación de la información de aprovisionamiento

Los dispositivos que usan la autenticación con certificados X.509 necesitan el nombre de su centro de IoT, su nombre de dispositivo y sus archivos de certificado para completar la instalación y el aprovisionamiento del entorno de ejecución de Azure IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Todos los dispositivos habilitados para Edge que se conectan al centro de IoT se enumeran en la página **IoT Edge**.

![Uso de Azure Portal para visualizar todos los dispositivos IoT Edge del centro de IoT](./media/how-to-provision-single-device-windows-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aunque no se admite el registro de dispositivos con certificados X.509 mediante Visual Studio Code, puede ver los dispositivos IoT Edge si es necesario.

Todos los dispositivos que se conectan a IoT Hub se enumeran en la sección **Azure IoT Hub** del explorador de Visual Studio Code. Los dispositivos de IoT Edge se distinguen de los dispositivos que no son Edge con un icono diferente y por el hecho de que los módulos **$edgeAgent** y **$edgeHub** se implementan en cada dispositivo IoT Edge.

![Uso de VS Code para visualizar todos los dispositivos IoT Edge del centro de IoT](./media/how-to-provision-single-device-windows-x509/view-devices.png)

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) para ver todos los dispositivos en su centro de IoT. Por ejemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

Los dispositivos que están registrados como dispositivos IoT Edge tendrán la propiedad **capabilities.iotEdge** establecida en **true**.

---

## <a name="install-iot-edge"></a>Instalación de IoT Edge

En esta sección, preparará el dispositivo Windows para IoT Edge. A continuación, instalará IoT Edge.

Azure IoT Edge utiliza un runtime de contenedor compatible con OCI. [Moby](https://github.com/moby/moby), un motor basado en Moby, se incluye en el script de instalación, lo que significa que no hay ningún paso adicional para instalar el motor.

Para instalar el entorno de ejecución de IoT Edge:

1. Ejecute PowerShell como administrador.

   Use una sesión de AMD64 de PowerShell, no PowerShell (x86). Si no está seguro del tipo de sesión que usa, ejecute el comando siguiente:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Ejecute el comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge), que realiza las tareas siguientes:

   * Comprueba que la máquina con Windows se encuentra en una versión compatible.
   * Activa la característica de los contenedores.
   * Descarga el motor de Moby y el entorno de ejecución de Azure IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Si se le solicita, reinicie el dispositivo.

Al instalar IoT Edge en un dispositivo, puede usar parámetros adicionales para modificar el proceso, entre los que se incluyen los siguientes:

* Dirigir el tráfico para que pase por un servidor proxy
* Haga que el instalador apunte a un directorio local, en el caso de una instalación sin conexión.

Para más información sobre estos parámetros adicionales, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Ahora que el motor del contenedor y el entorno de ejecución de IoT Edge están instalados en el dispositivo, está preparado para el paso siguiente, que consiste en configurar el dispositivo con su información de identidad de nube y autenticación.

1. En el dispositivo IoT Edge, ejecute PowerShell como administrador.

2. Use el comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar el entorno de ejecución de Azure IoT Edge en el equipo.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Si descargó el script IoTEdgeSecurityDaemon.ps1 en el dispositivo para la instalación sin conexión o de una versión específica, asegúrese de hacer referencia a la copia local del script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Cuando se le solicite, proporcione la siguiente información:

   * **IotHubHostName**: nombre de host del centro de IoT al que se conectará el dispositivo. Por ejemplo, `{IoT_hub_name}.azure-devices.net`.
   * **DeviceId**: el id. que proporcionó al registrar el dispositivo.
   * **X509IdentityCertificate**: ruta de acceso absoluta a un certificado de identidad en el dispositivo. Por ejemplo, `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: ruta de acceso absoluta al archivo de clave privada para el certificado de identidad proporcionado. Por ejemplo, `C:\path\identity_key.pem`.

Al aprovisionar un dispositivo manualmente, puede usar parámetros adicionales para modificar el proceso, incluyendo:

* Dirigir el tráfico para que pase por un servidor proxy
* Declarar una imagen de contenedor de edgeAgent específica y proporcionar las credenciales si se encuentra en un registro privado

Para más información sobre estos parámetros adicionales, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

---

## <a name="offline-or-specific-version-installation-optional"></a>Instalación sin conexión o de una versión específica (opcional)

Los pasos de esta sección corresponden a escenarios que no se tratan en los pasos de instalación estándar. Estos pueden incluir lo siguiente:

* Instalación de IoT Edge sin conexión
* Instalación de una versión candidata para lanzamiento
* Instalación de una versión que no sea la más reciente

Durante la instalación, se descargan tres archivos:

* Un script de PowerShell, que contiene las instrucciones de instalación.
* Un archivo .cab de Microsoft Azure IoT Edge, que contiene el demonio de seguridad de IoT Edge (iotedged), el motor del contenedor Moby y la CLI de Moby.
* El instalador del paquete redistribuible de Visual C++ (VC Runtime).

Si el dispositivo va a estar sin conexión durante la instalación o si desea instalar una versión específica de IoT Edge, puede descargar anticipadamente estos archivos en el dispositivo. En el momento de la instalación, haga que el script de instalación apunte hacia el directorio que contiene los archivos descargados. El instalador comprueba en primer lugar ese directorio y luego descarga solo los componentes que no se encuentren en él. Si los archivos están disponibles sin conexión, la instalación se puede realizar sin conexión a Internet.

1. Para obtener los archivos de instalación de IoT Edge más recientes, junto con las versiones anteriores, vea las [versiones de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Busque la versión que desea instalar y descargue los siguientes archivos de la sección **Recursos** de las notas de la versión en el dispositivo IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab del canal de versión 1.1.

   Es importante usar el script de PowerShell de la misma versión que el archivo .cab que se usa, porque la funcionalidad cambia para admitir las características de cada versión.

3. Si el archivo .cab que descargó tiene un sufijo de arquitectura, cambie el nombre del archivo a **Microsoft-Azure-IoTEdge.cab**.

4. Opcionalmente, descargue un instalador para Visual C++ Redistributable. Por ejemplo, el script de PowerShell usa esta versión: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Guarde el instalador en la misma carpeta del dispositivo IoT que los archivos IoT Edge.

5. Para instalar con componentes sin conexión, [use el operador punto](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) en la copia local del script de PowerShell.

6. Ejecute el comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) con el parámetro `-OfflineInstallationPath`. Especifique la ruta de acceso absoluta al directorio de archivos. Por ejemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   El comando de implementación usará cualquier componente que se encuentre en el directorio de archivos local proporcionado. Si falta el archivo .cab o el instalador de Visual C++, intentará descargarlos.

## <a name="uninstall-iot-edge"></a>Desinstalación de IoT Edge

Si quiere quitar la instalación de IoT Edge de su dispositivo, use los siguientes comandos.

Si quiere quitar la instalación de IoT Edge del dispositivo Windows, use el comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) en una ventana administrativa de PowerShell. Este comando quita el entorno de ejecución de IoT Edge, junto con la configuración existente y los datos de motor de Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Para más información acerca de las opciones de desinstalación, use el comando `Get-Help Uninstall-IoTEdge -full`.

## <a name="next-steps"></a>Pasos siguientes

Siga [implementando módulos de IoT Edge](how-to-deploy-modules-portal.md) para información sobre cómo implementar módulos en el dispositivo.
