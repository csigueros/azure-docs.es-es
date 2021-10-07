---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a12f5717d927e8358a503a50f314c6b80f577952
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585354"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="record-calls"></a>Registro de llamadas
> [!WARNING]
> Hasta la versión 1.1.0 y la versión beta 1.1.0-beta.1 de la llamada de ACS, Android SDK tiene `isRecordingActive` y `addOnIsRecordingActiveChangedListener` como parte del objeto `Call`. En el caso de las versiones beta, esas API se movieron como una característica extendida de `Call`, tal como se describe a continuación.

> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión "beta" del Android SDK de llamada de ACS.

La grabación de llamadas es una característica extendida de la API `Call` principal. Primero debe obtener el objeto de API de la característica de grabación:

```java
RecordingCallFeature callRecordingFeature = call.api(Features.RECORDING);
```

Después, para comprobar si se está grabando la llamada, inspeccione la propiedad `isRecordingActive` de `callRecordingFeature`. Devuelve `boolean`.

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

También puede suscribirse a los cambios de grabación:

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);
```

Si desea iniciar la grabación desde la aplicación, siga los pasos para configurar la grabación de llamadas que aparecen en [Introducción a la grabación de llamadas](../../../../concepts/voice-video-calling/call-recording.md).

Una vez configurada la grabación de llamadas en el servidor, desde la aplicación de Android debe obtener el valor `ServerCallId` de la llamada y, luego, enviarlo al servidor para iniciar el proceso de grabación. Puede encontrar el valor `ServerCallId` si usa `getServerCallId()` de la clase `CallInfo`, que se puede encontrar en el objeto de clase con `getInfo()`.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

Cuando se inicia la grabación desde el servidor, se desencadenará el evento `handleCallOnIsRecordingChanged` y el valor de `callRecordingFeature.isRecordingActive()` será `true`.

Al igual que al comenzar a grabar la llamada, si desea detener esta grabación, deberá obtener `ServerCallId` y enviarlo al servidor de grabación para que pueda detener la grabación de llamadas.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

Cuando se detiene la grabación desde el servidor, se desencadenará el evento `handleCallOnIsRecordingChanged` y el valor de `callRecordingFeature.isRecordingActive()` será `false`.
