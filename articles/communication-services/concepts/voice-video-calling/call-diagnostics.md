---
title: Diagnóstico de llamadas de Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Aquí se proporciona una introducción a la característica de diagnóstico de llamadas.
author: probableprime
ms.author: rifox
manager: chpalm
services: azure-communication-services
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 23307fbfccdce2faa1b6c5808095f0f97c7a0e66
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635895"
---
# <a name="call-diagnostics"></a>Diagnóstico de llamada

Al trabajar con llamadas en Azure Communication Services, pueden surgir problemas que generen molestias a los clientes. Para ayudar con esto hay una característica denominada "Diagnóstico de llamadas" que permite examinar distintas propiedades de una llamada para determinar cuál puede ser el problema.

**El diagnóstico de llamadas de momento solo se admite en el SDK web o JS.**

## <a name="accessing-diagnostics"></a>Acceso a los diagnósticos

El diagnóstico de llamada es una características extendida de la API `Call` principal que le permite diagnosticar una llamada activa.

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

## <a name="diagnostic-values"></a>Valores de diagnóstico

Están disponibles los diagnósticos siguientes para los usuarios:

### <a name="network-values"></a>Valores de red

| Nombre                      | Descripción                                                     | Valores posibles                                                                                                                                                                                                                                  | Casos de uso                                           |
| ------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| noNetwork                 | No hay red disponible.                                  | - Se establece en `True` cuando no se puede iniciar una llamada porque no hay red disponible. <br/> - Se establece en `False` cuando hay candidatos ICE.                                                                                                  | El dispositivo no está conectado a una red.               |
| networkRelaysNotReachable | Problemas con una red.                                        | - Se establece en `True` cuando la red tiene alguna restricción que no le permite acceder a las retransmisiones de ACS. <br/> - Se establece en `False` al hacer una llamada nueva.                                                                                                | Durante una llamada cuando la señal WiFi es intermitente. |
| networkReconnect          | Se perdió la conexión y nos estamos reconectando a la red. | - Se establece en `Poor` cuando se pierde la conectividad del transporte de medios. <br/> - Se establece en `Bad` cuando se desconecta la red. <br/> - Se establece en `Good` cuando se desconecta una sesión nueva.                                                                       | Ancho de banda reducido, sin Internet                          |
| networkReceiveQuality     | Indicador relacionado con la calidad de la secuencia entrante.                 | - Se establece en `Bad` cuando hay un problema grave al recibir la secuencia. calidad <br/> - Se establece en `Poor` cuando hay un problema leve al recibir la secuencia. calidad <br/> - Se establece en `Good` cuando no hay ningún problema al recibir la secuencia. | Ancho de banda bajo                                       |

### <a name="audio-values"></a>Valores de audio

| Nombre                           | Descripción                                                     | Valores posibles                                                                                                                                                                                                                                                                                                   | Casos de uso                                                        |
| ------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| noSpeakerDevicesEnumerated     | No hay ningún dispositivo de salida de audio (altavoz) en el sistema del usuario. | - Se establece en `True` cuando no hay ningún dispositivo de altavoz en el sistema y se admite la selección del altavoz. <br/> - Se establece en `False` cuando hay al menos 1 dispositivo de altavoz en el sistema y se admite la selección del altavoz.                                                                                             | Todos los altavoces están desconectados                                       |
| speakingWhileMicrophoneIsMuted | El usuario habla mientras está silenciado.                                   | - Se establece en `True` cuando el micrófono local está silenciado y el usuario local está hablando. <br/> - Se establece en `False` cuando el usuario local deja de hablar o activa el sonido del micrófono. <br/> \* Nota: A día de hoy, esto todavía no se admite en Safari, porque los ejemplos de nivel de audio se toman de webrtc. stats.                 | Durante una llamada, habla mientras tiene silenciado el micrófono.           |
| noMicrophoneDevicesEnumerated  | No hay dispositivos de captura de audio (micrófono) en el sistema del usuario.      | - Se establece en `True` cuando no hay dispositivos de micrófono en el sistema. <br/> - Se establece en `False` cuando hay al menos 1 dispositivo de micrófono en el sistema.                                                                                                                                                              | Todos los micrófonos se desconectan durante la llamada.                   |
| microphoneNotFunctioning       | El micrófono no funciona.                                  | - Se establece en `True` cuando no se puede iniciar el envío de la secuencia de audio local porque es posible que el dispositivo de micrófono esté deshabilitado en el sistema o si lo usa otro proceso. Este UFD tarda unos 10 segundos en activarse. <br/> - Se establece en `False` cuando el micrófono empieza a enviar nuevamente la secuencia de audio de manera correcta. | No hay micrófonos disponibles, el acceso al micrófono está deshabilitado en el sistema. |
| microphoneMuteUnexpectedly     | Se silenció el micrófono.                                             | - Se establece en `True` cuando el micrófono se silencia de manera inesperada. <br/> - Se establece en `False` cuando el micrófono empieza a enviar la secuencia de audio correctamente.                                                                                                                                                                  | El micrófono está silenciado desde el sistema.                             |
| microphonePermissionDenied     | El volumen del dispositivo está bajo o casi sin sonido en macOS. | - Se establece en `True`cuando la configuración del sistema (audio) deniega el permiso de audio. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br/> Nota: Este diagnóstico solo funciona en macOS.                                                                                                                             | Los permisos de micrófono están deshabilitados en Configuración.             |

### <a name="camera-values"></a>Valores de cámara

| Nombre                   | Descripción                                            | Valores posibles                                                                                                                                                                                                                                                                                              | Casos de uso                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| cameraFreeze           | La cámara deja de generar fotogramas durante más de 5 segundos. | - Se establece en `True` cuando se detiene la secuencia de vídeo local. Esto significa que el lado remoto ve el vídeo inmovilizado en su pantalla o que los participantes remotos no ven su vídeo en pantalla. <br/> - Se establece en `False` cuando finaliza la inmovilización y los usuarios pueden ver el vídeo de manera normal. | La cámara paró durante la llamada o una red insuficiente hizo que se inmovilizara. |
| cameraStartFailed      | Error genérico de la cámara.                                | - Se establece en `True` cuando no se puede iniciar el envío de vídeo local porque es posible que el dispositivo de cámara esté deshabilitado en el sistema o si lo usa otro proceso. <br/> - Se establece en `False` cuando el dispositivo de cámara seleccionado envía vídeo local correctamente. .                                                  | Errores de la cámara                                                                 |
| cameraStartTimedOut    | Escenario común en el que la cámara está en mal estado.          | - Se establece en `True` cuando se agotó el tiempo de espera del dispositivo de cámara para empezar a enviar la secuencia de vídeo. <br/> - Se establece en `False` cuando el dispositivo de cámara seleccionado vuelve a enviar vídeo local correctamente.                                                                                                                                         | Errores de la cámara                                                                 |
| cameraPermissionDenied | Se denegaron los permisos de cámara en la configuración.            | - Se establece en `True`cuando la configuración del sistema (video) deniega el permiso de cámara. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br> Nota: Este diagnóstico solo funciona en macOS.Chrome.                                                                                                                  | Los permisos de cámara están deshabilitados en Configuración.                                |

### <a name="misc-values"></a>Otros valores

| Nombre                         | Descripción                                                  | Valores posibles                                                                                                                                                                                         | Casos de uso                                 |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| screenshareRecordingDisabled | Las preferencias en Configuración deniegan el uso compartido de la pantalla del sistema. | - Se establece en `True`cuando la configuración del sistema (uso compartido) deniega el permiso para el uso compartido de la pantalla. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br/> Nota: Este diagnóstico solo funciona en macOS.Chrome. | La grabación de pantalla está deshabilitada en Configuración. |

## <a name="diagnostic-events"></a>Eventos de diagnóstico

- Suscríbase al evento `diagnosticChanged` para supervisar cuándo cambia cualquier diagnóstico de llamada.

```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` +
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

## <a name="get-the-latest-diagnostics"></a>Obtención del diagnóstico más reciente

- Obtenga los valores de diagnóstico de llamada más reciente generados. Si no se definió un diagnóstico es porque nunca se generó.

```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();

console.log(
  `noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`
);

console.log(
  `networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`
);

console.log(
  `networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`
);

const latestMediaDiagnostics = callDiagnostics.media.getLatest();

console.log(
  `speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`
);

console.log(
  `cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`
);

console.log(
  `microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`
);
```
