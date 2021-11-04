---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: eur
ms.openlocfilehash: f863a7a8b88cd97f0ceef0dd3a711c357202acd1
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131519972"
---
Para configurar el SDK de Voz para que acepte entradas de audio comprimidas, cree `PullAudioInputStream` o `PushAudioInputStream`. A continuación, cree un objeto `AudioConfig` a partir de una instancia de la clase de secuencia, especificando el formato de compresión de la secuencia.  Busque código de ejemplo relacionado en [Ejemplos del SDK de Voz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

Supongamos que tiene una clase de flujo de entrada llamada `pushStream` y que usa OPUS/OGG. El aspecto del código sería el siguiente:

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```
