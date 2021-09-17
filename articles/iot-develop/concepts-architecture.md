---
title: Arquitectura de IoT Plug and Play | Microsoft Docs
description: Como creador de soluciones, comprenda los elementos clave arquitectónicos de IoT Plug and Play.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 94376e1bc5192a0aa437066cb8a460e9aecd27a6
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607425"
---
# <a name="iot-plug-and-play-architecture"></a>Arquitectura de IoT Plug and Play

IoT Plug and Play permite a los creadores de soluciones integrar dispositivos IoT en sus soluciones sin necesidad de configuración manual. IoT Plug and Play se basa en un _modelo_ de dispositivo que describe las funcionalidades del dispositivo para una aplicación compatible con IoT Plug and Play. Este modelo se estructura como un conjunto de interfaces que definen:

- _Propiedades_ que representan el estado de solo lectura y grabable de un dispositivo o de otra entidad. Por ejemplo, el número de serie de un dispositivo puede ser una propiedad de solo lectura, y la temperatura objetivo de un termostato puede ser una propiedad grabable.
- _Datos de telemetría_, que son los datos que emite un dispositivo, independientemente de que sean una secuencia normal de lecturas de un sensor, un error ocasional o un mensaje informativo.
- _Comandos_ que describen una función u operación que se puede realizar en un dispositivo. Por ejemplo, un comando puede reiniciar una puerta de enlace o tomar una imagen mediante una cámara remota.

Cada modelo e interfaz tiene un id. único.

El diagrama siguiente muestra los elementos clave de una solución IoT Plug and Play:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="Arquitectura de IoT Plug and Play":::

## <a name="model-repository"></a>Repositorio de modelos

El [repositorio de modelos](./concepts-model-repository.md) es un almacén de definiciones de interfaz y modelo. Los modelos e interfaces se definen con el [Lenguaje de definición de Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl).

La interfaz de usuario web le permite administrar los modelos y las interfaces.

El repositorio modelo tiene controles de acceso basados en roles integrados que permiten limitar el acceso a las definiciones de interfaz.

## <a name="devices"></a>Dispositivos

Un creador de dispositivos implementa el código para que se ejecute en un dispositivo IoT mediante uno de los [SDK de dispositivo IoT de Azure](./libraries-sdks.md). Los SDK de dispositivo ayudan al creador de dispositivos a:

- Conectarse de forma segura a una instancia de IoT Hub.
- Registrar el dispositivo en su centro de IoT y anunciar el identificador de modelo que determina la colección de interfaces de DTDL que el dispositivo implementa.
- Sincronizar las propiedades definidas en las interfaces de DTDL entre el dispositivo y el centro de IoT.
- Agregar controladores de comandos para los comandos definidos en las interfaces de DTDL.
- Enviar telemetría al centro de IoT.

## <a name="iot-edge-gateway"></a>Puerta de enlace de IoT Edge

Una puerta de enlace de IoT Edge actúa como intermediario para conectar los dispositivos IoT Plug and Play que no se pueden conectar directamente a un centro de IoT. Para más información, consulte [Uso de un dispositivo IoT Edge como puerta de enlace](../iot-edge/iot-edge-as-gateway.md).

## <a name="iot-edge-modules"></a>Módulos de IoT Edge

Un _módulo de IoT Edge_ le permite implementar y administrar la lógica de negocios en el perímetro. Los módulos de Azure IoT Edge son la unidad más pequeña de cálculo que administra IoT Edge y pueden contener servicios de Azure (por ejemplo, Azure Stream Analytics) o su propio código específico de la solución.

El _centro de IoT Edge_ es uno de los dos módulos que componen el entorno de ejecución de Azure IoT Edge. Actúa como un proxy local de IoT Hub exponiendo los mismos puntos de conexión de protocolo que IoT Hub. Esta coherencia significa que los clientes (dispositivos o módulos) pueden conectarse a la instancia de IoT Edge en tiempo de ejecución de la misma forma que lo harían a IoT Hub.

Los SDK de dispositivo ayudan a un generador de módulos a:

- Usar el centro de IoT Edge para conectarse a su centro de IoT de forma segura.
- Registrar el módulo en su centro de IoT y anunciar el identificador de modelo que determina la colección de interfaces de DTDL que el dispositivo implementa.
- Sincronizar las propiedades definidas en las interfaces de DTDL entre el dispositivo y el centro de IoT.
- Agregar controladores de comandos para los comandos definidos en las interfaces de DTDL.
- Enviar telemetría al centro de IoT.

## <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) es un servicio hospedado en la nube que actúa como centro de mensajes para comunicaciones bidireccionales entre la solución IoT y los dispositivos que administra.

Un centro de IoT:

- Hace que el id. de modelo implementado por un dispositivo esté disponible para una solución de back-end.
- Mantiene el gemelo digital asociado a cada dispositivo Plug and Play conectado al centro.
- Reenvía transmisiones de telemetría a otros servicios para su procesamiento o almacenamiento.
- Enruta eventos de cambio de gemelo digital a otros servicios para permitir la supervisión de dispositivos.

## <a name="backend-solution"></a>Solución de back-end

Una solución de back-end supervisa y controla los dispositivos conectados mediante la interacción con gemelos digitales en el centro de IoT. Use uno de los SDK de servicio para implementar la solución de back-end. Para comprender las capacidades de un dispositivo conectado, el back-end de la solución:

1. Recupera el id. de modelo que el dispositivo registró en el centro de IoT.
1. Usa el id. de modelo para recuperar las definiciones de interfaz de cualquier repositorio del modelo.
1. Utiliza el analizador de modelos para extraer información de las definiciones de interfaz.

La solución de back-end puede usar la información de las definiciones de interfaz para:

- Leer los valores de propiedad informados por los dispositivos.
- Actualizar las propiedades grabables en un dispositivo.
- Llamar a comandos implementados por un dispositivo.
- Comprender el formato de la telemetría enviada por un dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una visión general de la arquitectura de una solución IoT Plug and Play, los pasos siguientes sirven para obtener más información acerca de:

- [Repositorio de modelos](./concepts-model-repository.md)
- [Integración de modelos de gemelos digitales](./concepts-model-discovery.md)
- [Desarrollo para IoT Plug and Play](./concepts-developer-guide-device.md)
