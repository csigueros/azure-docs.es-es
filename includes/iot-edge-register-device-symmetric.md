---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 80328e4ec6ea06937a30c94a0e0f854e03eb81da
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851956"
---
## <a name="register-your-device"></a>Registrar el dispositivo

Puede usar **Azure Portal**, **Visual Studio Code** o la **CLI de Azure** para registrar el dispositivo, según su preferencia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

En su centro de IoT en Azure Portal, los dispositivos IoT Edge se crean y administran por separado de los dispositivos IOT que no están habilitados para Edge.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a IoT Hub.

1. En el panel izquierdo, seleccione **IoT Edge** en el menú y, a continuación, seleccione **Agregar un dispositivo IoT Edge**.

   ![Agrear un dispositivo IoT Edge desde Azure Portal](./media/iot-edge-register-device-symmetric/portal-add-iot-edge-device.png)

1. En la página **Crear un dispositivo**, proporcione la información siguiente:

   * Cree un id. de dispositivo descriptivo. Anote este identificador de dispositivo, ya que lo usará más adelante.
   * Seleccione **Clave simétrica** como el tipo de autenticación.
   * Use la configuración predeterminada para la generación automática de claves de autenticación y para conectar el dispositivo nuevo a su centro.

1. Seleccione **Guardar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Iniciar sesión para acceder a IoT Hub

Puede usar las extensiones de Azure IoT para Visual Studio Code para realizar operaciones con IoT Hub. Para que estas operaciones funcionen, debe iniciar sesión en su cuenta de Azure y seleccionar su centro.

1. En Visual Studio Code, abra la vista **Explorer** (Explorador).
1. En la parte inferior de Explorer, expanda la sección **Azure IoT Hub**.

   ![Sección Expansión de los dispositivos de Azure IoT Hub](./media/iot-edge-register-device-symmetric/azure-iot-hub-devices.png)

1. Haga clic en los puntos suspensivos **...** en el encabezado de la sección **Azure IoT Hub**. Si no ve el botón de los puntos suspensivos, mantenga el ratón sobre el encabezado o haga clic en él.
1. Elija **Select IoT Hub** (Seleccionar IoT Hub).
1. Si no ha iniciado sesión en su cuenta de Azure, siga las indicaciones para hacerlo.
1. Seleccione su suscripción a Azure.
1. Seleccione IoT Hub.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registro de un nuevo dispositivo en Visual Studio Code

1. En el explorador de Visual Studio Code, expanda la sección **Azure IoT Hub**.
1. Haga clic en los puntos suspensivos **...** en el encabezado de la sección **Azure IoT Hub**. Si no ve el botón de los puntos suspensivos, mantenga el ratón sobre el encabezado o haga clic en él.
1. Seleccione **Create IoT Edge Device** (Crear un dispositivo de IoT Edge).
1. En el cuadro de texto que aparece, otorgue a su dispositivo un identificador.

En la pantalla de salida, consulte el resultado del comando. Se imprime la información del dispositivo, que incluye el valor de **deviceId** que proporcionó, y el valor de **connectionString** que puede usar para conectar su dispositivo físico a su instancia de IoT Hub.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) para crear una nueva identidad de dispositivo en su centro de IoT. Por ejemplo:

   ```azurecli
   az iot hub device-identity create --device-id device_id_here --hub-name hub_name_here --edge-enabled
   ```

Este comando incluye tres parámetros:

* `--device-id` o `-d`: proporcione un nombre descriptivo que sea único dentro de su centro de IoT.
* `--hub-name` o `-n`: proporcione el nombre de su centro de IoT.
* `--edge-enabled` o `--ee`: Declare que se trata de un dispositivo de IoT Edge.

   ![az iot hub device-identity create output](./media/iot-edge-register-device-symmetric/create-edge-device-cli.png)

---

Ahora que tiene un dispositivo registrado en IoT Hub, recupere la información que usa para completar la instalación y el aprovisionamiento del entorno de ejecución de Azure IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Visualización de dispositivos registrados y recuperación de la información de aprovisionamiento

Los dispositivos que usan la autenticación de claves simétricas necesitan sus cadenas de conexión para completar la instalación y el aprovisionamiento del entorno de ejecución de Azure IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Todos los dispositivos habilitados para Edge que se conectan al centro de IoT se enumeran en la página **IoT Edge**.

![Uso de Azure Portal para visualizar todos los dispositivos IoT Edge del centro de IoT](./media/iot-edge-register-device-symmetric/portal-view-devices.png)

Cuando esté listo para configurar el dispositivo, necesitará la cadena de conexión que vincula el dispositivo físico con su identidad en el centro de IoT.

Los dispositivos que se autentican con claves simétricas tienen sus cadenas de conexión disponibles para copiarse en el portal.

1. En la página **IoT Edge** del portal, haga clic en el identificador de dispositivo en la lista de dispositivos de IoT Edge.
2. Copie el valor de la **cadena de conexión primaria** o la **cadena de conexión secundaria**.

# <a name="visual-studio-code"></a>Tener [Visual Studio Code](#tab/visual-studio-code)

Todos los dispositivos que se conectan a IoT Hub se enumeran en la sección **Azure IoT Hub** del explorador de Visual Studio Code. Los dispositivos de IoT Edge se distinguen de los dispositivos que no son Edge con un icono diferente y por el hecho de que los módulos **$edgeAgent** y **$edgeHub** se implementan en cada dispositivo IoT Edge.

![Uso de VS Code para visualizar todos los dispositivos IoT Edge del centro de IoT](./media/iot-edge-register-device-symmetric/view-devices.png)

Cuando esté listo para configurar el dispositivo, necesitará la cadena de conexión que vincula el dispositivo físico con su identidad en el centro de IoT.

1. Haga clic con el botón derecho en el identificador del dispositivo en la sección **Azure IoT Hub**.
1. Seleccione **Copy Device Connection String** (Copiar la cadena de conexión del dispositivo).

   La cadena de conexión se copiará al portapapeles.

También puede seleccionar **Get Device Info** (Obtener información del dispositivo), si hace clic con el botón derecho del mouse en el menú para ver toda la información del dispositivo, incluyendo la cadena de conexión, en la ventana de salida.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) para ver todos los dispositivos en su centro de IoT. Por ejemplo:

   ```azurecli
   az iot hub device-identity list --hub-name hub_name_here
   ```

Los dispositivos que están registrados como dispositivos IoT Edge tendrán la propiedad **capabilities.iotEdge** establecida en **true**.

Cuando esté listo para configurar el dispositivo, necesitará la cadena de conexión que vincula el dispositivo físico con su identidad en el centro de IoT. Use el comando [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) para devolver la cadena de conexión para un único dispositivo:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device_id] --hub-name [hub_name]
   ```

>[!TIP]
>El comando `connection-string show` se presentó en la versión 0.9.8 de la extensión de Azure IoT, en reemplazo del comando `show-connection-string` en desuso. Si recibe un error al ejecutar este comando, asegúrese de que la versión de la extensión esté actualizada a 0.9.8 o posterior. Para obtener más información y las actualizaciones más recientes, consulte [Extensión de Microsoft Azure IoT para la CLI de Azure](https://github.com/Azure/azure-iot-cli-extension).

El valor del parámetro `device-id` distingue mayúsculas y minúsculas.

Al copiar la cadena de conexión que se va a usar en un dispositivo, no incluya las comillas alrededor de la cadena de conexión.

---
