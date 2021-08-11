---
title: Solución de problemas del módulo de audio y Azure Percept Audio
description: Obtenga sugerencias para la solución de problemas de Azure Percept Audio y azureearspeechclientmodule.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c02c93acd23b71f73d5e27d8914d08fb1a1f99f9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110071405"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Solución de problemas de los módulos de audio y voz de Azure Percept

Siga las instrucciones que se indican a continuación para solucionar problemas de la aplicación del asistente para voz.

## <a name="understanding-ear-som-led-indicators"></a>Descripción de los indicadores LED del módulo de sistema de escucha

Puede usar indicadores LED para comprender en qué estado se encuentra el dispositivo. El dispositivo tarda entre 4 y 5 minutos en encenderse y el módulo en inicializarse por completo. A medida que recorra los pasos de inicialización, verá:

1. LED blanco en el centro encendido (estático): el dispositivo está encendido.
1. LED blanco en el centro encendido (parpadeando): la autenticación está en curso.
1. LED blanco central encendido (estático): el dispositivo está autenticado pero la palabra clave no está configurada.
1. Los tres LED cambiarán a azul una vez que se haya implementado una demostración y el dispositivo esté listo para utilizarse.

Para obtener más referencia, consulte este artículo sobre el [botón Azure Percept Audio y el comportamiento del LED](./audio-button-led-behavior.md).

### <a name="troubleshooting-led-issues"></a>Solución de problemas de LED
- **Si el LED central es blanco fijo**, pruebe a [utilizar una plantilla para crear un asistente para voz](./tutorial-no-code-speech.md).
- **Si el LED central siempre parpadea**, existe un problema de autenticación. Pruebe con estos pasos para la solución de problemas:
    - Asegúrese de que las conexiones USB-A y micro USB están protegidas. 
    - Compruebe si el [módulo de voz se está ejecutando](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module).
    - Reinicie el dispositivo.
    - [Recopile registros](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs) y asócielos a una solicitud de soporte técnico.
    - Compruebe si el kit de desarrollo ejecuta el software más reciente y aplique una actualización si está disponible.

## <a name="checking-runtime-status-of-the-speech-module"></a>Comprobación del estado del entorno de ejecución del módulo de voz

Compruebe si el estado del entorno de ejecución de **azureearspeechclientmodule** aparece como **En ejecución**. Para buscar el estado del entorno de ejecución de los módulos del dispositivo, abra [Azure Portal](https://portal.azure.com/) y vaya a **Todos los recursos** ->  **[su centro de IoT]**  -> **IoT Edge** ->  **[su id. de dispositivo]** . Haga clic en la pestaña **Módulos** para ver el estado del entorno de ejecución de todos los módulos instalados.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Página del dispositivo Edge en Azure Portal.":::

Si el estado del entorno de ejecución de **azureearspeechclientmodule** no aparece como **en ejecución**, haga clic en **Establecer módulos** -> **azureearspeechclientmodule**. En la página **Configuración del módulo**, establezca **Estado deseado** como **En ejecución** y haga clic en **Actualizar**.

## <a name="collecting-speech-module-logs"></a>Recopilación de registros del módulo de voz

Para ejecutar estos comandos, [use SSH en el kit de desarrollo](./how-to-ssh-into-percept-dk.md) y escriba los comandos en el símbolo del sistema del cliente de SSH.

Recopile los registros del módulo de voz:

```console
sudo iotedge logs azureearspeechclientmodule
```

Para redirigir la salida a un archivo .txt para su posterior análisis, use la sintaxis siguiente:

```console
sudo [command] > [file name].txt
```

Cambie los permisos del archivo .txt para que se pueda copiar:

```console
sudo chmod 666 [file name].txt
```

Después de redirigir la salida a un archivo .txt, copie el archivo en el equipo host a través de SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[ruta de acceso del archivo de host local] hace referencia a la ubicación del equipo host en el que desea copiar el archivo .txt. [nombre de usuario remoto] es el nombre de usuario de SSH elegido durante la [experiencia de instalación](./quickstart-percept-dk-set-up.md).

## <a name="known-issues"></a>Problemas conocidos
- Si usa una evaluación gratuita, el modelo de voz puede superar el plan de precios de la evaluación gratuita. En este caso, el modelo dejará de funcionar sin un mensaje de error.
- Si se conectan más de 5 dispositivos IoT Edge, el informe (el texto enviado por telemetría a IoT Hub y Speech Studio) puede bloquearse.
- Si el dispositivo se encuentra en una región diferente de los recursos, el mensaje del informe puede retrasarse. 

## <a name="useful-links"></a>Vínculos útiles
- [Instalación de Azure Percept Audio](./quickstart-percept-audio-setup.md)
- [Botón y comportamiento de los indicadores LED de Azure Percept Audio](./audio-button-led-behavior.md)
- [Creación de un asistente para voz con Azure Percept DK y Azure Percept Audio](./tutorial-no-code-speech.md)
- [Guía general de solución de problemas de Azure Percept DK](./troubleshoot-dev-kit.md)
