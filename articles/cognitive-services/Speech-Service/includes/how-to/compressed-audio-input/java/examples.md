---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: lajanuar
ms.openlocfilehash: ae0a08d0e860777ccc6ff716bef8305f7d7f1748
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122264388"
---
Para configurar el SDK de Voz para que acepte entradas de audio comprimidas, cree `PullAudioInputStream` o `PushAudioInputStream`. A continuación, cree un objeto `AudioConfig` a partir de una instancia de la clase de secuencia, especificando el formato de compresión de la secuencia. Busque código de ejemplo relacionado en [Ejemplos del SDK de Voz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/WavStream.java).

Supongamos que tiene una clase de flujo de entrada llamada `pullStream` y que usa OPUS/OGG. El aspecto del código sería el siguiente:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

// ... omitted for brevity

SpeechConfig speechConfig =
    SpeechConfig.fromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = 
    AudioStreamFormat.getCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig =
    AudioConfig.fromStreamInput(
        pullStream,
        audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();

String text = result.getText();
```
