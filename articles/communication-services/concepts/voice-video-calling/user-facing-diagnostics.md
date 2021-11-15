---
title: Diagnósticos orientados al usuario de Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Se proporciona información general sobre la característica de diagnósticos orientados al usuario.
author: probableprime
ms.author: rifox
manager: chpalm
services: azure-communication-services
ms.date: 10/21/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: ca0a60b1944dcbf037ecee0b012822a819bdb730
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269960"
---
# <a name="user-facing-diagnostics"></a>Diagnósticos orientados al usuario

Al trabajar con llamadas en Azure Communication Services, pueden surgir problemas que provocan incidencias para los clientes. Para ayudar con este escenario, hay una característica denominada "diagnósticos orientados al usuario" que puede usar para examinar varias propiedades de una llamada y determinar cuál podría ser el problema.

> [!NOTE]
> Actualmente, los diagnósticos orientados al usuario solo se admiten en nuestro SDK web o JavaScript.

## <a name="accessing-diagnostics"></a>Acceso a los diagnósticos

Los diagnósticos orientados al usuario son una característica extendida de la API `Call` principal que le permiten diagnosticar una llamada activa.

```js
const userFacingDiagnostics = call.api(Features.UserFacingDiagnostics);
```

## <a name="diagnostic-values"></a>Valores de diagnóstico

Están disponibles los siguientes diagnósticos orientados al usuario:

### <a name="network-values"></a>Valores de red

| Nombre                      | Descripción                                                     | Valores posibles                                                                                                                                                                                                                                  | Casos de uso                                           |
| ------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| noNetwork                 | No hay red disponible.                                  | - Se establece en `True` cuando no se puede iniciar una llamada porque no hay red disponible. <br/> - Se establece en `False` cuando hay candidatos ICE.                                                                                                  | El dispositivo no está conectado a una red.               |
| networkRelaysNotReachable | Problemas con una red.                                        | - Se establece en `True` cuando la red tiene alguna restricción que no le permite acceder a las retransmisiones de ACS. <br/> - Se establece en `False` al hacer una llamada nueva.                                                                                                | Durante una llamada cuando la señal WiFi es intermitente. |
| networkReconnect          | Se perdió la conexión y nos estamos reconectando a la red. | - Se establece en `Bad` cuando se desconecta la red. <br/> - Se establece en `Poor` cuando se pierde la conectividad del transporte de medios. <br/> - Se establece en `Good` cuando se desconecta una sesión nueva.                                                                       | Ancho de banda reducido, sin Internet                          |
| networkReceiveQuality     | Indicador relacionado con la calidad de la secuencia entrante.                 | - Se establece en `Bad` cuando hay un problema grave al recibir la secuencia.  <br/> - Se establece en `Poor` cuando hay un problema leve al recibir la secuencia. <br/> - Se establece en `Good` cuando no hay ningún problema al recibir la secuencia. | Ancho de banda bajo                                       |    
|   networkSendQuality     | Indicador relativo a la calidad del flujo saliente.                 | - Se establece en `Bad` cuando hay un problema grave al enviar la secuencia. <br/> - Se establece en `Poor` cuando hay un problema de gravedad media al enviar la secuencia. <br/> - Se establece en `Good` cuando no hay ningún problema con el envío de la secuencia. | Ancho de banda bajo                                       |

### <a name="audio-values"></a>Valores de audio

| Nombre                           | Descripción                                                     | Valores posibles                                                                                                                                                                                                                                                                                                   | Casos de uso                                                        |
| ------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| noSpeakerDevicesEnumerated     | No hay ningún dispositivo de salida de audio (altavoz) en el sistema del usuario. | - Se establece en `True` cuando no hay ningún dispositivo de altavoz en el sistema y se admite la selección del altavoz. <br/> - Se establece en `False` cuando hay al menos 1 dispositivo de altavoz en el sistema y se admite la selección del altavoz.                                                                                             | Todos los altavoces están desconectados                                       |
| speakingWhileMicrophoneIsMuted | El usuario habla mientras está silenciado.                                   | - Se establece en `True` cuando el micrófono local está silenciado y el usuario local está hablando. <br/> - Se establece en `False` cuando el usuario local deja de hablar o activa el sonido del micrófono. <br/> \* Nota: Actualmente, esta opción no se admite en Safari porque las muestras de nivel de audio se toman de las estadísticas de WebRTC.                 | Durante una llamada, habla mientras tiene silenciado el micrófono.           |
| noMicrophoneDevicesEnumerated  | No hay dispositivos de captura de audio (micrófono) en el sistema del usuario.      | - Se establece en `True` cuando no hay dispositivos de micrófono en el sistema. <br/> - Se establece en `False` cuando hay al menos 1 dispositivo de micrófono en el sistema.                                                                                                                                                              | Todos los micrófonos se desconectan durante la llamada.                   |
| microphoneNotFunctioning       | El micrófono no funciona.                                  | - Se establece en `True` cuando no se puede iniciar el envío de la secuencia de audio local porque es posible que el dispositivo de micrófono esté deshabilitado en el sistema o si lo usa otro proceso. Este UFD tarda unos 10 segundos en activarse. <br/> - Se establece en `False` cuando el micrófono empieza a enviar nuevamente la secuencia de audio de manera correcta. | No hay micrófonos disponibles, el acceso al micrófono está deshabilitado en el sistema. |
| microphoneMuteUnexpectedly     | Se silenció el micrófono.                                             | - Se establece en `True` cuando el micrófono se silencia de manera inesperada. <br/> - Se establece en `False` cuando el micrófono empieza a enviar la secuencia de audio correctamente.                                                                                                                                                                  | El micrófono está silenciado desde el sistema.                             |
| microphonePermissionDenied     | El volumen del dispositivo está bajo o casi sin sonido en macOS. | - Se establece en `True`cuando la configuración del sistema (audio) deniega el permiso de audio. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br/> Nota: Este diagnóstico solo funciona en macOS.                                                                                                                             | Los permisos de micrófono están deshabilitados en Configuración.             |

### <a name="camera-values"></a>Valores de cámara

| Nombre                   | Descripción                                            | Valores posibles                                                                                                                                                                                                                                                                                              | Casos de uso                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| cameraFreeze           | La cámara deja de generar fotogramas durante más de 5 segundos. | - Se establece en `True` cuando se detiene la secuencia de vídeo local. Esto significa que el lado remoto ve el vídeo inmovilizado en su pantalla o que los participantes remotos no ven su vídeo en pantalla. <br/> - Se establece en `False` cuando finaliza la inmovilización y los usuarios pueden ver el vídeo de manera normal. | La cámara paró durante la llamada o una red insuficiente hizo que se inmovilizara. |
| cameraStartFailed      | Error genérico de la cámara.                                | - Se establece en `True` cuando no se puede iniciar el envío de vídeo local porque es posible que el dispositivo de cámara esté deshabilitado en el sistema o si lo usa otro proceso. <br/> - Se establece en `False` cuando el dispositivo de cámara seleccionado vuelve a enviar vídeo local correctamente.                                                  | Errores de la cámara                                                                 |
| cameraStartTimedOut    | Escenario común en el que la cámara está en mal estado.          | - Se establece en `True` cuando se agotó el tiempo de espera del dispositivo de cámara para empezar a enviar la secuencia de vídeo. <br/> - Se establece en `False` cuando el dispositivo de cámara seleccionado vuelve a enviar vídeo local correctamente.                                                                                                                                         | Errores de la cámara                                                                 |
| cameraPermissionDenied | Se denegaron los permisos de cámara en la configuración.            | - Se establece en `True`cuando la configuración del sistema (video) deniega el permiso de cámara. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br> Nota: Este diagnóstico solo funciona en macOS.Chrome.                                                                                                                  | Los permisos de cámara están deshabilitados en Configuración.                                |
| cameraStoppedUnexpectedly | Funcionamiento incorrecto de la cámara             | - Se establece en `True` cuando la cámara entra en estado detenido inesperadamente. <br/> - Se establece en `False` cuando la cámara empieza a enviar nuevamente la secuencia de vídeo de manera correcta.    | Comprobar que la cámara funciona correctamente     |

### <a name="misc-values"></a>Otros valores

| Nombre                         | Descripción                                                  | Valores posibles                                                                                                                                                                                         | Casos de uso                                 |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| screenshareRecordingDisabled | Las preferencias en Configuración deniegan el uso compartido de la pantalla del sistema. | - Se establece en `True`cuando la configuración del sistema (uso compartido) deniega el permiso para el uso compartido de la pantalla. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br/> Nota: Este diagnóstico solo funciona en macOS.Chrome. | La grabación de pantalla está deshabilitada en Configuración. |
| capturerStartFailed | Error al compartir la pantalla del sistema. | - Se establece en `True` cuando no se puede iniciar la captura de la pantalla. <br/> - Se establece en `False` cuando la captura de la pantalla se puede iniciar correctamente. |  |
| capturerStoppedUnexpectedly | Funcionamiento incorrecto del uso compartido de la pantalla del sistema             | - Se establece en `True` cuando el capturador de pantalla entra en estado detenido inesperadamente. <br/> - Se establece en `False` cuando el capturador de pantalla reinicia la operación de captura correctamente.    | Comprobar que el uso compartido de la pantalla funciona correctamente     |


## <a name="user-facing-diagnostic-events"></a>Eventos de diagnóstico orientados al usuario

- Suscríbase al evento `diagnosticChanged` para supervisar cuándo cambia cualquier diagnóstico orientado al usuario.

```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` +
        `Value type: ${diagnosticInfo.valueType}`);

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

userFacingDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
userFacingDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

## <a name="get-the-latest-user-facing-diagnostics"></a>Obtención de los últimos diagnósticos orientados al usuario

- Obtenga los últimos valores de diagnóstico que se generaron. Si no se definió un diagnóstico es porque nunca se generó.

```js
const latestNetworkDiagnostics = userFacingDiagnostics.network.getLatest();

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

const latestMediaDiagnostics = userFacingDiagnostics.media.getLatest();

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
