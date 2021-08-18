---
title: 'Implementación de módulos a escala con Visual Studio Code: Azure IoT Edge'
description: Use de la extensión de IoT para Visual Studio Code para crear implementaciones automáticas para grupos de dispositivos IoT Edge.
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b5eecd68a53607b9b3fc7fc24fc9aee78063fdfe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722046"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Implementación de módulos de IoT Edge a escala con Visual Studio Code

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Puede crear una **implementación automática de IoT Edge** mediante Visual Studio Code para administrar las implementaciones en curso de muchos dispositivos a la vez. Las implementaciones automáticas de IoT Edge forman parte de la característica de [Administración de dispositivos automática](../iot-hub/iot-hub-automatic-device-management.md) de IoT Hub. Las implementaciones son procesos dinámicos que permiten implementar varios módulos en varios dispositivos. También puede realizar un seguimiento del estado de los módulos, y realizar cambios cuando sea necesario.

Para más información, consulte el artículo [Descripción de las implementaciones automáticas de IoT Edge en un único dispositivo o a escala](module-deployment-monitoring.md).

En este artículo, configurará Visual Studio Code y la extensión de IoT. A continuación, aprenderá a implementar módulos en un conjunto de dispositivos IoT Edge.

## <a name="prerequisites"></a>Prerrequisitos

* Una instancia de [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) en la suscripción de Azure.
* Uno o más dispositivos IoT Edge.

  Si no tiene un dispositivo IoT Edge configurado, puede crear uno en una máquina virtual de Azure. Siga los pasos de alguno de los artículos de inicio rápido para [Crear un dispositivo virtual Linux](quickstart-linux.md) o [Crear un dispositivo virtual Windows](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Iniciar sesión para acceder a IoT Hub

Puede usar las extensiones de Azure IoT para Visual Studio Code para realizar operaciones con su instancia de IoT Hub. Para que estas operaciones funcionen, debe iniciar sesión en su cuenta de Azure y seleccionar la instancia de IoT Hub en la que está trabajando.

1. En Visual Studio Code, abra la vista **Explorer** (Explorador).

1. En la parte inferior de Explorer, expanda la sección **Azure IoT Hub**.

1. Haga clic en los puntos suspensivos **...** en el encabezado de la sección **Azure IoT Hub**. Si no ve el botón de los puntos suspensivos, mantenga el mouse sobre el encabezado.

1. Elija **Select IoT Hub** (Seleccionar IoT Hub).

1. Si no ha iniciado sesión en su cuenta de Azure, siga las indicaciones para hacerlo.

1. Seleccione su suscripción a Azure.

1. Seleccione IoT Hub.

## <a name="configure-a-deployment-manifest"></a>Configuración de un manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe los módulos que se implementarán. También describe cómo fluyen los datos entre los módulos y las propiedades deseadas de los módulos gemelos. Para más información, consulte [Aprenda a implementar módulos y establecer rutas en IoT Edge](module-composition.md).

Para implementar módulos con Visual Studio Code, guarde el manifiesto de implementación localmente como un archivo .JSON. Tendrá que proporcionar su ubicación al ejecutar el comando para aplicar la configuración al dispositivo.

Este es un manifiesto de implementación básico con un módulo como ejemplo:

>[!NOTE]
>Este manifiesto de implementación de ejemplo usa la versión de esquema 1.1 para el centro y el agente de IoT Edge. La versión de esquema 1.1 se publicó junto con la versión 1.0.10 de IoT Edge y habilita características como el orden de inicio y la priorización de rutas del módulo.

```json
{
  "content": {
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
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
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Si necesita determinar qué dispositivos IoT Edge puede configurar actualmente, ejecute el comando **IoT Edge: Get Device Info**.

## <a name="identify-devices-with-target-conditions"></a>Identificación de dispositivos con condiciones de destino

Para identificar los dispositivos IoT Edge que van a recibir la implementación, debe especificar una condición de destino. Una condición de destino se cumple cuando los criterios especificados coinciden con un identificador deviceId, un valor de etiqueta o un valor de propiedad indicado.

Las etiquetas se configuran en el dispositivo gemelo. Este es un ejemplo de un dispositivo gemelo que tiene etiquetas:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Este dispositivo recibirá una implementación si la condición de destino de la implementación contiene una expresión que coincide con uno de los valores de la etiqueta, como `tag.location.building = '20'`.

Si desea establecer como destino un dispositivo específico independientemente de sus etiquetas u otros valores, solo tiene que especificar el valor `deviceId` para la condición de destino.

Estos son otros ejemplos:

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' OR deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' AND tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'

Consulte la información de [condición de destino](module-deployment-monitoring.md#target-condition) para obtener más información. Para más información sobre dispositivos gemelos y etiquetas, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Edición del dispositivo gemelo

Puede editar el dispositivo gemelo en Visual Studio Code para configurar las etiquetas. En el menú **Vista**, seleccione **Paleta de comandos** y ejecute el comando **IOT Edge: Edit Device Twin**. Seleccione el dispositivo de IoT Edge y aparece el dispositivo gemelo.

En este ejemplo, no se ha definido ninguna etiqueta. Reemplace la sección vacía actual `"tags": {}` por su propia definición de etiquetas.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Después de guardar el archivo local, ejecute el comando **IoT Edge: Update Device Twin**.

## <a name="create-deployment-at-scale"></a>Creación de una implementación a escala

Después de haber configurado el manifiesto de implementación y las etiquetas en el dispositivo gemelo, está listo para la implementación.

1. En el menú **Vista**, seleccione **Paleta de comandos** y seleccione el comando **Azure IoT Edge: Create Deployment at Scale**.

1. Vaya al archivo JSON de manifiesto de implementación que quiere usar y haga clic en **Select Edge Deployment Manifest** (Seleccionar manifiesto de implementación de Edge).

1. Proporcione valores cuando se le solicite, empezando por el **id. de implementación**.

   ![Especificación de un id. de implementación](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Especifique valores para estos parámetros:

  | Parámetro | Descripción |
  | --- | --- |
  | Id. de implementación | El nombre de la implementación que se creará en IoT Hub. Asigne a su implementación un nombre exclusivo de hasta 128 letras en minúscula. Evite los espacios y los siguientes caracteres no válidos: `& ^ [ ] { } \ | " < > /`. |
  | Condición de destino | Escriba una condición de destino para determinar qué dispositivos se dirigirán a esta implementación.  La condición se basa en las etiquetas del dispositivo gemelo o en las propiedades notificadas del dispositivo gemelo y debe coincidir con el formato de expresión. Por ejemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Prioridad |  Un entero positivo. Si dos o más implementaciones se destinen al mismo dispositivo, se aplicará la implementación con el valor numérico más alto para la prioridad. |

  Después de especificar la prioridad, el terminal debe mostrar una salida similar a la siguiente:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Supervisión y modificación de implementaciones

Use [Azure Portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) o la [CLI de Azure](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) para supervisar, modificar y eliminar implementaciones. Ambos proporcionan métricas en las implementaciones.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [implementación de módulos en dispositivos IoT Edge](module-deployment-monitoring.md).