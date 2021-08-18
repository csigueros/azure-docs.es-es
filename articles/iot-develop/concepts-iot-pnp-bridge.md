---
title: Puente IoT Plug and Play | Microsoft Docs
description: Comprenda el puente Iot Plug and Play y cómo usarlo para conectar dispositivos existentes conectados a una puerta de enlace Windows o Linux como dispositivos IoT Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.custom: mvc
ms.openlocfilehash: 975b663be048d4438b14fce29e1a055bbd979643
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406567"
---
# <a name="iot-plug-and-play-bridge"></a>Puente de IoT Plug and Play

El puente Iot Plug and Play es una aplicación de código abierto para conectar dispositivos existentes conectados a una puerta de enlace Windows o Linux como dispositivos IoT Plug and Play. Después de instalar y configurar la aplicación en la máquina Windows o Linux, puede usarla para conectar los dispositivos conectados a IoT Hub. Puede usar el puente para asignar interfaces de IoT Plug and Play a la telemetría que los dispositivos conectados envían, trabajar con propiedades de dispositivo e invocar comandos.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="En el lado izquierdo, hay un par de sensores existentes conectados (tanto cableados como inalámbricos) a un equipo Windows o Linux que contiene un puente IoT Plug and Play. A continuación, el puente IoT Plug and Play se conecta a IoT Hub en el lado derecho.":::

El puente IoT Plug and Play se puede implementar como un archivo ejecutable independiente en cualquier dispositivo IoT, equipo industrial, servidor o puerta de enlace que ejecute Windows 10 o Linux. También se puede compilar en el código de la aplicación. Un archivo JSON de configuración sencillo indica al puente IoT Plug and Play qué dispositivos o periféricos conectados deben exponerse a Azure.

## <a name="supported-protocols-and-sensors"></a>Protocolos y sensores admitidos

El puente IoT Plug and Play admite los siguientes tipos de periféricos de forma predeterminada, con vínculos a la documentación del adaptador:

|Periférico|Windows|Linux|
|---------|---------|---------|
|El [adaptador del sensor Bluetooth](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) conecta los sensores Bluetooth de bajo consumo (BLE) habilitados detectados.       |Sí|No|
|El [adaptador de cámara](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) conecta las cámaras en un dispositivo con Windows 10.               |Sí|No|
|El [adaptador de Modbus](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) conecta los sensores en un dispositivo Modbus.              |Sí|Sí|
|El [adaptador de MQTT](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) conecta los dispositivos que usan un agente de MQTT.                  |Sí|Sí|
|El [adaptador SerialPnP](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) conecta los dispositivos que se comunican a través de una conexión serie.               |Sí|Sí|
|Los [periféricos USB de Windows](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) usan una lista de clases de interfaz de dispositivo compatibles con el adaptador para conectar los dispositivos que tienen un identificador de hardware específico.  |Sí|No aplicable|

Para obtener información sobre cómo ampliar el puente IoT Plug and Play para admitir protocolos de dispositivo adicionales, consulte [Extensión del puente IoT Plug and Play](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/author_adapter.md). Para más información sobre cómo crear e implementar el puente IoT Plug and Play, consulte [Creación e implementación de un puente IoT Plug and Play](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/build_deploy.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>Arquitectura de puente IoT Plug and Play

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="En el lado izquierdo, hay varios cuadros que indican que hay distintos periféricos conectados a un equipo Windows o Linux que contienen un puente IoT Plug and Play. Desde la parte superior, una casilla denominada Configuración apunta al puente. A continuación, el puente se conecta a una instancia de IoT Hub en el lado derecho del diagrama.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>Adaptadores de puente IoT Plug and Play

El puente IoT Plug and Play admite un conjunto de adaptadores de puente IoT Plug and Play para diversos tipos de dispositivos. Un *manifiesto de adaptador* define estáticamente los adaptadores para un puente.

El administrador del adaptador de puente usa el manifiesto para identificar y llamar a las funciones del adaptador. El administrador del adaptador solo llama a la función de creación en los adaptadores de puente requeridos por los componentes de interfaz indicados en el archivo de configuración. Se crea una instancia de adaptador para cada componente IoT Plug and Play.

Un adaptador de puente crea y adquiere un controlador de interfaz de gemelo digital. El adaptador usa este controlador para enlazar la funcionalidad del dispositivo con el gemelo digital.

Con la información del archivo de configuración, el adaptador de puente usa las siguientes técnicas para habilitar la comunicación completa del dispositivo al gemelo digital a través del puente:

- Establece un canal de comunicación directamente.
- Crea un monitor de dispositivo para esperar a que un canal de comunicación esté disponible.

### <a name="configuration-file"></a>Archivo de configuración

El puente IoT Plug and Play usa un archivo de configuración basado en JSON que especifica:

- Procedimiento para conectarse a una aplicación de centro de IoT o IoT Central: Las opciones incluyen cadenas de conexión, parámetros de autenticación o Device Provisioning Service (DPS).
- La ubicación de los modelos de capacidad de IoT Plug and Play que usa el puente. El modelo define las capacidades de un dispositivo IoT Plug and Play y es estático e inmutable.
- Una lista de los componentes de la interfaz IoT Plug and Play y la información siguiente para cada componente:
- El identificador de interfaz y el nombre del componente.
- El adaptador de puente necesario para interactuar con el componente.
- Información del dispositivo que el adaptador de puente necesita para establecer comunicación con el dispositivo. Por ejemplo, el identificador de hardware, o información específica de un adaptador, una interfaz o un protocolo.
- Una configuración de interfaz o un subtipo de adaptador de puente opcional si el adaptador admite varios tipos de comunicación con dispositivos similares. En el ejemplo se muestra cómo se puede configurar un componente de sensor Bluetooth:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Una lista opcional de parámetros globales del adaptador de puente. Por ejemplo, el adaptador de puente de sensor Bluetooth tiene un diccionario de configuraciones admitidas. Un componente de interfaz que requiere el adaptador de sensor Bluetooth puede elegir una de estas configuraciones como `blesensor_identity`:

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Descarga de un puente IoT Plug and Play

Puede descargar una versión pregenerada del puente con adaptadores admitidos en las [versiones de puente de IoT Plug and Play](https://github.com/Azure/iot-plug-and-play-bridge/releases) y expandir la lista de recursos para la versión más reciente. Descargue la versión más reciente de la aplicación para el sistema operativo.

También puede descargar y ver el código fuente del puente [IoT Plug and Play en GitHub](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una visión general de la arquitectura de un puente IoT Plug and Play, los pasos siguientes sirven para obtener más información acerca de:

- [Procedimiento para conectar una muestra de puente de IoT Plug and Play que se ejecuta en Linux o Windows a IoT Hub](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/quick_start.md)
- [Compilación e implementación de un puente de IoT Plug and Play](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/build_deploy.md)
- [Extensión de un puente de IoT Plug and Play](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/author_adapter.md)
- [Puente IoT Plug and Play en GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
