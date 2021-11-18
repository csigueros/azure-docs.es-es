---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 09/17/2021
ms.author: amishu
ms.openlocfilehash: 01b63f92ba7cf36802c0d91e4930280a3d802ab7
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156739"
---
Para configurar el SDK de Voz para que acepte entradas de audio comprimidas, cree `PullAudioInputStream` o `PushAudioInputStream`. A continuación, cree un objeto `AudioConfig` a partir de una instancia de la clase de secuencia, especificando el formato de compresión de la secuencia.

En el ejemplo siguiente supongamos que su caso de uso es la utilización de `PushStream` para un archivo comprimido. 

```go

package recognizer

import (
  "fmt"
  "time"
    "strings"

  "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
  "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
  "github.com/Microsoft/cognitive-services-speech-sdk-go/samples/helpers"
)

func RecognizeOnceFromCompressedFile(subscription string, region string, file string) {
  var containerFormat audio.AudioStreamContainerFormat
  if strings.Contains(file, ".mulaw") {
    containerFormat = audio.MULAW
  } else if strings.Contains(file, ".alaw") {
    containerFormat = audio.ALAW
  } else if strings.Contains(file, ".mp3") {
    containerFormat = audio.MP3
  } else if strings.Contains(file, ".flac") {
    containerFormat = audio.FLAC
  } else if strings.Contains(file, ".opus") {
    containerFormat = audio.OGGOPUS
  } else {
    containerFormat = audio.ANY
  }
  format, err := audio.GetCompressedFormat(containerFormat)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer format.Close()
  stream, err := audio.CreatePushAudioInputStreamFromFormat(format)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer stream.Close()
  audioConfig, err := audio.NewAudioConfigFromStreamInput(stream)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer audioConfig.Close()
  config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer config.Close()
  speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer speechRecognizer.Close()
  speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
  })
  speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
  })
  helpers.PumpFileIntoStream(file, stream)
  task := speechRecognizer.RecognizeOnceAsync()
  var outcome speech.SpeechRecognitionOutcome
  select {
  case outcome = <-task:
  case <-time.After(40 * time.Second):
    fmt.Println("Timed out")
    return
  }
  defer outcome.Close()
  if outcome.Error != nil {
    fmt.Println("Got an error: ", outcome.Error)
  }
  fmt.Println("Got a recognition!")
  fmt.Println(outcome.Result.Text)
}

```
