---
title: 'Azure Communication Services: procedimientos recomendados'
description: Obtenga más información sobre los procedimientos recomendados de Azure Communication Services.
author: srahaman
manager: akania
services: azure-communication-services
ms.author: srahaman
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 47d690b53b6e8fe9ccc2660e48283ce05e262d30
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154192"
---
# <a name="best-practices-azure-communication-services-calling-sdks"></a>Procedimientos recomendados: SDK de llamada de Azure Communication Services
En este artículo se proporciona información sobre los procedimientos recomendados relativos a los SDK de llamada de Azure Communication Services (ACS).

## <a name="acs-web-javascript-sdk-best-practices"></a>Procedimientos recomendados sobre los SDK web de JavaScript para ACS
En esta sección se proporciona información sobre los procedimientos recomendados asociados a los SDK de llamada de voz y vídeo de JavaScript de Azure Communication Services.

## <a name="javascript-voice-and-video-calling-sdk"></a>SDK de llamada de voz y vídeo de JavaScript

### <a name="plug-in-microphone-or-enable-microphone-from-device-manager-when-acs-call-in-progress"></a>Conexión o activación del micrófono desde el administrador de dispositivos cuando la llamada de ACS está en curso
Cuando no hay ningún micrófono disponible al inicio de una llamada y, a continuación, un micrófono está disponible, se genera el evento de diagnóstico de llamada "noMicrophoneDevicesEnumerated".
Cuando esto sucede, la aplicación debe invocar `askDevicePermission` para obtener el consentimiento del usuario para enumerar los dispositivos. A continuación, el usuario podrá silenciar o reactivar el micrófono.

### <a name="stop-video-on-page-hide"></a>Detención del vídeo en la ocultación de páginas
Cuando el usuario sale de la pestaña de llamada, el streaming de vídeo se detiene. Algunos dispositivos siguen transmitiendo el último fotograma. Para evitar este problema, se recomienda a los desarrolladores que detengan el streaming de vídeo cuando los usuarios abandonen una llamada activa con vídeo habilitado. El vídeo se puede detener mediante una llamada a la API `call.stopVideo`.
```JavaScript
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState === 'visible') {
        // Start Video if it was stopped on visibility change (flag true)
    } else {
        // Stop Video if it's on and set flag = true to keep track
    }
});
```

### <a name="dispose-video-stream-renderer-view"></a>Eliminación de la vista del representador de secuencias de vídeo
Las aplicaciones de Communication Services deben eliminar `VideoStreamRendererView` o su instancia de `VideoStreamRenderer` principal cuando ya no sean necesarios.

### <a name="hang-up-the-call-on-onbeforeunload-event"></a>Colgar la llamada con un evento onbeforeunload
La aplicación debe invocar `call.hangup` cuando se emite el evento `onbeforeunload`.

### <a name="handling-multiple-calls-on-multiple-tabs-on-mobile"></a>Control de varias llamadas en varias pestañas en dispositivos móviles
La aplicación no debe conectarse a llamadas desde varias pestañas del explorador simultáneamente, ya que esto puede provocar un comportamiento indefinido debido a la asignación de recursos para el micrófono y la cámara en el dispositivo. Se recomienda a los desarrolladores que siempre cuelguen las llamadas cuando se completen en segundo plano antes de iniciar una nueva.
```JavaScript 
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState != 'visible') {
            // call.hangUp
    }
});
 ```

### <a name="handle-os-muting-call-when-phone-call-comes-in"></a>Controle el silenciamiento del sistema operativo cuando se reciba una llamada de teléfono.
Mientras se realiza una llamada de ACS (para iOS y Android), si se recibe una llamada de teléfono en el sistema operativo, se silenciará automáticamente el micrófono y la cámara de los usuarios. En Android, el audio de la llamada se reactiva automáticamente y el vídeo se reinicia una vez que finaliza la llamada telefónica. En iOS, requiere la intervención del usuario para "reactivar el audio" e "iniciar el vídeo" de nuevo. Puede escuchar la notificación de que el micrófono se ha silenciado inesperadamente con el evento de calidad de `microphoneMuteUnexpectedly`. Tenga en cuenta que para poder volver a unirse correctamente a una llamada, deberá usar el SDK 1.2.2-beta.1 o superior.
```JavaScript
const latestMediaDiagnostic = call.api(SDK.Features.Diagnostics).media.getLatest();
const isIosSafari = (getOS() === OSName.ios) && (getPlatformName() === BrowserName.safari);
if (isIosSafari && latestMediaDiagnostic.microphoneMuteUnexpectedly && latestMediaDiagnostic.microphoneMuteUnexpectedly.value) {
  // received a QualityEvent on iOS that the microphone was unexpectedly muted - notify user to unmute their microphone and to start their video stream
}
 ```
La aplicación debe invocar `call.startVideo(localVideoStream);` para iniciar una secuencia de vídeo y debe usar `this.currentCall.unmute();` para reactivar el audio.

### <a name="device-management"></a>Administración de dispositivos
Puede usar el SDK de Azure Communication Services para administrar los dispositivos y las operaciones multimedia.
- La aplicación no debe usar API nativas del explorador como `getUserMedia` o `getDisplayMedia` para adquirir secuencias fuera del SDK. Si lo hace, tendrá que eliminar manualmente las secuencias multimedia antes de usar `DeviceManager` u otras API de administración de dispositivos mediante el SDK de Communication Services.

### <a name="request-device-permissions"></a>Solicitud de permisos de dispositivo
Puede solicitar permisos de dispositivo mediante el SDK:
- La aplicación debe usar `DeviceManager.askDevicePermission` para solicitar acceso a dispositivos de audio o vídeo.
- Si el usuario deniega el acceso, `DeviceManager.askDevicePermission` devolverá "false" para un tipo de dispositivo determinado (audio o vídeo) en las llamadas posteriores, incluso después de actualizar la página. En este escenario, la aplicación debe detectar que el usuario denegó previamente el acceso e indicar al usuario que restablezca manualmente o conceda explícitamente acceso a un tipo de dispositivo determinado.

## <a name="next-steps"></a>Pasos siguientes
Para más información, consulte los siguientes artículos.

- [Incorporación de chat a una aplicación](../quickstarts/chat/get-started.md)
- [Adición de la llamada de voz a una aplicación](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [Documentación de referencia](reference.md)
