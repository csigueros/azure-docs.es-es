---
title: 'Implementación de módulos y rutas con manifiestos de implementación: Azure IoT Edge'
description: Obtenga información sobre cómo un manifiesto de implementación declara qué módulos se deben implementar, cómo implementarlos y cómo crear rutas de mensajes entre ellos.
author: kgremban
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a83e2f8c14b2dcb4c97d1189ad262b3ed51b2d79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726512"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Obtenga información sobre cómo implementar módulos y establecer rutas en IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Cada dispositivo IoT Edge ejecuta al menos dos módulos: $edgeAgent y $edgeHub, que constituyen el entorno de ejecución de Azure IoT Edge. El dispositivo IoT Edge puede ejecutar varios módulos adicionales para llevar a cabo cualquier número de procesos. Use un manifiesto de implementación para indicar al dispositivo qué módulos debe instalar y cómo configurarlos para que funcionen conjuntamente.

El *manifiesto de implementación* es un documento JSON que describe lo siguiente:

* El módulo gemelo del **agente de IoT Edge**, que incluye tres componentes:
  * La imagen de contenedor para cada módulo que se ejecuta en el dispositivo.
  * Las credenciales para tener acceso a registros privados del contenedor que contienen imágenes del módulo.
  * Instrucciones sobre cómo se debe crearse y administrar cada módulo.
* El módulo gemelo del **centro de IoT Edge**, que incluye cómo fluyen los mensajes entre los módulos y finalmente hacia IoT Hub.
* Las propiedades deseadas de cualquier módulo gemelo adicional (opcional).

Todos los dispositivos IoT Edge deben configurarse con un manifiesto de implementación. Si no, una instancia de IoT Edge en tiempo de ejecución recién instalada notificará un código de error hasta que se configuren con un manifiesto válido.

En los tutoriales de Azure IoT Edge, creará un manifiesto de implementación a través de un asistente del portal de Azure IoT Edge. También puede aplicar un manifiesto de implementación mediante programación con REST o el SDK del servicio IoT Hub. Para más información, consulte el artículo [Descripción de las implementaciones de IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Creación de un manifiesto de implementación

A nivel general, un manifiesto de implementación es una lista de módulos gemelos que se configuran con sus propiedades deseadas. Un manifiesto de implementación indica a un dispositivo IoT Edge (o a un grupo de dispositivos) qué módulos debe instalar y cómo configurarlos. Los manifiestos de implementación incluyen las *propiedades deseadas* de cada módulo gemelo. Los dispositivos IoT Edge informan sobre las *propiedades notificadas* de cada módulo.

Se requieren dos módulos en cada manifiesto de implementación: `$edgeAgent` y `$edgeHub`. Estos módulos forman parte del entorno de ejecución de Azure IoT Edge que administra el dispositivo IoT Edge y los módulos que se ejecutan en él. Para más información acerca de estos módulos, consulte [Información sobre el entorno de ejecución de Azure IoT Edge y su arquitectura](iot-edge-runtime.md).

Además de los dos módulos en tiempo de ejecución, puede agregar hasta 50 módulos de su elección para que se ejecuten en un dispositivo IoT Edge.

Un manifiesto de implementación que contiene solo el entorno de ejecución de Azure IoT Edge (edgeAgent y edgeHub) es válido.

Los manifiestos de implementación siguen esta estructura:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>Configuración de módulos

Defina cómo instala el entorno de ejecución de Azure IoT Edge los módulos en la implementación. El agente de IoT Edge es el componente del entorno de ejecución que administra la instalación, las actualizaciones y los informes de estado de un dispositivo IoT Edge. Por tanto, el módulo gemelo $edgeAgent contiene la información de configuración y administración de todos los módulos. Esta información incluye los parámetros de configuración para el propio agente de IoT Edge.

Las propiedades de $edgeAgent siguen esta estructura:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

La versión de esquema 1.1 del agente de IoT Edge se ha publicado junto con la versión 1.0.10 de IoT Edge y habilita el orden de inicio del módulo. Esta versión de esquema 1.1 se recomienda para cualquier implementación de IoT Edge que ejecute la versión 1.0.10 o posterior.

### <a name="module-configuration-and-management"></a>Configuración y administración de módulos

La lista de propiedades deseadas del agente de IoT Edge es el lugar donde se definen los módulos que se implementan en un dispositivo IoT Edge, y cómo deben configurarse y administrarse.

Para ver una lista completa de propiedades deseadas que pueden o deben incluirse, consulte [Propiedades del agente de IoT Edge y del centro de IoT Edge](module-edgeagent-edgehub.md).

Por ejemplo:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Todos los módulos tienen una propiedad **settings** que contiene el módulo **image**, una dirección para la imagen de contenedor en un registro de contenedor y cualquier valor **createOptions** para configurar la imagen en el inicio. Para más información, consulte [Configuración de las opciones de creación de contenedores para módulos de IoT Edge](how-to-use-create-options.md).

El módulo edgeHub y los módulos personalizados también tienen tres propiedades que indican al agente de IoT Edge cómo administrarlos:

* **Estado**: si el módulo debe estar en ejecución o detenido cuando se implementa por primera vez. Necesario.
* **RestartPolicy**: cuándo y si el agente de IoT Edge debe reiniciar el módulo en caso de que se detenga. Necesario.
* **StartupOrder**: *introducido en IoT Edge versión 1.0.10*. Señala el orden en que el agente de IoT Edge debe iniciar los módulos la primera vez que se implementan. El orden se declara con enteros, donde el módulo con un valor de inicio de 0 se inicia primero y, a continuación, siguen los números mayores. El módulo edgeAgent no tiene un valor de inicio porque siempre es el primero que se inicia. Opcional.

  El agente de IoT Edge inicia los módulos según el orden del valor de inicio, pero no espera a que finalice el inicio de cada módulo antes de pasar al siguiente.

  El orden de inicio es útil si unos módulos dependen de otros. Por ejemplo, puede que desee que el módulo edgeHub se inicie primero para que esté listo para enrutar los mensajes cuando se inicien los otros módulos. O bien, puede que desee iniciar un módulo de almacenamiento antes que los módulos que le envían datos. Sin embargo, siempre debe diseñar los módulos para administrar los errores de otros módulos. Está en la naturaleza de los contenedores poder detenerse y reiniciarse en cualquier momento y cualquier número de veces.

## <a name="declare-routes"></a>Declaración de rutas

El centro de IoT Edge administra la comunicación entre los módulos, IoT Hub y todos los dispositivos de hoja. Por tanto, el módulo gemelo $edgeHub contiene una propiedad deseada denominada *routes* que declara cómo se pasan los mensajes en una implementación. Puede tener varias rutas dentro de la misma implementación.

Las rutas se declaran en las propiedades deseadas de **$edgeHub** con la sintaxis siguiente:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

La versión de esquema 1.1 del centro de IoT Edge se ha publicado junto con la versión 1.0.10 de IoT Edge y habilita la priorización de rutas y el período de vida. Esta versión de esquema 1.1 se recomienda para cualquier implementación de IoT Edge que ejecute la versión 1.0.10 o posterior.

Cada ruta necesita un *origen* del que proceden los mensajes y un *receptor* al que se envían. La *condición* es una parte opcional que puede usar para filtrar los mensajes.

Puede asignar *prioridad* a las rutas en las que desea asegurarse de que sus mensajes se procesan antes. Esta característica es útil en escenarios donde la conexión ascendente es débil o limitada y tiene datos críticos a los que se debe dar prioridad por encima de los mensajes de datos de telemetría estándar.

### <a name="source"></a>Source

El origen especifica de dónde proceden los mensajes. IoT Edge puede enrutar los mensajes desde los dispositivos de hoja o desde los módulos.

Mediante el SDK de IoT, los módulos pueden declarar colas de salida específicas para sus mensajes mediante la clase ModuleClient. Las colas de salida no son necesarias, pero son útiles para administrar varias rutas. Los dispositivos de hoja pueden usar la clase DeviceClient de los SDK de IoT para enviar mensajes a dispositivos de puerta de enlace de IoT Edge de la misma manera que enviarían mensajes a IoT Hub. Para más información, consulte [Información y uso de los SDK de Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).

La propiedad de origen puede ser cualquiera de los siguientes valores:

| Source | Descripción |
| ------ | ----------- |
| `/*` | Todos los mensajes del dispositivo a la nube o las notificaciones de cambios de gemelos de cualquier módulo o dispositivo de hoja. |
| `/twinChangeNotifications` | Todos los cambios de gemelos (propiedades notificadas) procedentes de todos los módulos o dispositivos de hoja. |
| `/messages/*` | Cualquier mensaje de dispositivo a nube que envíe un dispositivo de hoja o un módulo con alguna salida o sin ninguna. |
| `/messages/modules/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo con alguna salida o sin ninguna. |
| `/messages/modules/<moduleId>/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo específico con alguna salida o sin ninguna. |
| `/messages/modules/<moduleId>/outputs/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo específico mediante alguna salida. |
| `/messages/modules/<moduleId>/outputs/<output>` | Cualquier mensaje de dispositivo a nube que envíe un módulo específico mediante una salida específica. |

### <a name="condition"></a>Condición

La condición es opcional en una declaración de ruta. Si desea pasar todos los mensajes desde el receptor al origen, omita la cláusula **WHERE** por completo. O bien, puede usar el [lenguaje de consulta de IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar por determinados mensajes o tipos de mensajes que cumplen la condición. Las rutas de IoT Edge no admiten mensajes de filtrado basados en etiquetas o propiedades de gemelos.

Los mensajes que pasan entre módulos con IoT Edge tienen el mismo formato que los mensajes que pasan entre los dispositivos y Azure IoT Hub. Todos los mensajes tienen el formato JSON y tienen los parámetros **systemProperties**, **appProperties** y **body**.

Puede generar consultas en función de cualquiera de los tres parámetros con la sintaxis siguiente:

* Propiedades del sistema: `$<propertyName>` o `{$<propertyName>}`
* Propiedades de la aplicación: `<propertyName>`
* Propiedades del cuerpo: `$body.<propertyName>`

Para obtener ejemplos sobre cómo crear consultas para las propiedades de mensaje, consulte [Expresiones de consulta de rutas de mensajes de dispositivo a la nube](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Un ejemplo que es específico de IoT Edge es cuando desea filtrar los mensajes recibidos en un dispositivo de puerta de enlace desde un dispositivo hoja. Los mensajes que proceden de los módulos incluyen una propiedad del sistema denominada **connectionModuleId**. Así pues, si desea enrutar los mensajes desde dispositivos hoja directamente a IoT Hub, utilice la siguiente ruta para excluir mensajes de módulo:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Receptor

El receptor define dónde se envían los mensajes. Solo los módulos e IoT Hub pueden recibir mensajes. No se pueden enrutar mensajes a otros dispositivos. No hay opción de carácter comodín en la propiedad del receptor.

La propiedad del receptor puede ser cualquiera de los siguientes valores:

| Receptor | Descripción |
| ---- | ----------- |
| `$upstream` | Envía el mensaje a IoT Hub. |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Envía el mensaje a una entrada específica de un módulo específico. |

IoT Edge proporciona garantías de por lo menos una vez. El centro de IoT Edge almacena mensajes localmente en caso de que una ruta no pueda entregar el mensaje a su receptor. Por ejemplo, si el centro de IoT Edge no se puede conectar a IoT Hub o el módulo de destino no está conectado.

El centro de IoT Edge almacena los mensajes durante el tiempo especificado en la propiedad `storeAndForwardConfiguration.timeToLiveSecs` de las [propiedades deseadas del centro de IoT Edge](module-edgeagent-edgehub.md).

### <a name="priority-and-time-to-live"></a>Prioridad y período de vida

Las rutas se pueden declarar con solo una cadena que define la ruta, o como un objeto que toma una cadena de ruta, un entero de prioridad y un entero de período de vida.

Opción 1:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Opción 2, introducida en IoT Edge versión 1.0.10 con la versión de esquema 1.1 del centro de IoT Edge:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**priority** pueden tener un valor de 0 a 9, ambos inclusive, donde 0 es la prioridad más alta. Los mensajes se ponen en cola en función de sus puntos de conexión. Todos los mensajes de prioridad 0 que tienen como destino un punto de conexión específico se procesarán antes que los mensajes de prioridad 1 que tienen como destino el mismo punto de conexión y así consecutivamente. Si varias rutas para el mismo punto de conexión tienen la misma prioridad, sus mensajes se procesarán de acuerdo al orden de llegada. Si no se especifica ninguna prioridad, la ruta se asigna a la prioridad más baja.

La propiedad **timeToLiveSecs** hereda su valor de la propiedad **storeAndForwardConfiguration** del centro de IoT Edge, a menos que se establezca explícitamente. El valor puede ser cualquier entero positivo.

Para obtener información detallada sobre cómo se administran las colas de prioridad, consulte la página de referencia de [prioridad de ruta y período de vida](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md).

## <a name="define-or-update-desired-properties"></a>Definición o actualización de las propiedades deseadas

El manifiesto de implementación especifica las propiedades deseadas de cada módulo implementado en el dispositivo IoT Edge. Las propiedades deseadas del manifiesto de implementación sobrescriben a las que estén presentes en el módulo gemelo.

Si no se especifican las propiedades deseadas del módulo gemelo en el manifiesto de implementación, IoT Hub no modificará el módulo. En su lugar, se podrán establecer las propiedades deseadas mediante programación.

Los módulos gemelos se modifican con los mismos mecanismos que los dispositivos gemelos. Para más información vea la [guía para desarrolladores de módulos gemelos](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Ejemplo de manifiesto de implementación

El ejemplo siguiente muestra el aspecto de un documento de manifiesto de implementación válido.

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
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
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
            "startupOrder": 0,
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
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
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
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para ver una lista completa de propiedades que pueden o deben incluirse en $edgeAgent y $edgeHub, consulte [Properties of the IoT Edge agent and IoT Edge hub](module-edgeagent-edgehub.md) (Propiedades del agente de IoT Edge y el centro de IoT Edge).

* Ahora que sabe cómo se usan los módulos de IoT Hub, [descubra los requisitos y las herramientas para desarrollar módulos de IoT Edge](module-development.md).
