---
title: Alertas de seguridad personalizadas para IoT Hub
description: Obtenga información sobre las alertas de seguridad personalizables y la corrección recomendada mediante las características y el servicio de Defender para IoT Hub.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 3d72ee2d4cd91d0a922a7af61fbafd2cdb9a349a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319004"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Alertas de seguridad personalizadas de Defender para IoT Hub

Defender para IoT analiza continuamente la solución de IoT mediante análisis avanzados e inteligencia de amenazas para alertarle de cualquier actividad malintencionada.

Cree alertas personalizadas en función de su conocimiento del comportamiento esperado de cada dispositivo. Estas alertas actúan como los indicadores más eficaces de los posibles cambios en la implementación de la organización o en el entorno.

Las siguientes listas de alertas de Defender para IoT se pueden definir en función del comportamiento esperado de su instancia de IoT Hub. Para obtener más información acerca de cómo personalizar cada alerta, consulte la [creación de alertas personalizadas](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Alertas personalizadas integradas en la instancia de IoT Hub

### <a name="low-severity"></a>Low severity

| Nombre de la alerta | severity | Origen de datos | Descripción | Tipo de alerta |
|--|--|--|--|--|
| Alerta personalizada: el número de mensajes de la nube al dispositivo en el protocolo AMQP está fuera del intervalo permitido. | Bajo | IoT Hub | El número de mensajes de nube a dispositivo (protocolo AMQP) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_AmqpC2DMessagesNotInAllowedRange |
| Alerta personalizada: el número de mensajes de la nube al dispositivo rechazados en el protocolo AMQP está fuera del intervalo permitido. | Bajo | IoT Hub | El número de mensajes de nube a dispositivo (protocolo AMQP) rechazados por el dispositivo dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_AmqpC2DRejectedMessagesNotInAllowedRange |
| Alerta personalizada: el número de mensajes del dispositivo a la nube en el protocolo AMQP está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de mensajes de dispositivo a nube (protocolo AMQP) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_AmqpD2CMessagesNotInAllowedRange |
| Alerta personalizada: el número de invocaciones del método directo está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de invocaciones del método directo dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_DirectMethodInvokesNotInAllowedRange |
| Alerta personalizada: el número de cargas de archivos está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de cargas de archivos dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_FileUploadsNotInAllowedRange |
| Alerta personalizada: el número de mensajes de la nube al dispositivo en el protocolo HTTP está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de mensajes de la nube al dispositivo (protocolo HTTP) en un período de tiempo no está en el intervalo permitido configurado | IoT_CA_HttpC2DMessagesNotInAllowedRange |
| Alerta personalizada: el número de mensajes de la nube al dispositivo rechazados en el protocolo HTTP no se encuentra en el intervalo permitido. | Bajo | IoT Hub | La cantidad de mensajes de nube a dispositivo (protocolo HTTP) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_HttpC2DRejectedMessagesNotInAllowedRange |
| Alerta personalizada: el número de mensajes del dispositivo a la nube en el protocolo HTTP está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de mensajes de dispositivo a nube (protocolo HTTP) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_HttpD2CMessagesNotInAllowedRange |
| Alerta personalizada: el número de mensajes de la nube al dispositivo en el protocolo MQTT está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de mensajes de nube a dispositivo (protocolo MQTT) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_MqttC2DMessagesNotInAllowedRange |
| Alerta personalizada: el número de mensajes de la nube al dispositivo rechazados en el protocolo MQTT está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de mensajes de nube a dispositivo (protocolo MQTT) rechazados por el dispositivo dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_MqttC2DRejectedMessagesNotInAllowedRange |
| Alerta personalizada: el número de mensajes del dispositivo a la nube en el protocolo MQTT está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de mensajes de dispositivo a nube (protocolo MQTT) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_MqttD2CMessagesNotInAllowedRange |
| Alerta personalizada: el número de purgas de la cola de comandos que se encuentra fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de purgas de la cola de comandos dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_QueuePurgesNotInAllowedRange |
| Alerta personalizada: el número de actualizaciones de módulos gemelos está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de actualizaciones de módulos gemelos dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_TwinUpdatesNotInAllowedRange |
| Alerta personalizada: el número de operaciones sin autorizar está fuera del intervalo permitido. | Bajo | IoT Hub | La cantidad de operaciones sin autorizar dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. | IoT_CA_UnauthorizedOperationsNotInAllowedRange |

## <a name="next-steps"></a>Pasos siguientes

- Averigüe cómo puede [personalizar una alerta](quickstart-create-custom-alerts.md)
- [Información general](overview.md) del servicio Defender para IoT
