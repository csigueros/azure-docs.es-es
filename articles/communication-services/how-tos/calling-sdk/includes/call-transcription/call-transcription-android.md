---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: b880f8a8f84247c14a0f1e81577d9e252b2b78c8
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585208"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

> [!WARNING]
> Hasta la versión 1.1.0 y la versión beta 1.1.0-beta.1 de la llamada de ACS, Android SDK tiene `isTranscriptionActive` y `addOnIsTranscriptionActiveChangedListener` como parte del objeto `Call`. En el caso de las versiones beta, esas API se movieron como una característica extendida de `Call`, tal como se describe a continuación.

> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión "beta" del Android SDK de llamada de ACS.

La transcripción de llamadas es una característica extendida de la API `Call` principal. Primero debe obtener el objeto de API de la característica de transcripción:

```java
TranscriptionCallFeature callTranscriptionFeature = call.api(Features.TRANSCRIPTION);
```

Luego, a fin de comprobar si se está transcribiendo la llamada, revise la propiedad `isTranscriptionActive` de `callTranscriptionFeature`. Devuelve `boolean`.

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

También puede suscribirse a los cambios en la transcripción:

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);
```
