---
title: 'Procesamiento de audio con el SDK de Voz: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Introducción a las características, las funcionalidades y las restricciones del procesamiento de audio mediante el kit de desarrollo de software (SDK) de Voz.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: bbf774ac1f0b91848df48366436144d36b193033
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092955"
---
# <a name="audio-processing-with-speech-sdk"></a>Procesamiento de audio con el SDK de Voz

El SDK de Voz integra Microsoft Audio Stack (MAS), lo que permite que cualquier aplicación o producto use sus funcionalidades de procesamiento de audio en el audio de entrada. Se aplican los requisitos mínimos de [Requisitos mínimos para usar Microsoft Audio Stack](audio-processing-overview.md#minimum-requirements-to-use-microsoft-audio-stack).

Las principales características disponibles a través de las API del SDK de Voz incluyen:
* **Entrada de micrófono en tiempo real y entrada de archivo**: el procesamiento de Microsoft Audio Stack se puede aplicar a la entrada de micrófono en tiempo real, a las secuencias y a la entrada basada en archivos. 
* **Selección de mejoras**: para permitir el control total del escenario, el SDK permite deshabilitar mejoras individuales como la anulación de la reverberación, la supresión de ruido, el control automático de ganancia y la cancelación del eco acústico. Por ejemplo, si el escenario no incluye la representación del audio de salida que debe suprimirse del audio de entrada, tiene la opción de deshabilitar la cancelación del eco acústico.
* **Geometrías de micrófono personalizadas**: el SDK le permite proporcionar su propia información de geometría de micrófono personalizada, además de admitir geometrías preestablecidas, como las matrices lineales de dos micrófonos y cuatro micrófonos y la matriz circular de siete micrófonos (consulte más información sobre las geometrías preestablecidas admitidas en [Recomendaciones de matrices de micrófono](speech-sdk-microphone.md#microphone-geometry)).
* **Ángulos con forma de haz**: se pueden proporcionar ángulos con forma de haz específicos para optimizar la entrada de audio que se origina desde una ubicación predeterminada, en relación con los micrófonos.

El procesamiento se realiza de forma totalmente local y se usa el SDK de Voz. Microsoft Audio Stack no transmite datos de audio a los servicios en la nube de Microsoft para su procesamiento. La única excepción se aplica en el servicio de transcripción de conversaciones, donde el audio sin formato se envía a los servicios en la nube de Microsoft para su procesamiento. 

> [!NOTE]
> El SDK de dispositivos de voz está ahora en desuso. Las versiones archivadas del SDK de dispositivos de voz están disponibles [aquí](speech-devices-sdk.md), con el código de ejemplo correspondiente disponible en [GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK). Se recomienda a todos los usuarios del SDK de dispositivos de voz migrar a la utilización del SDK de voz v1.19 o posterior.

## <a name="reference-channel-for-echo-cancellation"></a>Canal de referencia para la cancelación del eco

Microsoft Audio Stack requiere que el canal de referencia (también conocido como canal de bucle invertido) realice la cancelación del eco. El origen del canal de referencia varía según la plataforma:
* **Windows**: el SDK de Voz recopila automáticamente el canal de referencia si se proporciona la opción `SpeakerReferenceChannel::LastChannel` al crear `AudioProcessingOptions`.
* **Linux**: ALSA (Advanced Linux Sound Architecture) deberá configurarse para proporcionar la secuencia de audio de referencia como último canal para el dispositivo de entrada de audio que se usará. Esto es además de proporcionar la opción `SpeakerReferenceChannel::LastChannel` al crear `AudioProcessingOptions`.

## <a name="language-and-platform-support"></a>Compatibilidad con lenguajes y plataformas

| Idioma   | Plataformas    | Documentos de referencia |
|------------|----------------|----------------|
| C++        | Windows, Linux | [Documentos de C++](/cpp/cognitive-services/speech/) |
| C#         | Windows, Linux | [Documentos de C#](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java       | Windows, Linux | [Documentos de Java](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Código de ejemplo

### <a name="using-microsoft-audio-stack-with-all-default-options"></a>Uso de Microsoft Audio Stack con todas las opciones predeterminadas

En este ejemplo se muestra cómo usar MAS con todas las opciones de mejora predeterminadas en la entrada del micrófono predeterminado del dispositivo.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-a-preset-microphone-geometry"></a>Uso de Microsoft Audio Stack con una geometría de micrófono preestablecida

En este ejemplo se muestra cómo usar MAS con una geometría de micrófono predefinida en un dispositivo de entrada de audio especificado. En este ejemplo:
* **Opciones de mejora**: las mejoras predeterminadas se aplicarán en la secuencia de audio de entrada.
* **Geometría preestablecida**: la geometría preestablecida representa una matriz lineal de 2 micrófonos.
* **Dispositivo de entrada de audio**: el id. de dispositivo de entrada de audio es `hw:0,1`. Para más información sobre cómo seleccionar un dispositivo de entrada de audio, consulte [Cómo seleccionar un dispositivo de entrada de audio con el SDK de Voz](how-to-select-audio-input-devices.md).

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
var audioInput = AudioConfig.FromMicrophoneInput("hw:0,1", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry::Linear2);
auto audioInput = AudioConfig::FromMicrophoneInput("hw:0,1", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
AudioConfig audioInput = AudioConfig.fromMicrophoneInput("hw:0,1", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-custom-microphone-geometry"></a>Uso de Microsoft Audio Stack con una geometría de micrófono personalizada

En este ejemplo se muestra cómo usar MAS con una geometría de micrófono personalizada en un dispositivo de entrada de audio especificado. En este ejemplo:
* **Opciones de mejora**: las mejoras predeterminadas se aplicarán en la secuencia de audio de entrada.
* **Geometría personalizada**: una geometría de micrófono personalizada para una matriz de 7 micrófonos se proporciona especificando las coordenadas del micrófono. Las unidades de las coordenadas son milímetros.
* **Entrada de audio**: la entrada de audio es de un archivo, donde se espera capturar el audio del archivo desde un dispositivo de entrada de audio correspondiente a la geometría personalizada especificada. 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7]
{
    new MicrophoneCoordinates(0, 0, 0),
    new MicrophoneCoordinates(40, 0, 0),
    new MicrophoneCoordinates(20, -35, 0),
    new MicrophoneCoordinates(-20, -35, 0),
    new MicrophoneCoordinates(-40, 0, 0),
    new MicrophoneCoordinates(-20, 35, 0),
    new MicrophoneCoordinates(20, 35, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var audioInput = AudioConfig.FromWavFileInput("katiesteve.wav", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Planar,
    { { 0, 0, 0 }, { 40, 0, 0 }, { 20, -35, 0 }, { -20, -35, 0 }, { -40, 0, 0 }, { -20, 35, 0 }, { 20, 35, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto audioInput = AudioConfig::FromWavFileInput("katiesteve.wav", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7];
microphoneCoordinates[0] = new MicrophoneCoordinates(0, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(40, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, -35, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(-20, -35, 0);
microphoneCoordinates[4] = new MicrophoneCoordinates(-40, 0, 0);
microphoneCoordinates[5] = new MicrophoneCoordinates(-20, 35, 0);
microphoneCoordinates[6] = new MicrophoneCoordinates(20, 35, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
AudioConfig audioInput = AudioConfig.fromWavFileInput("katiesteve.wav", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-select-enhancements"></a>Uso de Microsoft Audio Stack con mejoras seleccionadas

En este ejemplo se muestra cómo usar MAS con un conjunto personalizado de mejoras en el audio de entrada. De forma predeterminada, todas las mejoras están habilitadas, pero hay opciones para deshabilitar la anulación de la reverberación, la supresión del ruido, el control automático de ganancia y la cancelación del eco individualmente mediante `AudioProcessingOptions`.

En este ejemplo:
* **Opciones de mejora**: la cancelación del eco y la supresión del ruido se deshabilitarán, mientras todas las demás mejoras permanecen habilitadas.
* **Dispositivo de entrada de audio**: el dispositivo de entrada de audio es el micrófono predeterminado del dispositivo.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-to-specify-beamforming-angles"></a>Uso de Microsoft Audio Stack para especificar ángulos con forma de haz

En este ejemplo se muestra cómo usar MAS con una geometría de micrófono personalizada y ángulos con forma de haz en un dispositivo de entrada de audio especificado. En este ejemplo:
* **Opciones de mejora**: las mejoras predeterminadas se aplicarán en la secuencia de audio de entrada.
* **Geometría personalizada**: una geometría de micrófono personalizada para una matriz de 4 micrófonos se proporciona especificando las coordenadas del micrófono. Las unidades de las coordenadas son milímetros.
* **Ángulos con forma de haz**: los ángulos con forma de haz se especifican para optimizar el audio que se origina en ese intervalo. Las unidades para los ángulos son grados. En el código de ejemplo siguiente, el ángulo inicial se establece en 70 grados y el ángulo final en 110 grados.
* **Entrada de audio**: la entrada de audio es de una secuencia de inserción, donde se espera capturar el audio de la secuencia desde un dispositivo de entrada de audio correspondiente a la geometría personalizada especificada. 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4]
{
    new MicrophoneCoordinates(-60, 0, 0),
    new MicrophoneCoordinates(-20, 0, 0),
    new MicrophoneCoordinates(20, 0, 0),
    new MicrophoneCoordinates(60, 0, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Linear, 70, 110, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var pushStream = AudioInputStream.CreatePushStream();
var audioInput = AudioConfig.FromStreamInput(pushStream, audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Linear,
    70,
    110,
    { { -60, 0, 0 }, { -20, 0, 0 }, { 20, 0, 0 }, { 60, 0, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto pushStream = AudioInputStream::CreatePushStream();
auto audioInput = AudioConfig::FromStreamInput(pushStream, audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4];
microphoneCoordinates[0] = new MicrophoneCoordinates(-60, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(-20, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, 0, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(60, 0, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, 70, 110, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
PushAudioInputStream pushStream = AudioInputStream.createPushStream();
AudioConfig audioInput = AudioConfig.fromStreamInput(pushStream, audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---
