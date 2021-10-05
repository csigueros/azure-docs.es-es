---
title: 'Identificación de idioma: servicio Voz'
titleSuffix: Azure Cognitive Services
description: La identificación del idioma se usa para determinar el idioma que se habla en el audio que se pasa al SDK de Voz mediante la comparación con una lista de idiomas proporcionados.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/27/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-cs-cpp-py
ms.openlocfilehash: 6830ded4b6a074a29de9741eee957b0a4a1ff4b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588131"
---
# <a name="language-identification-preview"></a>Identificación del idioma (versión preliminar)

La identificación del idioma se usa para determinar el idioma que se habla en el audio que se pasa al SDK de Voz mediante la comparación con una lista de idiomas proporcionados. 

La identificación del idioma también se puede usar con la [traducción de voz](./get-started-speech-translation.md#multi-lingual-translation-with-language-identification) o mediante la [identificación del idioma durante el reconocimiento de voz](./how-to-automatic-language-detection.md). 

Para ver los idiomas que están disponibles, consulte la [compatibilidad de idioma](language-support.md).

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se da por supuesto que tiene una suscripción de Azure y un recurso de voz, y también, que conoce los conceptos básicos del reconocimiento de voz.

## <a name="standalone-language-identification"></a>Identificación del idioma independiente

En los casos de uso en los que solo desee detectar el idioma de origen que se habla, puede usar la identificación del idioma independiente como se muestra en el ejemplo de código siguiente. `SourceLanguageRecognizer` también se puede usar en escenarios de reconocimiento continuo.

::: zone pivot="programming-language-csharp"

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
// can switch "Latency" to "Accuracy" depending on priority
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SourceLanguageRecognizer(speechConfig, autoDetectSourceLanguageConfig))
{
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.RecognizedSpeech)
    {
        var lang = AutoDetectSourceLanguageResult.FromResult(result).Language;
        Console.WriteLine($"DETECTED: Language={lang}");
    }
}
```

Consulte el [ejemplo en GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/standalone_language_detection_samples.cs) para más ejemplos de identificación del idioma independiente, incluido un ejemplo de identificación continua.

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
config->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SourceLanguageRecognizer::FromConfig(config, autoDetectSourceLanguageConfig);
cout << "Say something...\n";

auto result = recognizer->RecognizeOnceAsync().get();
if (result->Reason == ResultReason::RecognizedSpeech)
{
    auto lidResult = AutoDetectSourceLanguageResult::FromResult(result);
    cout << "DETECTED: Language="<< lidResult->Language << std::endl;
}
```

Consulte el [ejemplo en GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/standalone_language_detection_samples.cpp) para más ejemplos de identificación del idioma independiente, incluido un ejemplo de identificación continua.

::: zone-end

::: zone pivot="programming-language-python"

```python
import azure.cognitiveservices.speech as speechsdk

speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    
speech_config.set_property(property_id=speechsdk.PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, value='Accuracy')

speech_language_detection = speechsdk.SourceLanguageRecognizer(speech_config=speech_config, auto_detect_source_language_config=auto_detect_source_language_config)

result = speech_language_detection.recognize_once()

# Check the result
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("RECOGNIZED: {}".format(result))
    detectedSrcLang = result.properties[speechsdk.PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult]
    print("Detected Language: {}".format(detectedSrcLang))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized")
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

Consulte el [ejemplo en GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_language_detection_sample.py) para más ejemplos de identificación del idioma independiente, incluido un ejemplo de identificación continua.

::: zone-end