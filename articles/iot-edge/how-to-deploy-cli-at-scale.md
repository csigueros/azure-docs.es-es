---
title: 'Implementación de módulos a escala mediante la CLI de Azure: Azure IoT Edge'
description: Use la extensión de IoT para la CLI de Azure para crear implementaciones automáticas para grupos de dispositivos IoT Edge.
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 678090d4c4de0dfd306bd5b91a26787f9ff15db2
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455861"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-by-using-the-azure-cli"></a>Implementación y supervisión de módulos de IoT Edge a gran escala mediante la CLI de Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Cree una [implementación automática de Azure IoT Edge](module-deployment-monitoring.md) mediante la interfaz de la línea de comandos de Azure para administrar las implementaciones en curso de muchos dispositivos a la vez. Las implementaciones automáticas de IoT Edge forman parte de la característica de [Administración de dispositivos automática](../iot-hub/iot-hub-automatic-device-management.md) de Azure IoT Hub. Las implementaciones son procesos dinámicos que permiten implementar varios módulos en múltiples dispositivos, realizar un seguimiento del estado y del mantenimiento de los módulos, y realizar cambios cuando sea necesario.

En este artículo, configuró la CLI de Azure y la extensión de IoT. A continuación, aprenderá a implementar módulos en un conjunto de dispositivos IoT Edge y a supervisar el progreso mediante los comandos de la CLI disponibles.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) en la suscripción de Azure.
* Uno o más dispositivos IoT Edge.

  Si no tiene un dispositivo IoT Edge configurado, puede crear uno en una máquina virtual de Azure. Siga los pasos de alguno de estos artículos de inicio rápido: [Crear un dispositivo virtual Linux](quickstart-linux.md) o [Crear un dispositivo virtual Windows](quickstart.md).

* La [CLI de Azure](/cli/azure/install-azure-cli) en su entorno. La versión de la CLI de Azure debe ser 2.0.70 o posterior. Use `az --version` para asegurarse. Esta versión admite comandos az extension e introduce la plataforma de comandos de Knack.
* La [extensión de IoT para la CLI de Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configuración de un manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe qué módulos se van a implementar, cómo fluyen los datos entre los módulos y las propiedades deseadas de los módulos gemelos. Para más información, consulte [Aprenda a implementar módulos y establecer rutas en IoT Edge](module-composition.md).

Para implementar módulos con la CLI de Azure, guarde el manifiesto de implementación localmente como un archivo .txt. Use la ruta de acceso al archivo de la siguiente sección cuando ejecute el comando para aplicar la configuración al dispositivo.

Este es un manifiesto de implementación básico con un módulo como ejemplo:

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

>[!NOTE]
>Este manifiesto de implementación de ejemplo usa la versión de esquema 1.1 para el centro y el agente de IoT Edge. La versión de esquema 1.1 se ha publicado junto con la versión 1.0.10 de IoT Edge. Habilita características como el orden de inicio del módulo y la priorización de rutas.

## <a name="layered-deployment"></a>Implementación superpuesta

Las implementaciones superpuestas son un tipo de implementación automática que se puede apilar entre sí. Para más información sobre las implementaciones superpuestas, consulte el artículo [Descripción de las implementaciones automáticas de IoT Edge en un único dispositivo o a escala](module-deployment-monitoring.md).

Las implementaciones superpuestas se pueden crear y administrar con la CLI de Azure como cualquier implementación automática, con solo algunas diferencias. Una vez que se crea una implementación superpuesta, la CLI de Azure funciona con las implementaciones superpuestas igual que con cualquier otra implementación. Para crear una implementación superpuesta, agregue la marca `--layered` al comando CREATE.

La segunda diferencia es la construcción del manifiesto de implementación. Aunque la implementación automática estándar debe contener los módulos del entorno de ejecución del sistema además de los módulos de usuario, las implementaciones superpuestas solo pueden contener módulos de usuario. Las implementaciones superpuestas también necesitan que haya una implementación automática estándar en un dispositivo para proporcionar los componentes necesarios de cada dispositivo IoT Edge, como los módulos del entorno de ejecución del sistema.

Este es un manifiesto de implementación superpuesta básico con un módulo como ejemplo:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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
>[!NOTE]
> Este manifiesto de implementación superpuesta tiene un formato ligeramente diferente al de un manifiesto de implementación estándar. Las propiedades deseadas de los módulos en tiempo de ejecución se contraen y usan la notación de puntos. Este formato es necesario para que Azure Portal reconozca una implementación en capas. Por ejemplo:
>
> - `properties.desired.modules.<module_name>`
> - `properties.desired.routes.<route_name>`

En el ejemplo anterior se mostraba la implementación superpuesta que establecía `properties.desired` para un módulo. Si esta implementación superpuesta se dirigiera a un dispositivo en el que ya se aplicó el mismo módulo, se sobrescribirían las propiedades deseadas existentes. Para actualizar las propiedades deseadas en lugar de sobrescribirlas, puede definir una subsección nueva. Por ejemplo:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

También se puede expresar lo mismo con:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties.SendData" : true,
  "properties.desired.layeredProperties.SendInterval": 5
}
```

>[!NOTE]
>Actualmente, todas las implementaciones superpuestas deben incluir un objeto `edgeAgent` para que se consideren válidas. Incluso si una implementación en capas solo actualiza las propiedades del módulo, incluya un objeto vacío. Por ejemplo: `"$edgeAgent":{}`. Una implementación superpuesta con un objeto `edgeAgent` vacío se mostrará como **destino** en el módulo gemelo `edgeAgent`, y no se **aplicará**.

En resumen, para crear una implementación en capas:

- Agregue la marca `--layered` al comando create de la CLI de Azure.
- No incluya módulos del sistema.
- Use la notación de puntos completa en `$edgeAgent` y `$edgeHub`.

Para obtener más información sobre la configuración de los módulos gemelos en implementaciones superpuestas, consulte [Implementación superpuesta](module-deployment-monitoring.md#layered-deployment).

## <a name="identify-devices-by-using-tags"></a>Identificación de dispositivos mediante etiquetas

Antes de crear una implementación, tendrá que especificar los dispositivos que desea afectar. Azure IoT Edge identifica los dispositivos mediante *etiquetas* en el dispositivo gemelo. 

Cada dispositivo puede tener varias etiquetas que puede definir de cualquier manera que tenga sentido para su solución. Por ejemplo, si administra un campus de edificios inteligentes, puede agregar las siguientes etiquetas a un dispositivo:

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

Para más información sobre dispositivos gemelos y etiquetas, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>de una implementación

Implemente módulos en los dispositivos de destino; para ello, cree una implementación que consista en el manifiesto de implementación y otros parámetros.

Utilice el comando [az iot edge deployment create](/cli/azure/iot/edge/deployment) para crear una implementación:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Use el mismo comando con la marca `--layered` para crear una implementación superpuesta.

El comando create para la implementación adopta los siguientes parámetros:

* **--layered**. Una marca opcional para identificar la implementación como una implementación superpuesta.
* **--deployment-id**. El nombre de la implementación que se creará en IoT Hub. Asigne a su implementación un nombre exclusivo de hasta 128 letras en minúscula. Evite los espacios y los siguientes caracteres no válidos: `& ^ [ ] { } \ | " < > /`. Este parámetro es obligatorio.
* **--content**. Ruta del archivo del manifiesto de implementación JSON. Este parámetro es obligatorio.
* **--hub-name**. Nombre de la instancia de IoT Hub en la que se creará la implementación. El centro debe estar en la suscripción actual. Cambie la suscripción actual con el comando `az account set -s [subscription name]`.
* **--labels**. Pares nombre-valor que describen las implementaciones y le ayudan a realizar un seguimiento de las mismas. Las etiquetas adoptan el formato JSON en los nombres y valores. Por ejemplo: `{"HostPlatform":"Linux", "Version:"3.0.1"}`.
* **--target-condition**. La condición que determina qué dispositivos serán los destinos con esta implementación. La condición se basa en las etiquetas del dispositivo gemelo o en las propiedades notificadas del dispositivo gemelo y debe coincidir con el formato de expresión. Por ejemplo: `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority**. Un número entero positivo. Si dos o más implementaciones se destinen al mismo dispositivo, se aplicará la implementación con el valor numérico más alto para la prioridad.
* **--metrics**. Métricas que realizan consultas en las propiedades notificadas de `edgeHub` para realizar un seguimiento del estado de una implementación. Las métricas adoptan una entrada JSON o una ruta de archivo. Por ejemplo: `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Para supervisar una implementación con la CLI de Azure, vea [Supervisión de las implementaciones de IoT Edge](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Modificación de una implementación

Cuando se modifica una implementación, los cambios se replican inmediatamente a todos los dispositivos seleccionados.

Si actualiza la condición de destino, se producen las siguientes actualizaciones:

* Si un dispositivo no cumplía la antigua condición de destino, pero cumple la nueva condición de destino y esta implementación es la prioridad más alta para ese dispositivo, esta implementación se aplica al dispositivo.
* Si un dispositivo que actualmente ejecuta esta implementación ya no cumple la condición de destino, desinstala esta implementación y asume la siguiente implementación de mayor prioridad.
* Si un dispositivo que actualmente ejecuta esta implementación ya no cumple la condición de destino y no cumple la condición de destino de cualquier otra implementación, no se produce ningún cambio en el dispositivo. El dispositivo sigue ejecutando los módulos actuales en su estado actual, pero ya no se administra como parte de esta implementación. Una vez que el dispositivo cumple la condición de destino de cualquier otra implementación, desinstala esta implementación y adopta una nueva.

No puede actualizar el contenido de una implementación, lo cual incluye los módulos y las rutas definidos en el manifiesto de implementación. Si desea actualizar el contenido de una implementación, cree una implementación que tenga como destino los mismos dispositivos con una prioridad más alta. Puede modificar determinadas propiedades de un módulo existente, incluidas la condición de destino, las etiquetas, las métricas y la prioridad.

Utilice el comando [az iot edge deployment update](/cli/azure/iot/edge/deployment) para actualizar una implementación:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

El comando deployment update toma los parámetros siguientes:

* **--deployment-id**. Nombre de la implementación que está en IoT Hub.
* **--hub-name**. Nombre de la instancia de IoT Hub en la que está la implementación. El centro debe estar en la suscripción actual. Cambie a la suscripción que quiera usar mediante el comando `az account set -s [subscription name]`.
* **--set**. Actualizar una propiedad de la implementación. Puede actualizar las propiedades siguientes:
  * `targetCondition` (por ejemplo, `targetCondition=tags.location.state='Oregon'`)
  * `labels`
  * `priority`
* **--add**. Agregar una nueva propiedad a la implementación, incluidas las condiciones de destino o las etiquetas.
* **--remove**. Quitar una propiedad existente, incluidas las condiciones de destino o las etiquetas.

## <a name="delete-a-deployment"></a>Eliminación de una implementación

Cuando se elimina una implementación, los dispositivos adoptan la siguiente implementación de prioridad más alta. Si los dispositivos no cumplen la condición de destino de alguna implementación, los módulos no se quitan cuando se elimina la implementación.

Utilice el comando [az iot edge deployment delete](/cli/azure/iot/edge/deployment) para eliminar una implementación:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

El comando `deployment delete` toma los parámetros siguientes:

* **--deployment-id**. Nombre de la implementación que está en IoT Hub.
* **--hub-name**. Nombre de la instancia de IoT Hub en la que está la implementación. El centro debe estar en la suscripción actual. Cambie a la suscripción que quiera usar mediante el comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [implementación de módulos en dispositivos IoT Edge](module-deployment-monitoring.md).
