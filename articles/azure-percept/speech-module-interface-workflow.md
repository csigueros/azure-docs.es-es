---
title: Flujo de trabajo de la interfaz del módulo de voz de Azure Percept
description: Describe el flujo de trabajo y los métodos disponibles para el módulo de voz de Azure Percept.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 7/19/2021
ms.custom: template-concept
ms.openlocfilehash: 8dec6a6c4162225b4f5e9ddf3ce74b739fd39562
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222823"
---
# <a name="azure-percept-speech-module-interface-workflow"></a>Flujo de trabajo de la interfaz del módulo de voz de Azure Percept

En este artículo, se describe cómo interactúa el módulo de voz de Azure Percept con IoT Hub. Lo hace mediante los métodos de módulo gemelo y de módulo. Además, enumera las llamadas al método directo que se usan para invocar el módulo de voz.

## <a name="speech-module-interaction-with-iot-hub-via-module-twin-and-module-method"></a>Interacción del módulo de voz con IoT Hub mediante los métodos de módulo gemelo y de módulo
- IoT Hub usa el módulo gemelo para implementar la configuración del módulo de voz y esta configuración se guarda en las propiedades. El módulo de voz puede actualizar la información del dispositivo y los datos de telemetría en IoT Hub mediante las propiedades notificadas por el módulo gemelo.
- IoT Hub puede enviar solicitudes de control al módulo de voz mediante el método de módulo.
- IoT Hub puede obtener el estado del módulo de voz mediante el método de módulo.

Para más información, consulte [Uso de módulos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).


## <a name="speech-module-states"></a>Estados del módulo de voz
- **IoTInitialized**: indica que el módulo IoT está inicializado y la red entre el módulo de voz y el módulo del centro perimetral está conectada.
- **Authenticating**: se está procesando la autenticación del dispositivo de audio de Azure.
- **Authenticated**: ha finalizado la autenticación del dispositivo de audio de Azure. Si se produce un error, el centro de IoT recibirá un mensaje de error.
- **MicDiscovering**: se empieza a enumerar la matriz de micrófonos mediante la interfaz ALSA.
- **MicDiscovered**: ha finalizado la enumeración de la matriz de micrófonos. Si se produce un error, el centro de IoT recibirá un mensaje de error.
- **SpeechConfigured**: ha finalizado la configuración de CC. Si se produce un error, el centro de IoT recibirá un mensaje de error.
- **SpeechStarted**: indica que el bot está configurado y en ejecución.
- **SpeechStopped**: indica que el bot está detenido.
- **DeviceRemoved**: indica que se ha quitado el dispositivo de audio de Azure.


## <a name="speech-bot-states"></a>Estados del bot de voz
La consulta de los estados del bot de voz solo se admite en el estado del módulo de voz **SpeechStarted**.
- **Ready**: KWS está listo y esperando la activación por voz.
- **Listening**: el bot escucha la entrada de voz.
- **Thinking**: el bot está esperando respuesta.
- **Speaking**: el bot obtiene la respuesta y habla la respuesta.

## <a name="interaction-between-iot-hub-and-the-speech-module"></a>Interacción entre IoT Hub y el módulo de voz 
En esta sección, se describe cómo interactúa IoT Hub con el módulo de voz. Como se muestra en el diagrama, hay tres tipos de mensajes.
- Implementación con las propiedades necesarias y actualización con las propiedades notificadas
- Invocación del método de módulo
- Actualización de los daos de telemetría

:::image type="content" source="media/speech-module-interface-workflow/speech-module-diagram.png" alt-text="Diagrama que muestra la interacción entre IoT Hub y el módulo de voz":::

IoT Hub invoca el método de módulo con dos parámetros:
- Nombre del método del módulo (distingue mayúsculas de minúsculas)
- Carga del método

El módulo de voz responde con:
- Un código de estado
    - **0** = inactivo
    - **102** = procesando
    - **200** = correcto
    - **202** = pendiente
    - **500** = error
    - **501** = ausente
- Una carga de estado

Este es un ejemplo de uso del método de módulo GetModuleState:
1. Invoque el método con estos parámetros:
    - Cadena: "GetModuleState"
    - Sin especificar
1. Respuesta:
    - Código de estado: 200
    - Carga: "DeviceRemoved"

## <a name="next-steps"></a>Pasos siguientes
Intente aplicar estos conceptos al [configurar una aplicación de asistente de voz con Azure IoT Hub](./how-to-configure-voice-assistant.md).