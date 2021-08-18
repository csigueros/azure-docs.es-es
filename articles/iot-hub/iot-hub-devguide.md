---
title: Guía del desarrollador de IoT Hub de Azure | Microsoft Docs
description: La Guía del desarrollador de IoT Hub de Azure incluye explicaciones sobre los puntos de conexión, la seguridad, el registro de identidad, la administración de dispositivos, los métodos directos, los dispositivos gemelos, las cargas de archivos, los trabajos, el lenguaje de consultas de IoT Hub y la mensajería.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: cecb59c065a223f433fc1f4c516a67e42af559c4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728770"
---
# <a name="azure-iot-hub-developer-guide"></a>Guía del desarrollador de Azure IoT Hub

IoT Hub de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una solución de back-end.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub de Azure le proporciona:

* Comunicaciones seguras a través del uso de credenciales de seguridad para cada dispositivo y el control de acceso.

* Varias opciones de comunicación a gran escala de dispositivo a la nube y de la nube a dispositivo.

* Almacenamiento consultable de la información de estado y los metadatos de cada dispositivo.

* Conectividad fácil del dispositivo con las bibliotecas de dispositivos para las plataformas y lenguajes más populares.

Esta guía del desarrollador de IoT Hub incluye los siguientes artículos:

* [Guía de comunicación de dispositivo a nube](iot-hub-devguide-d2c-guidance.md): con este artículo le resultará más fácil elegir entre mensajes del dispositivo a la nube, propiedades notificadas del dispositivo gemelo y carga de archivos.

* [Guía de comunicación de nube a dispositivo](iot-hub-devguide-c2d-guidance.md): con este artículo le resultará más fácil elegir entre métodos directos, propiedades preferidas del dispositivo gemelo y mensajes de la nube al dispositivo.

* [Mensajería de dispositivo a nube y de nube a dispositivo con IoT Hub](iot-hub-devguide-messaging.md): describe las características de mensajería (de dispositivo a nube y de nube a dispositivo) que expone IoT Hub.

  * [Envío de mensajes de dispositivos a la nube a IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Lectura de mensajes de dispositivos a la nube desde el punto de conexión integrado](iot-hub-devguide-messages-read-builtin.md).

  * [Uso de puntos de conexión personalizados y reglas de enrutamiento para mensajes del dispositivo a la nube](iot-hub-devguide-messages-read-custom.md).

  * [Envío de mensajes de nube a dispositivo desde IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Creación y lectura de mensajes de IoT Hub](iot-hub-devguide-messages-construct.md).

* [Carga de archivos desde un dispositivo](iot-hub-devguide-file-upload.md): describe cómo se cargan archivos desde un dispositivo. El artículo también incluye información acerca de temas como las notificaciones que el proceso de carga puede enviar.

* En [Administrar identidades del dispositivo en IoT Hub](iot-hub-devguide-identity-registry.md) se describe qué información almacena el registro de identidades de cada instancia de IoT Hub. En el artículo también se describe cómo puede tener acceso y modificarlo.

* En [Control de acceso a IoT Hub](iot-hub-devguide-security.md) se describe el modelo de seguridad que se usa para conceder acceso a las funciones de IoT Hub tanto para los dispositivos como para los componentes de la nube. El artículo incluye información acerca del uso de tokens y certificados X.509, y los detalles de los permisos que puede conceder.

* En [Uso de dispositivos gemelos para sincronizar el estado y las configuraciones](iot-hub-devguide-device-twins.md) se describe el concepto de *dispositivo gemelo*. En el artículo también se describe la funcionalidad que los dispositivos gemelos exponen, como la sincronización de un dispositivo con su dispositivo gemelo. El artículo incluye información acerca de los datos almacenados en un dispositivo gemelo.

* En [Invocación de un método directo en un dispositivo](iot-hub-devguide-direct-methods.md) se describe el ciclo de vida de un método directo. En el artículo se describe cómo invocar métodos en un dispositivo desde la aplicación de back-end y cómo controlar el método directo en el dispositivo.

* En [Programación de trabajos en varios dispositivos](iot-hub-devguide-jobs.md) se describe cómo se programan trabajos en varios dispositivos. En el artículo se describe cómo enviar trabajos que realizan tareas como la ejecución de un método directo y la actualización de un dispositivo mediante un dispositivo gemelo. También describe cómo consultar el estado de un trabajo.

* [Referencia: elección de un protocolo de comunicación](iot-hub-devguide-protocols.md): describe los protocolos de comunicación que IoT Hub admite para la comunicación de dispositivos y muestra los puertos que deben estar abiertos.

* [Referencia: Puntos de conexión de IoT Hub](iot-hub-devguide-endpoints.md): describe los diferentes puntos de conexión que expone cada centro de IoT para las operaciones en tiempo de ejecución y de administración. En el artículo también se describe cómo puede crear puntos de conexión adicionales en su IoT Hub y cómo usar una puerta de enlace de campo para habilitar la conectividad con los puntos de conexión de su IoT Hub en escenarios no estándar.

* [Referencia: lenguaje de consulta para dispositivos gemelos, trabajos y enrutamiento de mensajes](iot-hub-devguide-query-language.md): describe el lenguaje de consulta de IoT Hub que le permite recuperar información desde su instancia de IoT Hub sobre los dispositivos gemelos y los trabajos.

* En [Referencia: cuotas y limitaciones](iot-hub-devguide-quotas-throttling.md) se resumen las cuotas establecidas en el servicio de IoT Hub y la limitación que se produce cuando supera una cuota.

* [Referencia: precios](iot-hub-devguide-pricing.md): proporciona información general sobre diferentes SKU y precios para IoT Hub y detalles sobre cómo las distintas funciones se miden como mensajes en IoT Hub.

* En [Referencia: SDK de servicio y de dispositivo](iot-hub-devguide-sdks.md) se muestran los diversos SDK de Azure IoT para desarrollar aplicaciones de dispositivo y de servicio que interactúen con su IoT Hub. El artículo incluye vínculos a documentación de la API en línea.

* [Referencia: compatibilidad con MQTT de IoT Hub](iot-hub-mqtt-support.md): proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT. En el artículo se describe la compatibilidad con el protocolo MQTT integrado en los SDK de IoT de Azure y se proporciona información acerca de cómo utilizar el protocolo MQTT directamente.
