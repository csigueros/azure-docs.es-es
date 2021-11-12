---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3e07cbe5b8b90ff236b9cdc0d12f31af601a253a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842721"
---
## <a name="register-your-device"></a>Registrar el dispositivo

Puede usar **Azure Portal**, **Visual Studio Code** o la **CLI de Azure** para registrar el dispositivo, según su preferencia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

En su centro de IoT en Azure Portal, los dispositivos IoT Edge se crean y administran por separado de los dispositivos IOT que no están habilitados para Edge.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a IoT Hub.

1. En el panel izquierdo, seleccione **IoT Edge** en el menú y, a continuación, seleccione **Agregar un dispositivo IoT Edge**.

   ![Agrear un dispositivo IoT Edge desde Azure Portal](./media/iot-edge-register-device-x509/portal-add-iot-edge-device.png)

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
   az iot hub device-identity create --device-id device_id_here --hub-name hub_name_here --edge-enabled --auth-method x509_thumbprint --primary-thumbprint primary_SHA_thumbprint_here --secondary-thumbprint secdonary_SHA_thumbprint_here
   ```

Este comando incluye varios parámetros:

* `--device-id` o `-d`: proporcione un nombre descriptivo que sea único de su centro de IoT. Anote este id. de dispositivo porque lo usará en la sección siguiente.
* `hub-name` o `-n`: proporcione el nombre de su centro de IoT.
* `--edge-enabled` o `--ee`: declare que se trata de un dispositivo IoT Edge.
* `--auth-method` o `--am`: declare el tipo de autorización que usará el dispositivo. En este caso, vamos a usar huellas digitales de certificado X.509.
* `--primary-thumbprint` o `--ptp`: proporcione una huella digital de certificado X.509 que se usará como clave principal.
* `--secondary-thumbprint` o `--stp`: proporcione una huella digital de certificado X.509 que se usará como clave secundaria.

---

Ahora que tiene un dispositivo registrado en IoT Hub, recupere la información que usa para completar la instalación y el aprovisionamiento del entorno de ejecución de Azure IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Visualización de dispositivos registrados y recuperación de la información de aprovisionamiento

Los dispositivos que usan la autenticación con certificados X.509 necesitan el nombre de su centro de IoT, su nombre de dispositivo y sus archivos de certificado para completar la instalación y el aprovisionamiento del entorno de ejecución de Azure IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Todos los dispositivos habilitados para Edge que se conectan al centro de IoT se enumeran en la página **IoT Edge**.

![Uso de Azure Portal para visualizar todos los dispositivos IoT Edge del centro de IoT](./media/iot-edge-register-device-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aunque no se admite el registro de dispositivos con certificados X.509 mediante Visual Studio Code, puede ver los dispositivos IoT Edge si es necesario.

Todos los dispositivos que se conectan a IoT Hub se enumeran en la sección **Azure IoT Hub** del explorador de Visual Studio Code. Los dispositivos de IoT Edge se distinguen de los dispositivos que no son Edge con un icono diferente y por el hecho de que los módulos **$edgeAgent** y **$edgeHub** se implementan en cada dispositivo IoT Edge.

![Uso de VS Code para visualizar todos los dispositivos IoT Edge del centro de IoT](./media/iot-edge-register-device-x509/view-devices.png)

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) para ver todos los dispositivos en su centro de IoT. Por ejemplo:

   ```azurecli
   az iot hub device-identity list --hub-name hub_name_here
   ```

Los dispositivos que están registrados como dispositivos IoT Edge tendrán la propiedad **capabilities.iotEdge** establecida en **true**.

---
