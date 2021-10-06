---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: a651fe0ddb25fb44de518c794264133f16879982
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378026"
---
En este inicio rápido, aprenderá los patrones de diseño básicos de Speaker Recognition mediante el SDK de Voz, que incluyen:

* Comprobación dependiente e independiente del texto.
* Identificación del hablante para identificar una muestra de voz entre un grupo de voces.
* Eliminación de perfiles de voz.

Para obtener una visión general de los conceptos de reconocimiento de voz, consulte el artículo de [información general](../../../speaker-recognition-overview.md).

## <a name="skip-to-samples-on-github"></a>Pasar a los ejemplos en GitHub

Si quiere pasar directamente al código de ejemplo, consulte los [ejemplos del inicio rápido de JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) en GitHub.

## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por sentado que tiene una cuenta de Azure y una suscripción al servicio de voz. Si no dispone de una cuenta y una suscripción, [pruebe el servicio de voz de forma gratuita](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Speaker Recognition *solo* se admite actualmente en los recursos de Voz de Azure creados en la región de `westus`.

## <a name="install-the-speech-sdk"></a>Instalación de Speech SDK

En primer lugar, deberá instalar <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK para JavaScript</a>. Utilice las siguientes instrucciones en función de la plataforma:

- [Node.js](../../../speech-sdk.md?tabs=nodejs#get-the-speech-sdk)
- [Explorador web](../../../speech-sdk.md?tabs=browser#get-the-speech-sdk)

Además, en función del entorno de destino, use una de las siguientes:

# <a name="script"></a>[script](#tab/script)

Descargue y extraiga el archivo *microsoft.cognitiveservices.speech.sdk.bundle.js* de <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK para JavaScript</a> y colóquelo en una carpeta accesible para el archivo HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Si el destino es un explorador web y usa la etiqueta `<script>`, el prefijo `sdk` no es necesario. El prefijo `sdk` es un alias que se usa para asignar un nombre al módulo `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Para más información sobre `import`, consulte <a href="https://javascript.info/import-export" target="_blank">export and import </a> (export e import).

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para más información sobre `require`, consulte <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">what is require? </a> (¿Qué es require?).

---

## <a name="import-dependencies"></a>Dependencias de importación

Para ejecutar los ejemplos de este artículo, agregue las siguientes instrucciones al principio del archivo .js.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Estas instrucciones importan las bibliotecas necesarias y obtienen la clave y región de la suscripción del servicio Voz de las variables de entorno. También especifican las rutas de acceso a los archivos de audio que usará en las siguientes tareas.

## <a name="create-helper-function"></a>Creación de funciones de asistente

Agregue la siguiente función del asistente para leer archivos de audio en secuencias que los usa el servicio Voz.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

En esta función, se usan los métodos [AudioInputStream. createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream#createpushstream-audiostreamformat-) y [AudioConfig. fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) para crear un objeto [AudioConfig](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig). Este objeto `AudioConfig` representa una secuencia de audio. En las siguientes tareas, utilizará varios de estos objetos `AudioConfig`.

## <a name="text-dependent-verification"></a>Comprobación dependiente del texto

Speaker Verification es el acto de confirmar que un hablante coincide con una voz conocida o **inscrita**. El primer paso es **inscribir** un perfil de voz, de modo que el servicio tenga algo para comparar futuras muestras de voz. En este ejemplo, el perfil se inscribe mediante una estrategia **dependiente del texto**, que requiere que se use una frase de contraseña específica tanto para la inscripción como para la comprobación. Consulte los [documentos de referencia](/rest/api/speakerrecognition/) para ver la lista de las frases de contraseña admitidas.

### <a name="textdependentverification-function"></a>Función TextDependentVerification

Empiece por crear la función `TextDependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Esta función crea un objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) con el método [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-). Observe que hay tres [tipos](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype) de `VoiceProfile`:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

En este caso, pasará `VoiceProfileType.TextDependentVerification` a `VoiceProfileClient.createProfileAsync`.

Luego, llamará a dos funciones auxiliares que definirá a continuación: `AddEnrollmentsToTextDependentProfile` y `SpeakerVerify`. Por último, llamará a [VoiceProfileClient. deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para quitar el perfil.

### <a name="addenrollmentstotextdependentprofile-function"></a>Función AddEnrollmentsToTextDependentProfile

Defina la función siguiente para inscribir un perfil de voz.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

En esta función, se llama a la función `GetAudioConfigFromFile`, que se ha definido anteriormente, para crear objetos `AudioConfig` a partir de muestras de audio. Estas muestras de audio contienen una frase de contraseña, como "My voice is my passport, verify me". Luego, se inscriben estas muestras de audio mediante el método [VoiceProfileClient. enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

### <a name="speakerverify-function"></a>Función SpeakerVerify

Defina `SpeakerVerify` como se indica a continuación.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

En esta función, creará un objeto [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel) con el método [SpeakerVerificationModel.FromProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel#fromprofile-voiceprofile-) y pasará el objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) que creó anteriormente.

A continuación, llamará al método [SpeechRecognizer. recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) para validar una muestra de audio que contiene la misma frase de contraseña que las muestras de audio que inscribió anteriormente. `SpeechRecognizer.recognizeOnceAsync` devuelve un objeto [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) cuya `score`, propiedad contiene una puntuación de similitud comprendida entre 0,0 y 1,0. El objeto `SpeakerRecognitionResult` también contiene una propiedad `reason` del tipo [ResultReason](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason). Si la comprobación se realizó correctamente, la propiedad `reason` debería tener el valor `RecognizedSpeaker`.

## <a name="text-independent-verification"></a>Comprobación independiente del texto

A diferencia de la comprobación **dependiente del texto**, la comprobación **independiente del texto**:

* No es preciso que se diga una frase de contraseña determinada, se puede decir cualquier cosa.
* No se necesitan tres muestras de audio, pero *sí* 20 segundos de audio en total.

### <a name="textindependentverification-function"></a>Función TextIndependentVerification

Empiece por crear la función `TextIndependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Al igual que la función `TextDependentVerification`, esta función crea un objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) con el método [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

En este caso, pasará `VoiceProfileType.TextIndependentVerification` a `createProfileAsync`.

Luego, llamará a dos funciones auxiliares: `AddEnrollmentsToTextIndependentProfile`, que definirá a continuación, y `SpeakerVerify`, que ya ha definido. Por último, llamará a [VoiceProfileClient. deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para quitar el perfil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Defina la función siguiente para inscribir un perfil de voz.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

En esta función, se llama a la función `GetAudioConfigFromFile`, que se ha definido anteriormente, para crear objetos `AudioConfig` a partir de muestras de audio. Luego, se inscriben estas muestras de audio mediante el método [VoiceProfileClient. enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

## <a name="speaker-identification"></a>Identificación del hablante

Speaker Identification se utiliza para determinar **quién** está hablando de un grupo determinado de voces inscritas. El proceso es similar al de **comprobación independiente del texto**. La diferencia principal es que la comprobación se puede realizar con varios perfiles de voz a la vez, en lugar de con uno solo.

### <a name="textindependentidentification-function"></a>Función TextIndependentIdentification

Empiece por crear la función `TextIndependentIdentification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Al igual que las funciones `TextDependentVerification` y `TextIndependentVerification`, esta función crea un objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) con el método [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

En este caso, pasará `VoiceProfileType.TextIndependentIdentification` a `VoiceProfileClient.createProfileAsync`.

Luego, llamará a dos funciones auxiliares: `AddEnrollmentsToTextIndependentProfile`, que ya ha definido y `SpeakerIdentify`, que definirá a continuación. Por último, llamará a [VoiceProfileClient. deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para quitar el perfil.

### <a name="speakeridentify-function"></a>Función SpeakerIdentify

Defina la función `SpeakerIdentify` como se indica a continuación.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

En esta función, creará un objeto [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel) con el método [SpeakerIdentificationModel.fromProfiles](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel#fromprofiles-voiceprofile---) y pasará el objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) que creó anteriormente.

Luego, llamará al método [SpeechRecognizer. recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) y utilizara una muestra de audio.
`SpeechRecognizer.recognizeOnceAsync` intentará identificar la voz de esta muestra de audio en función de los objetos `VoiceProfile` que se usaron para crear `SpeakerIdentificationModel`. Devolverá un objeto [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult), cuya propiedad `profileId` identificará el `VoiceProfile` coincidente, si hay alguno, mientras que la propiedad `score` contendrá contiene una puntuación de similitud comprendida entre 0,0 y 1,0.

## <a name="main-function"></a>Función main

Por último, definirá la función `main` de la sigiuente manera.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Esta función crea un objeto [VoiceProfileClient](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient), que se usa para crear, inscribir y eliminar perfiles de voz. Y, posteriormente, llama a las funciones que definió anteriormente.