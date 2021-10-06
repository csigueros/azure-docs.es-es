---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: d2011720b203493b5b17fabb3d65ca912e7ec37d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585037"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!WARNING]
> Hasta la versión 1.1.0 y la versión beta 1.1.0-beta.1 de la llamada de ACS, el SDK de iOS tiene `isTranscriptionActive` como parte del objeto `Call` y `didChangeTranscriptionState` es parte del delegado `CallDelegate`. En el caso de las versiones beta, esas API se movieron como una característica extendida de `Call`, tal como se describe a continuación.
> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión "beta" del SDK de iOS de llamada de ACS.

La transcripción de llamadas es una característica extendida de la API `Call` principal. Primero debe obtener el objeto de API de la característica de transcripción:

```swift
let callTranscriptionFeature = call.api(Features.transcription)
```

Luego, a fin de comprobar si se transcribe la llamada, revise la propiedad `isTranscriptionActive` de `callTranscriptionFeature`. Devuelve `Bool`.

```swift
let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive;
```

También puede suscribirse a los cambios de transcripción mediante la implementación del delegado `TranscriptionCallFeatureDelegate` en la clase con el evento `didChangeTranscriptionState`:

```swift
callTranscriptionFeature.delegate = self

// didChangeTranscriptionState is a member of TranscriptionCallFeatureDelegate
public func transcriptionCallFeature(_ transcriptionCallFeature: TranscriptionCallFeature, didChangeTranscriptionState args: PropertyChangedEventArgs) {
    let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive
}
```
