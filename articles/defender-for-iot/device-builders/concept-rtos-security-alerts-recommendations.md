---
title: Alertas y recomendaciones personalizables e integradas del microagente de Defender para IoT para Azure RTOS
description: 'Obtenga información sobre las alertas de seguridad y la corrección recomendada mediante el microagente de Defender para IoT de Azure IoT: RTOS.'
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: cfbd411617a0b80f4857e08f9803b34b80b873d4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018274"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Microagente de Defender para IoT para alertas y recomendaciones de seguridad de Azure RTOS (versión preliminar)

El microagente de Defender para IoT para Azure RTOS analiza continuamente su solución de IoT mediante el análisis avanzado y la inteligencia de amenazas para alertarle de posibles actividades malintencionadas y modificaciones sospechosas en el sistema. Además, puede crear alertas personalizadas según su conocimiento del comportamiento esperado del dispositivo y las líneas de base.

Una alerta del microagente de Defender para IoT para Azure RTOS sirve como indicador de un posible peligro, y debe investigarse y corregirse. Una recomendación del microagente de Defender para IoT para Azure RTOS identifica la posición de seguridad débil que se va a corregir y actualizar. 

En este artículo, encontrará una lista de alertas y recomendaciones integradas que se desencadenan en función de los intervalos predeterminados y se pueden personalizar con sus propios valores, según el comportamiento esperado o de línea de base. 

Para obtener más información sobre cómo funciona la personalización de alertas en el servicio de Defender para IoT, consulte [Alertas personalizables](concept-customizable-security-alerts.md). Las alertas y recomendaciones específicas disponibles para la personalización al usar el microagente de Defender para IoT para Azure RTOS se detallan en las tablas siguientes. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Alertas de seguridad admitidas con el microagente de Defender para IoT para Azure RTOS

### <a name="device-related-security-alerts"></a>Alertas de seguridad relacionadas con dispositivos

|Actividad de alertas de seguridad relacionadas con dispositivos  |Nombre de la alerta  |
|---------|---------|
|IP address (Dirección IP)| Se ha detectado una comunicación con una dirección IP sospechosa.|
|Huella digital de certificado de dispositivo x.509|Error de concordancia de huella digital de certificado de dispositivo x.509|
|Certificado X.509| X.509 certificate expired (Certificado X.509 expirado)|
|Token de SAS| Expired SAS Token (Token de SAS expirado)|
|Token de SAS| Firma de token de SAS no válida|

### <a name="iot-hub-related-security-alerts"></a>Alertas de seguridad relacionadas con IoT Hub

|Actividad de alertas de seguridad de IoT Hub  |Nombre de la alerta  |
|---------|---------|
|Incorporación de un certificado    |  Se ha detectado un intento erróneo de agregar un certificado a una instancia de IoT Hub.       |
|Adición o edición de una configuración de diagnóstico    | Se ha detectado un intento de agregar o editar una configuración de diagnóstico de una instancia de IoT Hub.      |
|Eliminación de un certificado    |  Se ha detectado un intento erróneo de eliminar un certificado de una instancia de IoT Hub.       |
|Eliminación de la configuración de diagnóstico    |  Se ha detectado un intento de eliminar una configuración de diagnóstico de una instancia de IoT Hub.      |
|Certificado eliminado    | Se detectó la eliminación de un certificado de IoT Hub.        |
|Nuevo certificado     |  Se ha detectado la adición de un nuevo certificado a IoT Hub.       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Alertas personalizables admitidas en el microagente de Defender para IoT para Azure RTOS

### <a name="device-related-customizable-alerts"></a>Alertas personalizables relacionadas con el dispositivo

|Actividad relacionada con el dispositivo |Nombre de la alerta  |
|---------|---------|
|Conexiones activas|El número de conexiones activas no está en el intervalo permitido.|
|Mensajes de nube a dispositivo en el protocolo **MQTT**.|El número de mensajes de la nube al dispositivo en el protocolo **MQTT** no se encuentra en el intervalo permitido.|
|Conexión saliente| Conexión saliente a una dirección IP que no está permitida.|

### <a name="hub-related-customizable-alerts"></a>Alertas personalizables relacionadas con el concentrador 

|Actividad relacionada con el concentrador  |Nombre de la alerta  |
|---------|---------|
|Purgas de la cola de comandos     |  El número de purgas de la cola de comandos está fuera del intervalo permitido.       |
|Mensajes de nube a dispositivo en el protocolo **MQTT**.    |  El número de mensajes de la nube al dispositivo en el protocolo **MQTT** está fuera del intervalo permitido.       |
|Mensajes de dispositivo a nube en el protocolo **MQTT**.    | El número de mensajes del dispositivo a la nube en el protocolo **MQTT** está fuera del intervalo permitido.        |
|Invocaciones de método directo     |  El número de invocaciones del método directo está fuera del intervalo permitido.       |
|Mensajes de nube a dispositivo rechazados en el protocolo **MQTT**.     |   El número de mensajes de la nube al dispositivo rechazados en el protocolo **MQTT** está fuera del intervalo permitido.      |
|Actualizaciones de los módulos gemelos     |  Número de actualizaciones de los módulos gemelos fuera del intervalo permitido       |
|Operaciones no autorizadas    |  El número de operaciones sin autorizar está fuera del intervalo permitido.       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Recomendaciones admitidas con el microagente de Defender para IoT para Azure RTOS

### <a name="device-related-recommendations"></a>Recomendaciones relacionadas con dispositivos

|Actividad relacionada con dispositivos  |Nombre de la recomendación |
|---------|---------|
|Credenciales de autenticación    |  Credenciales de autenticación idénticas utilizadas por varios dispositivos       |

### <a name="hub-related-recommendations"></a>Recomendaciones relacionadas con el concentrador

|Actividad relacionada con IoT Hub  |Nombre de la recomendación |
|---------|---------|
|Directiva de filtro de IP   |  La directiva del filtro IP predeterminada debe ser **Denegar**.  |
|Reglas del filtro IP| La regla del filtro IP incluye un amplio intervalo de IP.|
|Registros de diagnóstico|Sugerencia para habilitar registros de diagnóstico en IoT Hub|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>Todas las alertas y recomendaciones de Defender para IoT

Para obtener una lista completa de todas las recomendaciones y alertas relacionadas con el servicio Defender para IoT, consulte las [alertas de seguridad](concept-security-alerts.md) de IoT y las [recomendaciones](concept-recommendations.md) de seguridad de IoT.

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido: microagente de Defender para IoT para Azure RTOS](quickstart-azure-rtos-security-module.md)
- [Configuración y personalización del microagente de Defender para IoT para Azure RTOS](how-to-azure-rtos-security-module.md)
- Consulte la [API del microagente de Defender para IoT para Azure RTOS](azure-rtos-security-module-api.md).