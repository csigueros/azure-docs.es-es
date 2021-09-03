---
title: Cómo obtener eventos de postura facial para la sincronización de labios
titleSuffix: Azure Cognitive Services
description: El SDK de Voz admite eventos de visema durante la síntesis de voz, que representa los planteamientos clave en la voz observada, como la posición de los labios, la mandíbula y la lengua al generar un fonema determinado.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 3601cd6f7580a4d87dda7488826e25ca85b233c9
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110654208"
---
# <a name="get-facial-pose-events"></a>Obtención de eventos de postura facial

> [!NOTE]
> Los eventos de visema solo están disponibles para `en-US`voces neuronales[ en ](language-support.md#text-to-speech) inglés (Estados Unidos) por ahora.

Un _visema_ es la descripción visual de un fonema en lenguaje hablado.
Define la posición de la esfera y la boca al hablar de una palabra.
Cada visema describe las principales supuestos faciales para un conjunto específico de fonemas.
El visema se puede usar para controlar el movimiento de los modelos de avatar 2D y 3D, y hacer coincidir perfectamente los movimientos de la boca con la voz sintetizada.

Los visemas facilitan el uso y el control de los avatares. Con los visemas, puede hacer lo siguiente:

 * Crear un **asistente para voz virtual animado** para quioscos inteligentes y servicios integrados multi-modo para los clientes.
 * Crear **informativos inmersivos** y mejorar las experiencias del público con movimientos naturales de la cara y la boca.
 * Generar **avatares de juegos y personajes animados más interactivos** que puedan hablar con contenido dinámico.
 * Crear **vídeos de enseñanza de idiomas más eficaces** que ayuden a los aprendices a comprender el comportamiento de la boca de cada palabra y fonema.
 * Las personas con deficiencias auditivas también pueden captar los sonidos de forma visual y **"leer los labios"** del contenido de voz que muestra los visemas en una cara animada.

Vea [el vídeo de introducción](https://youtu.be/ui9XT47uwxs) del visema.
> [!VIDEO https://www.youtube.com/embed/ui9XT47uwxs]

## <a name="azure-neural-tts-can-produce-visemes-with-speech"></a>TTS neuronal de Azure puede producir visemas con voz

Una voz neuronal convierte el texto de entrada o SSML (Lenguaje de marcado de síntesis de voz) en voz sintetizada. La salida de audio de voz puede ir acompañada de los ID de visema y sus marcas de tiempo de desplazamiento. Cada id. de visema especifica una posición específica en la voz observada, como la de los labios, la mandíbula y la lengua al generar un fonema concreto. Con un motor de representación 2D o 3D, puede usar estos eventos de visema para animar el avatar.

El flujo de trabajo general de visemas se muestra en el diagrama de flujo siguiente.

![Flujo de trabajo general del visema](media/text-to-speech/viseme-structure.png)

| Parámetro | Descripción |
|-----------|-------------|
| Id. de visema | Número entero que especifica un visema. En inglés (Estados Unidos), ofrecemos 22 visemas diferentes para describir las formas de la boca para un conjunto específico de fonemas. No hay una correspondencia uno a uno entre visemas y fonemas. A menudo, varios fonemas se corresponden con un solo visema, ya que varios fonemas tienen el mismo aspecto en la superficie cuando se producen, como `s` y `z`. Consulte la [tabla de asignación entre visemas y fonemas](#map-phonemes-to-visemes).  |
| Desplazamiento de audio | La hora de inicio de cada visema, en pasos (100 nanosegundos). |

## <a name="get-viseme-events-with-the-speech-sdk"></a>Obtención de eventos de visema con el SDK de voz

Para obtener visemas con la voz sintetizada, suscríbase al evento `VisemeReceived` en el SDK de Voz.
En el fragmento de código siguiente se muestra cómo suscribirse al evento de visema.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

Este es un ejemplo de la salida del visema.

```text
(Viseme), Viseme ID: 1, Audio offset: 200ms.

(Viseme), Viseme ID: 5, Audio offset: 850ms.

……

(Viseme), Viseme ID: 13, Audio offset: 2350ms.
```

Después de obtener la salida del visema, puede usar estos eventos para controlar la animación de los personajes. Puede crear personajes propios y animarlos de forma automática.

Para los personajes 2D, puede diseñar uno que se adapte al escenario y usar gráficos vectoriales escalables (SVG) para cada identificador de visema a fin de obtener una posición de la cara basada en el tiempo. Con las etiquetas temporales proporcionadas en el evento de visema, estos gráficos SVG bien diseñados se procesarán con modificaciones de suavizado y proporcionarán una animación sólida a los usuarios. Por ejemplo, en la ilustración siguiente se muestra una animación de unos labios de color rojo diseñada para el aprendizaje de idiomas.

![Ejemplo de representación 2D](media/text-to-speech/viseme-demo-2D.png)

En el caso de los personajes 3D, imagine que son títeres. El maestro titiritero tira de las cuerdas de un estado a otro y las leyes de la física hacen el resto y controlan el movimiento fluido del títere. La salida del visema actúa como un maestro titiritero para proporcionar una escala de tiempo de acción. El motor de animación define las leyes físicas de la acción. Al interpolar fotogramas con algoritmos de aceleración, el motor puede generar animaciones de alta calidad.

## <a name="map-phonemes-to-visemes"></a>Asignación de fonemas a visemas

Las visemas varían según el lenguaje. Cada lenguaje tiene un conjunto de visemas que corresponden a sus fonemas específicos. En la tabla siguiente se muestra la correspondencia entre el alfabeto fonético internacional (IPA) fonemas y los identificadores de visema para inglés (Estados Unidos).

| IPA | Ejemplo | Id. de visema |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **a** te | 4 |
| ɛ | **e** very | 4 |
|æ  |   **a** ctive        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** ce           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **a** go           |1|
|ɪɹ |   **ear** s          |[6, 13]|
|ɛɹ |   **air** plane      |[4, 13]|
|ʊɹ |   c **ur** e          |[4, 13]|
|aɪ(ə)ɹ |   **Ire** land   |[11, 13]|
|aʊ(ə)ɹ |   **hour** s     |[9, 13]|
|ɔɹ |   **or** ange        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **ear** th         |[5, 13]|
|ɚ  |   all **er** gy       |[1, 13]|
|w  |   **w** ith, s **ue** de   |7|
|j  |   **y** ard, f **e** w     |6|
|p  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alk          |19|
|d  |   **d** ig           |19|
|k  |   **c** ut           |20|
|e  |   **g** o            |20|
|m  |   **m** at, s **m** ash    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th** in          |17|
|ð  |   **th** en          |17|
|s  |   **s** it           |15|
|z  |   **z** ap           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** elp          |12|
|tʃ |   **ch** in          |16|
|dʒ |   **j** oy           |16|
|l  |   **l** id, g **l** ad     |14|
|ɹ  |   **r** ed, b **r** ing    |13|


## <a name="next-steps"></a>Pasos siguientes

* [Documentación de referencia del SDK de voz](speech-sdk.md)
