---
title: Solución de problemas del módulo de audio y Azure Percept Audio
description: Obtenga sugerencias para la solución de problemas de Azure Percept Audio y azureearspeechclientmodule.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: ded0fe7150fae280bd2d2e33bc314c1cb8add106
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122072493"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Solución de problemas de los módulos de audio y voz de Azure Percept

Siga las instrucciones que se indican a continuación para solucionar problemas de la aplicación del asistente para voz.

## <a name="checking-runtime-status-of-the-speech-module"></a>Comprobación del estado del entorno de ejecución del módulo de voz

Compruebe si el estado del entorno de ejecución de **azureearspeechclientmodule** aparece como **En ejecución**. Para buscar el estado del entorno de ejecución de los módulos del dispositivo, abra [Azure Portal](https://portal.azure.com/) y vaya a **Todos los recursos** ->  **[su centro de IoT]**  -> **IoT Edge** ->  **[su id. de dispositivo]** . Seleccione la pestaña **Módulos** para ver el estado del entorno de ejecución de todos los módulos instalados.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Página del dispositivo Edge en Azure Portal.":::

Si el estado del entorno de ejecución de **azureearspeechclientmodule** no aparece como **en ejecución**, seleccione **Establecer módulos** -> **azureearspeechclientmodule**. En la página **Configuración del módulo**, establezca **Estado deseado** en **En ejecución** y seleccione **Actualizar**.

## <a name="voice-assistant-application-doesnt-load"></a>No se carga la aplicación del asistente para voz
Pruebe a [implementar una de las plantillas del asistente para voz](./tutorial-no-code-speech.md). La implementación de una plantilla garantiza que se crean todos los recursos auxiliares necesarios para las aplicaciones del asistente para voz.

## <a name="voice-assistant-template-doesnt-get-created"></a>La plantilla del asistente para voz no se crea
Un error al crear una plantilla de asistente para voz suele deberse un problema con uno de los recursos auxiliares.
1. [Elimine todos los recursos del asistente para voz creados anteriormente](./delete-voice-assistant-application.md).
1. Implemente una nueva [plantilla del asistente para voz](./tutorial-no-code-speech.md).

## <a name="voice-assistant-was-created-but-doesnt-respond-to-commands"></a>Se ha creado el asistente para voz pero no responde a los comandos
Siga las instrucciones de la [guía de solución de problemas y comportamiento del LED](audio-button-led-behavior.md) para solucionar este problema.

## <a name="voice-assistant-doesnt-respond-to-custom-keywords-created-in-speech-studio"></a>El asistente para voz no responde a las palabras claves personalizadas creadas en Speech Studio
Esto puede ocurrir si el módulo de voz no está actualizado. Siga estos pasos para actualizar el módulo de voz a la versión más reciente:

1. Seleccione **Devices** (Dispositivos) en el panel de menú de la izquierda de la página principal de Azure Percept Studio.
1. Busque y seleccione el dispositivo.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Captura de pantalla de la lista de dispositivos en Azure Percept Studio.":::
1. En la ventana del dispositivo, seleccione la pestaña **Speech** (Voz).
1. Compruebe la versión del módulo de voz. Si hay una actualización disponible, verá un botón **Update** (Actualizar) junto al número de versión.
1. Seleccione **Update** (Actualizar) para implementar la actualización del módulo de voz. Por lo general, el proceso de actualización tarda 2-3 minutos en completarse.

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
- [Procedimiento para volver a una aplicación de asistente para voz creada previamente](return-to-voice-assistant-application-window.md)
