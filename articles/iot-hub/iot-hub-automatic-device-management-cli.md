---
title: Administración de dispositivos automática a escala con Azure IoT Hub (CLI)| Microsoft Docs
description: Uso de configuraciones automáticas de Azure IoT Hub para administrar varios dispositivos o módulos IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: robinsh
ms.openlocfilehash: abbef6e0e244f7cfc937f738c7c2d0acd89549dd
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114205206"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Administración automática de dispositivos y módulos IoT mediante la CLI de Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

La administración de dispositivos automática de Azure IoT Hub automatiza muchas de las tareas repetitivas y complejas de administración de grandes flotas de dispositivos. Con la administración de dispositivos automática, puede tener como destino un conjunto de dispositivos según sus propiedades, definir una configuración que se quiera y luego permitir que IoT Hub actualice los dispositivos cuando estén dentro del ámbito. Esta actualización se realiza mediante una *configuración automática de dispositivos* o una *configuración automática de módulos*, que permite resumir la integridad y el cumplimiento, administrar combinaciones y conflictos e implementar configuraciones por fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

La administración automática de dispositivos funciona mediante la actualización de un conjunto de dispositivos o módulos gemelos con las propiedades deseadas y la elaboración de un informe de resumen basado en las propiedades notificadas de los gemelos.  Presenta una nueva clase y documento JSON, llamado *Configuración*, que tiene tres partes:

* La **condición de destino** define el ámbito de los dispositivos o módulos gemelos que se van a actualizar. La condición de destino se especifica como una consulta de las propiedades notificadas o de las etiquetas de los dispositivos gemelos.

* El **contenido de destino** define las propiedades deseadas que se van a agregar o actualizar en los dispositivos o módulos gemelos de destino. El contenido incluye una ruta de acceso a la sección de las propiedades que desea cambiar.

* Las **métricas** definen el número de resúmenes de los distintos estados de configuración, como **Correcto**, **En curso** y **Error**. Las métricas personalizadas se especifican como consultas de las propiedades notificadas de los gemelos. Las métricas del sistema son las métricas predeterminadas que miden el estado de actualización de los gemelos, como el número de gemelos de destino y el número de gemelos que se han actualizado correctamente.

Las configuraciones automáticas se ejecutan por primera vez poco después de crear la configuración y posteriormente en intervalos de cinco minutos. Las consultas de métricas se ejecutan cada vez que se ejecuta la configuración automática.

## <a name="cli-prerequisites"></a>Requisitos previos de la CLI

* Una instancia de [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) en la suscripción de Azure. 

* La [CLI de Azure](/cli/azure/install-azure-cli) en su entorno. Como mínimo, la versión de la CLI de Azure debe ser la 2.0.70. Use `az –-version` para asegurarse. Esta versión admite comandos az extension e introduce la plataforma de comandos de Knack. 

* La [extensión de IoT para la CLI de Azure](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementación de gemelos

Las configuraciones automáticas de dispositivos requieren el uso de dispositivos gemelos para sincronizar el estado entre la nube y los dispositivos.  Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](iot-hub-devguide-device-twins.md).

Las configuraciones automáticas de módulos requieren el uso de módulos gemelos para sincronizar el estado entre la nube y los módulos. Para más información, consulte [Uso de módulos gemelos en IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Uso de etiquetas para identificar los gemelos

Antes de crear una configuración, debe especificar a qué dispositivos o módulos quiere que se aplique. Azure IoT Hub identifica dispositivos mediante etiquetas en el dispositivo gemelo e identifica módulos mediante etiquetas en el módulo gemelo. Cada dispositivo o módulo puede tener varias etiquetas, y puede definirlas de cualquier manera que tenga sentido para su solución. Por ejemplo, si administra dispositivos en distintas ubicaciones, agregue las siguientes etiquetas a un dispositivo gemelo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definir el contenido de destino y las métricas

El contenido de destino y las consultas de métricas se especifican como documentos JSON que describen las propiedades deseadas del dispositivo o módulo gemelo que quiere establecer y las propiedades notificadas que se van a medir.  Para crear una configuración automática mediante la CLI de Azure, guarde el contenido y las métricas de destino de forma local como archivos .txt. Usará las rutas de acceso de los archivos en una sección posterior en la que ejecutará el comando para aplicar la configuración al dispositivo.

A continuación se muestra un ejemplo básico de contenido de destino para una configuración automática de dispositivos:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Las configuraciones automáticas de módulos se comportan de manera muy similar, pero el destino es `moduleContent` en lugar de `deviceContent`.

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Aquí tiene ejemplos de consultas de métrica:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Las consultas de métricas de los módulos también son similares a las consultas de los dispositivos, pero se selecciona `moduleId` en `devices.modules`. Por ejemplo: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Creación de una configuración

Debe configurar los dispositivos de destino creando una configuración que tenga el contenido y las métricas de destino. 

Use el comando siguiente para crear una configuración:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id**: el nombre de la configuración que se creará en IoT Hub. Asigne a su configuración un nombre exclusivo de hasta 128 letras en minúscula. Evite los espacios y los siguientes caracteres no válidos: `& ^ [ ] { } \ | " < > /`.

* --**labels**: agrega etiquetas para ayudar a realizar un mejor seguimiento de la configuración. Las etiquetas son pares de Nombre y Valor que describen la implementación. Por ejemplo, `HostPlatform, Linux` o `Version, 3.0.1`.

* --**content**: formato JSON alineado o ruta de acceso del archivo al contenido de destino que se establecerá como las propiedades gemelas que quiera. 

* --**hub-name**: nombre de la instancia de IoT Hub en la que se creará la configuración. El centro debe estar en la suscripción actual. Cambie a la suscripción que quiera usar con el comando `az account set -s [subscription name]`.

* --**target-condition**: escriba una condición de destino para determinar qué dispositivos o módulos se identificarán con esta configuración.  En la configuración automática de dispositivos, la condición se basa en las etiquetas del dispositivo gemelo o en sus propiedades deseadas y debe coincidir con el formato de expresión.  Por ejemplo, `tags.environment='test'` o `properties.desired.devicemodel='4000x'`.  En la configuración automática de módulos, la condición se basa en las etiquetas del módulo gemelo o en sus propiedades deseadas. Por ejemplo, `from devices.modules where tags.environment='test'` o `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**priority**: debe ser un entero positivo. En el caso de que dos o más configuraciones se destinen al mismo dispositivo o módulo, se aplicará la configuración que tenga el mayor valor numérico, según la prioridad.

* --**metrics**: ruta de acceso de archivo a las consultas de métrica. Las métricas proporcionan el número de resúmenes de los distintos estados que un dispositivo o módulo puede notificar después de aplicar el contenido de configuración. Por ejemplo, puede crear una métrica para los cambios de configuración pendientes, una métrica para los errores y una métrica para los cambios de configuración correctos. 

## <a name="monitor-a-configuration"></a>Supervisión de una configuración

Use los comandos siguientes para ver el contenido de una configuración:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id**: el nombre de la configuración que está en IoT Hub.

* --**hub-name**: el nombre de la instancia de IoT Hub en la que está la configuración. El centro debe estar en la suscripción actual. Cambie a la suscripción que quiera usar con el comando `az account set -s [subscription name]`.

Revise la configuración en la ventana de comandos.  La propiedad **metrics** enumera un recuento para cada métrica que evalúa cada centro:

* **targetedCount**: una métrica del sistema que especifica la cantidad de dispositivos o módulos gemelos en IoT Hub que coinciden con la condición de destino.

* **appliedCount**: una métrica del sistema que especifica la cantidad de dispositivos o módulos cuyo contenido de destino se ha aplicado.

* **La métrica personalizada**: cualquier métrica que haya definido se considera métrica de usuario.

Puede mostrar una lista de identificadores de dispositivo, identificadores de módulo u objetos para cada una de las métricas mediante el siguiente comando:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id**: el nombre de la implementación que está en IoT Hub.

* --**metric-id**: el nombre de la métrica de la cual quiere ver la lista de identificadores de dispositivo o módulo, por ejemplo `appliedCount`.

* --**hub-name**: nombre de la instancia de IoT Hub en la que está la implementación. El centro debe estar en la suscripción actual. Cambie a la suscripción que quiera usar con el comando `az account set -s [subscription name]`.

* --**metric-type**: el tipo de métrica puede ser `system` o `user`.  Las métricas del sistema son `targetedCount` y `appliedCount`. El resto de métricas son métricas de usuario.

## <a name="modify-a-configuration"></a>Modificación de una configuración

Cuando se modifica una configuración, los cambios se replican inmediatamente a todos los dispositivos seleccionados. 

Si actualiza la condición de destino, se producen las siguientes actualizaciones:

* Si un gemelo no cumplía la condición de destino anterior, pero cumple la nueva condición de destino y esta configuración es la prioridad más alta para ese gemelo, se aplica esta configuración. 

* Si un gemelo que ejecuta actualmente esta configuración ya no cumple la condición de destino, los valores de la configuración se quitarán y el gemelo se modificará con la configuración de la siguiente prioridad más alta. 

* Si un gemelo que ejecuta actualmente esta configuración ya no cumple la condición de destino y tampoco la condición de destino de cualquier otra configuración, los valores de la configuración se eliminarán y no se realizará ningún otro cambio en el gemelo. 

Use los comandos siguientes para actualizar una configuración:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id**: el nombre de la configuración que está en IoT Hub.

* --**hub-name**: el nombre de la instancia de IoT Hub en la que está la configuración. El centro debe estar en la suscripción actual. Cambie a la suscripción que quiera usar con el comando `az account set -s [subscription name]`.

* --**set**: actualiza una propiedad de la configuración. Puede actualizar las propiedades siguientes:

    * targetCondition: por ejemplo, `targetCondition=tags.location.state='Oregon'`.

    * labels 

    * priority

## <a name="delete-a-configuration"></a>Eliminación de una configuración

Al eliminar una configuración, los dispositivos o módulos gemelos adoptan la configuración con la siguiente prioridad más alta. Si los gemelos no cumplen la condición de destino de ninguna otra configuración, no se aplica ningún otro valor. 

Use los comandos siguientes para eliminar una configuración:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id**: el nombre de la configuración que está en IoT Hub.

* --**hub-name**: el nombre de la instancia de IoT Hub en la que está la configuración. El centro debe estar en la suscripción actual. Cambie a la suscripción que quiera usar con el comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo configurar y supervisar dispositivos IoT a escala. Siga estos vínculos para más información sobre la administración de Azure IoT Hub:

* [Administración de las identidades de dispositivo de IoT Hub de forma masiva](iot-hub-bulk-identity-mgmt.md)
* [Supervisión del centro de IoT](monitor-iot-hub.md)

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub](iot-hub-devguide.md)
* [Implementación de IA en dispositivos perimetrales con Azure IoT Edge](../iot-edge/quickstart-linux.md)

Para explorar el uso del servicio IoT Hub Device Provisioning para habilitar el aprovisionamiento Just-In-Time sin intervención del usuario, vea: 

* [Servicio Azure IoT Hub Device Provisioning](../iot-dps/index.yml)