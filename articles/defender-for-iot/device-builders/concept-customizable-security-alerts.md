---
title: Alertas de seguridad personalizadas para IoT Hub
description: Obtenga información sobre las alertas de seguridad personalizables y la corrección recomendada mediante las características y el servicio de Defender para IoT Hub.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: d7a58bcdb759c3f31290cc7930eba6ca52fcc17b
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018290"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Alertas de seguridad personalizadas de Defender para IoT Hub

Defender para IoT analiza continuamente la solución de IoT mediante análisis avanzados e inteligencia de amenazas para alertarle de cualquier actividad malintencionada.

Se recomienda crear alertas personalizadas en función de su conocimiento sobre el comportamiento esperado del dispositivo. De este modo, se asegura de que las alertas actúan como indicadores eficaces del riesgo potencial en la implementación e infraestructura únicos de la organización.

Las siguientes listas de alertas de Defender para IoT se pueden definir en función del comportamiento esperado de su instancia de IoT Hub. Para obtener más información acerca de cómo personalizar cada alerta, consulte la [creación de alertas personalizadas](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Alertas personalizadas integradas en la instancia de IoT Hub

| severity | Nombre de la alerta | Origen de datos | Descripción | Corrección sugerida |
|--|--|--|--|--|
| Bajo | Alerta personalizada: el número de mensajes de la nube al dispositivo en el protocolo AMQP está fuera del intervalo permitido. | IoT Hub | El número de mensajes de nube a dispositivo (protocolo AMQP) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |  |
| Bajo | Alerta personalizada: el número de mensajes de la nube al dispositivo rechazados en el protocolo AMQP está fuera del intervalo permitido. | IoT Hub | El número de mensajes de nube a dispositivo (protocolo AMQP) rechazados por el dispositivo dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |  |
| Bajo | Alerta personalizada: el número de mensajes del dispositivo a la nube en el protocolo AMQP está fuera del intervalo permitido. | IoT Hub | La cantidad de mensajes de dispositivo a nube (protocolo AMQP) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |  |
| Bajo | Alerta personalizada: el número de invocaciones del método directo está fuera del intervalo permitido. | IoT Hub | La cantidad de invocaciones del método directo dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |  |
| Bajo | Alerta personalizada: el número de cargas de archivos está fuera del intervalo permitido. | IoT Hub | La cantidad de cargas de archivos dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |  |
| Bajo | Alerta personalizada: el número de mensajes de la nube al dispositivo en el protocolo HTTP está fuera del intervalo permitido. | IoT Hub | La cantidad de mensajes de la nube al dispositivo (protocolo HTTP) en un período de tiempo no está en el intervalo permitido configurado |
| Bajo | Alerta personalizada: el número de mensajes de la nube al dispositivo rechazados en el protocolo HTTP no se encuentra en el intervalo permitido. | IoT Hub | La cantidad de mensajes de nube a dispositivo (protocolo HTTP) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |
| Bajo | Alerta personalizada: el número de mensajes del dispositivo a la nube en el protocolo HTTP está fuera del intervalo permitido. | IoT Hub | La cantidad de mensajes de dispositivo a nube (protocolo HTTP) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |  |
| Bajo | Alerta personalizada: el número de mensajes de la nube al dispositivo en el protocolo MQTT está fuera del intervalo permitido. | IoT Hub | La cantidad de mensajes de nube a dispositivo (protocolo MQTT) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |  |
| Bajo | Alerta personalizada: el número de mensajes de la nube al dispositivo rechazados en el protocolo MQTT está fuera del intervalo permitido. | IoT Hub | La cantidad de mensajes de nube a dispositivo (protocolo MQTT) rechazados por el dispositivo dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |
| Bajo | Alerta personalizada: el número de mensajes del dispositivo a la nube en el protocolo MQTT está fuera del intervalo permitido. | IoT Hub | La cantidad de mensajes de dispositivo a nube (protocolo MQTT) dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |
| Bajo | Alerta personalizada: el número de purgas de la cola de comandos que se encuentra fuera del intervalo permitido. | IoT Hub | La cantidad de purgas de la cola de comandos dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |  |
| Bajo | Alerta personalizada: el número de actualizaciones de módulos gemelos está fuera del intervalo permitido. | IoT Hub | La cantidad de actualizaciones de módulos gemelos dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |
| Bajo | Alerta personalizada: el número de operaciones sin autorizar está fuera del intervalo permitido. | IoT Hub | La cantidad de operaciones sin autorizar dentro de un período de tiempo específico está fuera del intervalo configurado y permitido actualmente. |

## <a name="next-steps"></a>Pasos siguientes

- Averigüe cómo puede [personalizar una alerta](quickstart-create-custom-alerts.md)
- [Información general](overview.md) del servicio Defender para IoT
- Aprenda a [acceder a los datos de seguridad](how-to-security-data-access.md)
- Más información sobre cómo [investigar un dispositivo](how-to-investigate-device.md)
