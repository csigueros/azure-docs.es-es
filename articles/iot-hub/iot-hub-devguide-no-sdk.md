---
title: Desarrollo sin un SDK de Azure IoT | Microsoft Docs
description: 'Guía del desarrollador: Información y vínculos a los temas que puede usar para compilar aplicaciones de back-end y de aplicaciones de dispositivo sin usar el SDK de Azure IoT.'
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 34476ee99dcf455886a72d3fcef2356ab4e6229f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721926"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Desarrollo sin usar un SDK de Azure IoT Hub

En este tema se proporciona información útil y vínculos para desarrolladores que quieren desarrollar aplicaciones de dispositivo o de back-end sin usar los SDK de Azure IoT.

Microsoft recomienda encarecidamente usar un SDK de Azure IoT. Los SDK de dispositivo y de servicio de Azure IoT se publican en muchas plataformas populares. Los SDK proporcionan una capa de conveniencia que controla gran parte de la complejidad del protocolo de comunicación subyacente, como la conexión y reconexión del dispositivo, y la directiva de reintentos. Los SDK se actualizan periódicamente para proporcionar las características más recientes que expone IoT Hub, así como actualizaciones de seguridad. El uso de los SDK puede ayudarle a reducir el tiempo de desarrollo y el tiempo que dedica al mantenimiento del código. Para más información acerca de los SDK de Azure IoT, consulte el artículo sobre los [SDK de dispositivo y servicio de Azure IoT](iot-hub-devguide-sdks.md). Para obtener más información sobre las ventajas de usar un SDK de Azure IoT, consulte la entrada de blog sobre las [ventajas de usar los SDK de Azure IoT y las dificultades a evitar si no se usan](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

Aunque IoT Hub admite AMQP, AMQP sobre WebSockets, HTTPS, MQTT y MQTT sobre WebSockets para la comunicación con dispositivos, se recomienda usar MQTT si el dispositivo lo admite.

## <a name="development-prerequisites"></a>Requisitos previos para el desarrollo

Antes de iniciar el desarrollo, debe tener un conocimiento exhaustivo de IoT Hub y de las características que quiere que implemente el dispositivo o la aplicación de back-end. Esta es una lista muy resumida de los temas con los que debería familiarizarse:

* Asegúrese de que comprende los puntos de conexión que expone IoT Hub y los protocolos que se admiten en cada uno de estos. Para más información, consulte [Puntos de conexión de IoT Hub](iot-hub-devguide-endpoints.md).

* Si existe una elección de protocolo implicada para las aplicaciones de dispositivo, se recomienda encarecidamente usar MQTT. Sin embargo, antes de elegir un protocolo, asegúrese de que comprende las limitaciones que impone cada uno. Para más información, consulte [Elección de un protocolo de comunicación](iot-hub-devguide-protocols.md).

* Para entender la autenticación con IoT Hub, consulte [Control del acceso a IoT Hub](iot-hub-devguide-security.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Ayuda sobre los distintos protocolos

Para obtener ayuda sobre el uso de los siguientes protocolos sin un SDK de Azure IoT:

* Para aplicaciones de dispositivo o de back-end en **AMQP**, consulte [Compatibilidad de AMQP](iot-hub-amqp-support.md).

* Para aplicaciones de dispositivo en **MQTT**, consulte [Compatibilidad de MQTT](iot-hub-mqtt-support.md). En la mayor parte de este tema se trata el uso del protocolo MQTT directamente. También contiene información sobre el uso del [repositorio de ejemplo de MQTT para IoT](https://github.com/Azure-Samples/IoTMQTTSample). Este repositorio contiene ejemplos de C que usan la biblioteca Eclipse Mosquitto para enviar mensajes a IoT Hub.

* Para las aplicaciones de dispositivo o de back-end en **HTTPS**, consulte las [API de REST de Azure IoT Hub](/rest/api/iothub/). Tenga en cuenta que, como se indica en [Requisitos previos para el desarrollo](#development-prerequisites), no puede usar la autenticación de la entidad de certificación (CA) X.509 con HTTPS.

En el caso de los dispositivos, se recomienda encarecidamente usar MQTT si el dispositivo lo admite.

## <a name="next-steps"></a>Pasos siguientes

* [Soporte para MQTT](iot-hub-mqtt-support.md)