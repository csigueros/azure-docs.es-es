---
title: 'Azure Communication Services: problemas conocidos'
description: Obtenga información acerca de Azure Communication Services.
author: rinarish
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: a32b462b17a96eacb3858e7a22a27262046b8589
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491269"
---
# <a name="known-issues-in-the-sdks-and-apis"></a>Problemas conocidos en los SDK y las API

En este artículo se proporciona información sobre las limitaciones y los problemas conocidos relativos a los SDK de llamadas de Azure Communication Services y las API de automatización de llamadas de Communication Services.

> [!IMPORTANT]
> Hay varios factores que pueden afectar a la calidad de la experiencia de llamada. Para más información sobre los procedimientos recomendados para las pruebas y la configuración de red de Communication Services, vea [Recomendaciones de red](./voice-video-calling/network-requirements.md).

## <a name="javascript-sdk"></a>SDK de JavaScript

En las secciones siguientes se proporciona información sobre los problemas conocidos asociados a los SDK de llamada de voz y vídeo JavaScript de Communication Services.

### <a name="ios-with-safari-crashes-and-refreshes-the-page-if-a-user-tries-to-send-video-in-a-call"></a>iOS con Safari se bloquea y actualiza la página si un usuario intenta enviar vídeo en una llamada

iOS 15.1 introdujo un error que afecta a la mayoría de las llamadas con vídeo de Communication Services que se realizan en iOS con Safari. En concreto, el problema se produce cuando un usuario se une a una llamada de Communication Services o a una reunión en Microsoft Teams mediante Communication Services en iOS 15.1 desde cualquier explorador con el vídeo activado. Este conjunto de circunstancias provocan el bloqueo del explorador Safari.

Se trata de un [error conocido en iOS 15.1 con Safari](https://bugs.webkit.org/show_bug.cgi?id=231505). Debe evitar que los usuarios usen (activen) cualquier vídeo en iOS 15.1 con Safari para videollamadas de Communication Services. También debe evitar esta actividad para las reuniones por videollamada entre Microsoft Teams y Communication Services.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>La actualización de una página no quita al usuario inmediatamente de su llamada

Si un usuario está en una llamada y decide actualizar la página, el servicio multimedia de Communication Services no quita este usuario inmediatamente de la llamada. Espera a que el usuario vuelva a unirse. Transcurrido el tiempo de espera del servicio multimedia, se quita al usuario de la llamada.

Es mejor crear experiencias de usuario en las que no sea necesario que los usuarios finales actualicen la página de la aplicación durante una llamada. Si un usuario actualiza la página, vuelva a usar el mismo identificador de usuario de Communication Services después de que el usuario vuelva a la aplicación. Al volver a unirse con el mismo identificador de usuario, el usuario se representa como el mismo objeto existente en la colección `remoteParticipants`. Desde la perspectiva de otros participantes de la llamada, el usuario permanece en la llamada durante el tiempo que se tarda en actualizar la página, hasta un minuto o dos.

Si antes de actualizar la página, el usuario estaba enviando vídeo, la colección `videoStreams` conservará la información de la secuencia anterior hasta que se agote el tiempo de espera del servicio y la quite. En este escenario, la aplicación puede decidir mantener las nuevas secuencias agregadas a la colección y representar una con el valor de `id` más alto. 

### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>No es posible representar varias vistas previas de varios dispositivos en Web

Esta es una limitación conocida. Para más información, vea la [introducción a los SDK de llamadas](./voice-video-calling/calling-sdk-features.md).

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>No es posible enumerar dispositivos en Safari cuando la aplicación se ejecuta en iOS o iPadOS.

Las aplicaciones no pueden mostrar ni seleccionar dispositivos de micrófono o altavoz (por ejemplo, Bluetooth) en iOS con Safari o iPadOS. Se trata de una limitación conocida de estos sistemas operativos.

Si usa Safari en macOS, la aplicación no podrá mostrar ni seleccionar altavoces mediante el Administrador de dispositivos de Communication Services. En este escenario, debe seleccionar dispositivos mediante el sistema operativo. Si usa Chrome en macOS, la aplicación sí puede mostrar o seleccionar dispositivos mediante el Administrador de dispositivos de Communication Services.

### <a name="device-mutes-and-incoming-video-stops-rendering-when-certain-interruptions-occur"></a>El dispositivo se silencia y el vídeo entrante detiene la representación cuando se producen determinadas interrupciones

Este problema puede producirse si otra aplicación o el sistema operativo toma el control del micrófono o la cámara. Estos son algunos ejemplos que pueden ocurrir mientras un usuario está en la llamada:

- Una llamada entrante llega a través de la red telefónica conmutada (RTC) y captura el acceso al dispositivo de micrófono.
- Un usuario reproduce un vídeo de YouTube, por ejemplo, o inicia una llamada de FaceTime. Cambiar a otra aplicación nativa puede capturar el acceso al micrófono o la cámara.
- Un usuario habilita Siri, que capturará el acceso al micrófono.

Para recuperarse de todos estos casos, el usuario debe volver a la aplicación para reactivar el audio. En el caso del vídeo, el usuario debe iniciar el vídeo para que el audio y el vídeo se reanuden después de la interrupción.

En ocasiones, los dispositivos de micrófono o cámara no se iniciarán a tiempo, lo que puede causar problemas con la llamada original. Por ejemplo, si el usuario intenta reactivar el audio mientras ve un vídeo de YouTube, o si una llamada RTC está activa simultáneamente. 

El entorno en el que se produce este problema es el siguiente:

- Biblioteca cliente: Calling (JavaScript)
- Explorador: Safari
- Sistema operativo: iOS

### <a name="repeatedly-switching-video-devices-might-cause-video-streaming-to-stop-temporarily"></a>El cambio repetido de los dispositivos de vídeo puede provocar la detención temporal del streaming de vídeo

El cambio entre dispositivos de vídeo puede hacer que la secuencia de vídeo se pause mientras se adquiere la secuencia desde el dispositivo seleccionado. Cambiar entre dispositivos con frecuencia puede provocar una degradación del rendimiento. Lo más conveniente es que los desarrolladores detengan una secuencia del dispositivo antes de iniciar otra.

### <a name="bluetooth-headset-microphone-isnt-detected-or-audible-during-the-call-on-safari-on-ios"></a>El micrófono de los auriculares Bluetooth no se detecta o no se oye durante la llamada en iOS con Safari

Safari en IOS no admite auriculares Bluetooth. El dispositivo Bluetooth no aparecerá entre las opciones de micrófono disponibles y otros participantes no podrán oírle si intenta usar Bluetooth en Safari.

Hay una limitación conocida del sistema operativo. Con Safari en macOS y iOS/iPadOS, no es posible mostrar ni seleccionar dispositivos de altavoz mediante el Administrador de dispositivos de Communication Services. Esto se debe a que Safari no permite mostrar ni seleccionar altavoces. En este escenario, use el sistema operativo para actualizar la selección de dispositivos.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>La rotación de un dispositivo puede crear una calidad de vídeo deficiente

Cuando los usuarios rotan un dispositivo, este movimiento puede degradar la calidad del vídeo que se está transmitiendo.

El entorno en el que se produce este problema es el siguiente:

- Dispositivos afectados: Google Pixel 5, Google Pixel 3a, Apple iPad 8 y Apple iPad X
- Biblioteca cliente: Calling (JavaScript)
- Exploradores: Safari, Chrome
- Sistemas operativos: iOS, Android

### <a name="camera-switching-makes-the-screen-freeze"></a>Al cambiar de cámara, la pantalla se bloquea 

Cuando un usuario de Communication Services se une a una llamada mediante el SDK de llamada de JavaScript y, a continuación, selecciona el botón para cambiar la cámara, la interfaz de usuario puede dejar de responder. Después, el usuario debe actualizar la aplicación o ejecutar el explorador en segundo plano.

El entorno en el que se produce este problema es el siguiente:

- Dispositivos afectados: Google Pixel 4A
- Biblioteca cliente: Calling (JavaScript)
- Explorador: Chrome
- Sistemas operativos: iOS, Android

### <a name="video-signal-problem-when-the-call-is-in-connecting-state"></a>Problema de la señal de vídeo cuando la llamada está en estado Conectando 

Si un usuario activa y desactiva el vídeo rápidamente mientras la llamada está en estado *Conectando*, esto podría provocar un problema con la secuencia adquirida para la llamada. Lo más conveniente es que los desarrolladores creen sus aplicaciones de forma que no sea necesario activar y desactivar el vídeo mientras la llamada está en estado *Conectando*. El rendimiento del vídeo puede disminuir en los escenarios siguientes:

 - Si el usuario comienza con audio y, luego, inicia y detiene el vídeo mientras la llamada está en estado *Conectando*.
 - Si el usuario comienza con audio y, luego, inicia y detiene el vídeo mientras la llamada está en estado *Sala de espera*.

### <a name="enumerating-or-accessing-devices-for-safari-on-macos-and-ios"></a>Enumeración o acceso de dispositivos para Safari en macOS y iOS 

En determinados entornos, es posible que observe que los permisos del dispositivo se restablecen después de un período de tiempo. En macOS y iOS, Safari no mantiene los permisos durante mucho tiempo a menos que se adquiera una secuencia. La manera más sencilla de evitarlo es llamar a la API `DeviceManager.askDevicePermission()` antes de llamar a las API de enumeración de dispositivos del Administrador de dispositivos. Estas API de enumeración son `DeviceManager.getCameras()`, `DeviceManager.getSpeakers()` y `DeviceManager.getMicrophones()`. Si los permisos existen, el usuario no verá nada. Si los permisos no existen, estos se volverán a solicitar al usuario.

El entorno en el que se produce este problema es el siguiente:

- Dispositivo afectado: iPhone
- Biblioteca cliente: Calling (JavaScript)
- Explorador: Safari
- Sistema operativo: iOS

### <a name="delay-in-rendering-remote-participant-videos"></a>Retraso en la representación de vídeos de participantes remotos

Durante una llamada de grupo en curso, suponga que el _Usuario A_ envía el vídeo y, luego, el _Usuario B_ se une a la llamada. A veces, el usuario B no ve el vídeo del usuario A, o el vídeo del usuario A comienza a representarse después de un retraso largo. Un problema de configuración del entorno de red podría provocar este retraso. Para más información, vea [Recomendaciones de red](./voice-video-calling/network-requirements.md).

### <a name="using-third-party-libraries-during-the-call-might-result-in-audio-loss"></a>El uso de bibliotecas de terceros durante la llamada podría provocar una pérdida del audio

Si usa `getUserMedia` por separado dentro de la aplicación, se pierde la secuencia de audio. Esto se debe a que una biblioteca de terceros se encarga del acceso a los dispositivos desde la biblioteca de Azure Communication Services.

- No use bibliotecas de terceros que usen la API `getUserMedia` internamente durante la llamada.
- Si todavía necesita usar una biblioteca de terceros, la única manera de recuperar la secuencia de audio es cambiar el dispositivo seleccionado (si el usuario tiene más de uno) o reiniciar la llamada.

El entorno en el que se produce este problema es el siguiente:

- Explorador: Safari
- Sistema operativo: iOS

La causa de este problema puede ser que la adquisición de su propia secuencia desde el mismo dispositivo tendrá un efecto secundario por ejecutarse en condiciones de carrera. La adquisición de secuencias de otros dispositivos puede provocar que el usuario tenga un ancho de banda de USB o E/S insuficiente y que la tasa de `sourceUnavailableError` se dispare.  

### <a name="support-for-simulcast"></a>Compatibilidad con la difusión simultánea

La difusión simultánea es una técnica por la que un cliente codifica la misma secuencia de vídeo dos veces, en diferentes resoluciones y velocidades de bits. A continuación, el cliente deja que Communication Services decida qué secuencia debe recibir un cliente. El SDK de la biblioteca de llamadas de Communication Services para Windows, Android o iOS admite el envío de secuencias de difusión simultánea. El SDK web de Communication Services no admite actualmente el envío de secuencias de difusión simultánea.

## <a name="communication-services-call-automation-apis"></a>API de automatización de llamadas de Communication Services

Estos son los problemas conocidos de las API de automatización de llamadas de Communication Services:

- La única autenticación admitida actualmente para las aplicaciones de servidor es usar una cadena de conexión.

- Realice llamadas solo entre entidades del mismo recurso de Communication Services. La comunicación entre recursos está bloqueada.

- No se permiten las llamadas entre los usuarios inquilinos de Microsoft Teams y las entidades de aplicaciones de servidor o usuarios de Communication Services.

- Si una aplicación llama a dos o más identidades RTC y, después, sale de la llamada, se cancela la llamada entre las otras entidades RTC.

