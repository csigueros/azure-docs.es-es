---
title: Opciones de nube a dispositivo de IoT Hub de Azure | Microsoft Docs
description: 'Guía del desarrollador: una guía sobre cuándo usar métodos directos, propiedades notificadas del dispositivo gemelo o mensajes de nube a dispositivo para comunicaciones de este mismo tipo.'
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 105374dc214ad77978973a5e7288ac1dd487941b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728798"
---
# <a name="cloud-to-device-communications-guidance"></a>Guía de comunicación de nube a dispositivo

IoT Hub proporciona tres opciones para aplicaciones de dispositivo que exponen funcionalidades a una aplicación de back-end:

* [Métodos directos](iot-hub-devguide-direct-methods.md), para las comunicaciones que requieren confirmación inmediata del resultado. Los métodos directos se utilizan frecuentemente para el control interactivo de dispositivos, como la activación de un ventilador.

* [Propiedades deseadas del dispositivo gemelo](iot-hub-devguide-device-twins.md), para comandos de ejecución prolongada destinados a poner el dispositivo en un determinado estado deseado. Por ejemplo, establecer el intervalo de envío de telemetría en 30 minutos.

* [Mensajes de nube a dispositivo](iot-hub-devguide-messages-c2d.md), para notificaciones unidireccionales a la aplicación de dispositivo.

Para obtener información sobre cómo usa [Azure IoT Plug and Play](../iot-develop/overview-iot-plug-and-play.md) estas opciones para controlar los dispositivos IoT Plug and Play, consulte [Guía para desarrolladores de dispositivos IoT Plug and Play](../iot-develop/concepts-developer-guide-service.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Esta es una comparación detallada de las distintas opciones de comunicación de nube a dispositivo.

| Categorías | Métodos directos | Propiedades deseadas del dispositivo gemelo | Mensajes de nube a dispositivo |
| ---------- | -------------- | ------------------------- | ------------------------ |
| Escenario | Comandos que necesitan confirmación inmediata, por ejemplo, encender un ventilador. | Comandos de ejecución prolongada destinados a poner el dispositivo en un determinado estado deseado. Por ejemplo, establecer el intervalo de envío de telemetría en 30 minutos. | Notificaciones unidireccionales a la aplicación de dispositivo. |
| flujo de datos | Bidireccional. La aplicación de dispositivo puede responder al método inmediatamente. El back-end de solución recibe el resultado contextualmente a la solicitud. | Unidireccional. La aplicación de dispositivo recibe una notificación con el cambio de propiedad. | Unidireccional. La aplicación de dispositivo recibe el mensaje.
| Durabilidad. | No se establece contacto con los dispositivos desconectados. Se notifica al back-end de la solución que el dispositivo no está conectado. | Se conservan los valores de propiedad en el dispositivo gemelo. El dispositivo los leerá en la siguiente reconexión. Los valores de propiedad son recuperables con el [lenguaje de consulta de IoT Hub](iot-hub-devguide-query-language.md). | IoT Hub puede conservar los mensajes durante 48 horas como máximo. |
| Destinos | Un único dispositivo que usa **deviceId**, o varios dispositivos que usan [trabajos](iot-hub-devguide-jobs.md). | Un único dispositivo que usa **deviceId**, o varios dispositivos que usan [trabajos](iot-hub-devguide-jobs.md). | Dispositivo único por **deviceId**. |
| Size | El tamaño de carga máximo del método directo es de 128 KB. | El tamaño máximo de las propiedades deseadas es 32 KB. | Mensajes de hasta 64 KB. |
| Frecuencia | Alta. Para más información, consulte los [Límites de IoT Hub](iot-hub-devguide-quotas-throttling.md). | Mediana. Para más información, consulte los [Límites de IoT Hub](iot-hub-devguide-quotas-throttling.md). | Baja. Para más información, consulte los [Límites de IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protocolo | Disponible con MQTT o AMQP. | Disponible con MQTT o AMQP. | Disponible en todos los protocolos. El dispositivo debe sondear al usar HTTPS. |

Aprenda a usar métodos directos, propiedades deseadas y mensajes de nube a dispositivo en los siguientes tutoriales:

* [Uso de métodos directos](quickstart-control-device.md)
* [Uso de las propiedades deseadas para configurar dispositivos](tutorial-device-twins.md) 
* [Envío de mensajes de nube a dispositivo](iot-hub-node-node-c2d.md)
