---
title: Estados de los botones e indicadores LED de Azure Percept Audio
description: Obtenga más información sobre el botón y los estados de los indicadores LED de Azure Percept Audio.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 04ee60d6ebe9c84d77ea7e5ead140f7930b8cfe4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224562"
---
# <a name="azure-percept-audio-button-and-led-states"></a>Estados de los botones e indicadores LED de Azure Percept Audio

Consulte la siguiente guía para obtener información sobre el botón y los estados de los indicadores LED del dispositivo de Azure Percept Audio.

## <a name="button-behavior"></a>Comportamiento del botón

Use los botones para controlar el comportamiento del dispositivo.

|Estado del botón|Comportamiento|
|------------|----------|
|Silencio|Presione para desactivar o activar el sonido de la matriz de micrófonos. Al pulsar el botón, el evento de botón se desactiva o activa.|
|PTT/PTS|Presione PTT para omitir el estado de detección de palabras clave y activar el estado de escucha de comandos. Vuelva a presionar esta tecla para detener el diálogo activo del agente y volver al estado de detección de palabras clave. Al pulsar el botón, el evento de botón se desactiva o activa. PTS solo funciona cuando se presiona el botón mientras el agente está hablando, no cuando el agente está escuchando o pensando.|

## <a name="led-states"></a>Estados del indicador LED

Use los indicadores LED para comprender en qué estado se encuentra el dispositivo.

|LED|Estado del indicador LED|Estado del módulo de sistema de escucha|
|---|------------|----------------|
|L02|1 blanco, estático activado|Encendido |
|L02|1 blanco, intermitencia de 0,5 Hz|Autenticación en curso |
|L01 & L02 & L03|3 azules, estático activado|Esperando palabra clave|
|L01 & L02 & L03|Intermitencia de la matriz LED, 20 fps |Escuchando o hablando|
|L01 & L02 & L03|Parpadeo rápido de la matriz LED, 20 fps|Pensando|
|L01 & L02 & L03|3 rojos, estático activado |Silencio|

## <a name="understanding-ear-som-led-indicators"></a>Descripción de los indicadores LED del módulo de sistema de escucha
Puede usar indicadores LED para comprender en qué estado se encuentra el dispositivo. El dispositivo tarda entre 4 y 5 minutos en encenderse y el módulo en inicializarse por completo. A medida que recorra los pasos de inicialización, verá:

1. LED blanco en el centro encendido (estático): el dispositivo está encendido.
1. LED blanco en el centro encendido (parpadeando): la autenticación está en curso.
1. LED blanco central encendido (estático): el dispositivo está autenticado pero la palabra clave no está configurada.
1. Los tres LED cambiarán a azul una vez que se haya implementado una demostración y el dispositivo esté listo para utilizarse.


## <a name="troubleshooting-led-issues"></a>Solución de problemas de LED
- **Si el LED central es blanco fijo**, pruebe a [utilizar una plantilla para crear un asistente para voz](./tutorial-no-code-speech.md).
- **Si el LED central siempre parpadea**, existe un problema de autenticación. Pruebe con estos pasos para la solución de problemas:
    1. Asegúrese de que las conexiones USB-A y micro USB están protegidas. 
    1. Compruebe si el [módulo de voz se está ejecutando](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module).
    1. Reinicie el dispositivo.
    1. [Recopile registros](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs) y asócielos a una solicitud de soporte técnico.
    1. Compruebe si el kit de desarrollo ejecuta el software más reciente y aplique una actualización si está disponible.

## <a name="next-steps"></a>Pasos siguientes

Para obtener sugerencias de solución de problemas para el dispositivo de Azure Percept Audio, consulte esta [guía](./troubleshoot-audio-accessory-speech-module.md).