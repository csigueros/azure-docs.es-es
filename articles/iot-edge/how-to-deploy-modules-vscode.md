---
title: Implementación de módulos desde Visual Studio Code - Azure IoT Edge
description: Use Visual Studio Code con Azure IoT Tools para enviar un módulo de IoT Edge desde la instancia de IoT Hub al dispositivo IoT Edge, tal como se configura en un manifiesto de implementación.
author: kgremban
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8a1ba312d404a0045e08d2fbc966141471b57eb5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750781"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implementación de módulos de Azure IoT Edge desde Visual Studio Code

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Una vez que ha creado módulos de IoT Edge con su lógica empresarial, querrá implementarlos en sus dispositivos para usarlos en el perímetro. Si tiene varios módulos que funcionan conjuntamente para recopilar y procesar datos, puede implementarlos todos a la vez y declarar las reglas de enrutamiento que los conectan.

En este artículo se muestra cómo crear un manifiesto de implementación de JSON y, luego, usar ese archivo para insertar la implementación en un dispositivo IoT Edge. Para obtener información sobre la creación de una implementación dirigida a varios dispositivos en función de sus etiquetas compartidas, vea [Implementación de módulos de IoT Edge a escala con Visual Studio Code](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) en la suscripción de Azure.
* Un dispositivo IoT Edge.

  Si no tiene un dispositivo IoT Edge configurado, puede crear uno en una máquina virtual de Azure. Siga los pasos de alguno de los artículos de inicio rápido para [Crear un dispositivo virtual Linux](quickstart-linux.md) o [Crear un dispositivo virtual Windows](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Configuración de un manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe qué módulos se van a implementar, cómo fluyen los datos entre los módulos y las propiedades deseadas de los módulos gemelos. Para más información sobre los manifiestos de implementación y cómo crearlos, consulte [Descripción de cómo se pueden utilizar, configurar y reutilizar los módulos de IoT Edge](module-composition.md).

Para implementar módulos con Visual Studio Code, guarde el manifiesto de implementación localmente como un archivo .JSON. Usará la ruta de acceso al archivo en la sección siguiente, al ejecutar el comando para aplicar la configuración al dispositivo.

Este es un manifiesto de implementación básico con un módulo como ejemplo:

>[!NOTE]
>Este manifiesto de implementación de ejemplo usa la versión de esquema 1.1 para el centro y el agente de IoT Edge. La versión de esquema 1.1 se publicó junto con la versión 1.0.10 de IoT Edge y habilita características como el orden de inicio y la priorización de rutas del módulo.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Iniciar sesión para acceder a IoT Hub

Puede usar las extensiones de Azure IoT para Visual Studio Code para realizar operaciones con IoT Hub. Para que estas operaciones funcionen, debe iniciar sesión en su cuenta de Azure y seleccionar la instancia de IoT Hub en la que está trabajando.

1. En Visual Studio Code, abra la vista **Explorer** (Explorador).

1. En la parte inferior de Explorer, expanda la sección **Azure IoT Hub**.

   ![Expansión de la sección Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Haga clic en los puntos suspensivos **...** en el encabezado de la sección **Azure IoT Hub**. Si no ve el botón de los puntos suspensivos, mantenga el mouse sobre el encabezado.

1. Elija **Select IoT Hub** (Seleccionar IoT Hub).

1. Si no ha iniciado sesión en su cuenta de Azure, siga las indicaciones para hacerlo.

1. Seleccione su suscripción a Azure.

1. Seleccione IoT Hub.

## <a name="deploy-to-your-device"></a>Implementación en el dispositivo

Para implementar los módulos en el dispositivo, aplique el manifiesto de implementación que configuró con la información del módulo.

1. En la vista del explorador de Visual Studio Code, expanda la sección **Azure IoT Hub** y después el nodo **Dispositivos**.

1. Haga clic con el botón derecho en el dispositivo de IoT Edge que quiere configurar con el manifiesto de implementación.

    > [!TIP]
    > Para confirmar que el dispositivo que ha elegido es un dispositivo IoT Edge, selecciónelo para expandir la lista de módulos y comprobar la presencia de **$edgeHub** y **$edgeAgent**. Todos los dispositivos IoT Edge incluyen estos dos módulos.

1. Seleccione **Create Deployment for Single Device** (Crear una implementación para un dispositivo individual).

1. Vaya al archivo JSON de manifiesto de implementación que quiere usar y haga clic en **Select Edge Deployment Manifest** (Seleccionar manifiesto de implementación de Edge).

   ![Selección del manifiesto de implementación de Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Los resultados de la implementación se imprimen en la salida de VS Code. Las implementaciones correctas se aplican a los pocos minutos si el dispositivo de destino está en ejecución y conectado a Internet.

## <a name="view-modules-on-your-device"></a>Visualización de módulos en el dispositivo

Una vez que haya implementado los módulos en el dispositivo, puede verlos todos en la sección **Azure IoT Hub**. Seleccione la flecha situada junto a su dispositivo IoT Edge para expandirlo. Se muestran todos los módulos actualmente en ejecución.

Si ha implementado recientemente nuevos módulos en un dispositivo, mantenga el mouse sobre el encabezado de la sección **Azure IoT Hub Devices** (Dispositivos de Azure IoT Hub) y seleccione el icono de actualización para actualizar la vista.

Haga clic con el botón derecho en el nombre del módulo para ver y editar el módulo gemelo.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [implementar y supervisar módulos de IoT Edge a escala mediante Visual Studio Code](how-to-deploy-at-scale.md)
