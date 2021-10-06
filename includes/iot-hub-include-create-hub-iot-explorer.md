---
title: Archivo de inclusión
description: archivo de inclusión
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 92a81cc28b605c715e2522c6ad78e5732a379c8f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610297"
---
## <a name="create-an-iot-hub"></a>Crear un centro de IoT
En esta sección, usará la CLI de Azure para crear un centro de IoT y un grupo de recursos.  Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Un centro de IoT funciona como centro de mensajes unificado para la comunicación bidireccional entre la aplicación de IoT y los dispositivos.

Para crear un centro de IoT y un grupo de recursos:

1. Inicie la CLI de Azure: 
    - Si usa Cloud Shell, seleccione el botón **Probar** en los comandos de la CLI para iniciar Cloud Shell en una ventana dividida del explorador. O bien, puede abrir [Cloud Shell](https://shell.azure.com/bash) en una pestaña del explorador independiente.
    - Si va a usar la CLI de Azure localmente, abra una consola como Windows CMD, PowerShell o Bash, e [inicie sesión en la CLI de Azure](/cli/azure/authenticate-azure-cli).
    
    Para ejecutar los comandos de la CLI en el resto de este inicio rápido, copie la sintaxis del comando, péguela en la ventana de Cloud Shell o en la consola de la CLI, edite los valores de las variables y presione Entrar.

1. Ejecute [az extension add](/cli/azure/extension#az_extension_add) para instalar o actualizar la extensión *azure-iot* a la versión actual.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. Ejecute el comando [az group create](/cli/azure/group#az_group_create) para crear un grupo de recursos. El siguiente comando crea un grupo de recursos denominado *MyResourceGroup* en la ubicación *eastus*. 
    >[!NOTE]
    > Como alternativa, puede establecer otra ubicación. Para ver las ubicaciones disponibles, ejecute `az account list-locations`. En este tutorial se usa *eastus* tal y como se muestra en el comando de ejemplo. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. Ejecute el comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) para crear una instancia de IoT Hub. La creación de una instancia de IoT Hub puede tardar unos minutos. 

    *YourIotHubName*. Reemplace este marcador de posición y las llaves circundantes en el siguiente comando, con el nombre que eligió para el centro de IoT. Un nombre de centro de IoT debe ser único globalmente en Azure. Use el nombre del centro de IoT a lo largo de este inicio rápido cuando vea el marcador de posición.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > Después de crear un centro de IoT, usará Azure IoT Explorer para interactuar con el centro de IoT en el resto de esta guía de inicio rápido. IoT Explorer es una aplicación de GUI que le permite conectarse a un dispositivo IoT Hub existente y agregar, administrar y supervisar dispositivos. Para obtener más información, consulte [Instalación y uso del explorador de Azure IoT](../articles/iot-fundamentals/howto-use-iot-explorer.md). Opcionalmente, puede seguir usando comandos de la CLI.

### <a name="configure-iot-explorer"></a>Configuración de IoT Explorer

En el resto de este inicio rápido, usará IoT Explorer para registrar un dispositivo en su centro de IoT y ver la telemetría del dispositivo. En esta sección, configurará IoT Explorer para conectarse al centro de IoT que acaba de crear y para leer los modelos Plug and Play desde el repositorio de modelos público. 

> [!NOTE]
> También puede usar la CLI de Azure para registrar un dispositivo. Use el comando *[az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) --device-id mydevice --hub-name {YourIoTHubName}* para registrar un nuevo dispositivo y el comando *[az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) --device-id mydevice --hub-name {YourIoTHubName}* para obtener la cadena de conexión principal del dispositivo. Una vez que anote la cadena de conexión del dispositivo, puede ir directamente a [Ejecución de un dispositivo simulado](#run-a-simulated-device).

Para agregar una conexión al centro de IoT:

1. Ejecute el comando [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) para obtener la cadena de conexión para el centro de IoT.

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. Copie la cadena de conexión sin los caracteres de comillas circundantes.
1. En Azure IoT Explorer, seleccione **Centros de IoT** en el menú izquierdo y, a continuación, seleccione **+ Agregar conexión**.
1. Pegue la cadena de conexión en el cuadro **Cadena de conexión**.
1. Seleccione **Guardar**.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-connection.png" alt-text="Captura de pantalla de la adición de una conexión en IoT Explorer":::

1. Si la conexión se realiza correctamente, IoT Explorer cambia a la vista **Dispositivos**.

Para agregar el repositorio de modelos público:

1. En IoT Explorer, seleccione **Inicio** para volver a la vista principal.
1. En el menú de la izquierda, seleccione **Configuración de IoT Plug and Play** y, a continuación, seleccione **+Agregar** y elija **Repositorio público** en el menú desplegable.
1. Aparece una entrada para el repositorio de modelos público en `https://devicemodels.azure.com`.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-public-repository.png" alt-text="Captura de pantalla de la adición del repositorio de modelos público en IoT Explorer":::

1. Seleccione **Guardar**.

### <a name="register-a-device"></a>Registrar un dispositivo

En esta sección, creará una nueva instancia de dispositivo y la registrará en el centro de IoT que creó. Usará la información de conexión del dispositivo recién registrado para conectar de forma segura el dispositivo simulado en una sección posterior.

Para registrar un dispositivo:

1. En la vista principal de IoT Explorer, seleccione **Centros de IoT**.
1. Debería aparecer la conexión que agregó anteriormente. Seleccione **View devices in this hub** (Ver dispositivos en este centro) debajo de las propiedades de conexión.
1. Seleccione **+ Nuevo** y escriba un identificador de dispositivo para el dispositivo. Por ejemplo, *miDispositivo*. Deje todas las demás propiedades tal cual.
1. Seleccione **Crear**.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-device-created.png" alt-text="Captura de pantalla de una identidad de dispositivo de Azure IoT Explorer":::

1. Use los botones de copia para copiar y anotar el campo **Cadena de conexión principal**. Necesitará esta cadena de conexión más adelante.
