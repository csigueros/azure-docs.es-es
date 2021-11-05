---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 11e93561a0f78ad1fb62d1cab1f3e8d8f3f3b293
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069195"
---
En este inicio rápido, aprenderá los patrones de diseño básicos de Speaker Recognition mediante el SDK de Voz, que incluyen:

* Comprobación dependiente e independiente del texto.
* Identificación del hablante para identificar una muestra de voz entre un grupo de voces.
* Eliminación de perfiles de voz.

Para obtener una visión general de los conceptos de Speaker Recognition, consulte el artículo de [información general](../../../speaker-recognition-overview.md). Consulte el nodo de referencia situado en el panel de navegación izquierdo para ver una lista de las plataformas admitidas.

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se da por sentado que tiene una cuenta de Azure y una suscripción al servicio de voz. Si no dispone de una cuenta y una suscripción, [pruebe el servicio de voz de forma gratuita](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Speaker Recognition *solo* se admite actualmente en los recursos de Voz de Azure creados en la región de `westus`.

## <a name="text-dependent-verification"></a>Comprobación dependiente del texto

Speaker Verification es el acto de confirmar que un hablante coincide con una voz conocida o **inscrita**. El primer paso es **inscribir** un perfil de voz, de modo que el servicio tenga algo para comparar futuras muestras de voz. En este ejemplo, el perfil se inscribe mediante una estrategia **dependiente del texto**, que requiere que se use una frase de contraseña específica tanto para la inscripción como para la comprobación. Consulte los [documentos de referencia](/rest/api/speakerrecognition/) para ver la lista de las frases de contraseña admitidas.

Para empezar, [cree un perfil de voz](/rest/api/speakerrecognition/verification/textdependent/createprofile). Tendrá que insertar la región y la clave de suscripción del servicio Voz en cada uno de los comandos curl de este artículo.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Hay tres tipos de perfiles de voz:

- Comprobación dependiente del texto
- Comprobación independiente del texto
- Identificación independiente del texto

En este caso, se crea un perfil de voz de comprobación dependiente del texto. Debería recibir la respuesta siguiente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Luego, se [inscribe el perfil de voz](/rest/api/speakerrecognition/verification/textdependent/createenrollment). En el caso del valor del parámetro `--data-binary`, especifique un archivo de audio en el equipo que contenga una de las frases de contraseña admitidas, como "my voice is my passport, verify me". Este tipo de archivo de audio se puede grabar con una aplicación como [Windows Voice Recorder](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab), o bien puede generarlo mediante la [conversión de texto a voz](../../../index-text-to-speech.yml).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Debería recibir la respuesta siguiente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Esta respuesta le indica que debe inscribir dos muestras de audio más.

Cuando haya inscrito un total de tres ejemplos de audio, debe recibir la siguiente respuesta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Ya está listo para [comprobar una muestra de audio con el perfil de voz](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Esta muestra de audio debe contener la misma frase de contraseña que los ejemplos que usó para inscribir el perfil de voz.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Debería recibir la respuesta siguiente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept` significa que la frase de contraseña coincidía y que la comprobación se realizó correctamente. La respuesta también contiene una puntuación de similitud comprendida entre 0,0 y 1,0.

Para finalizar, [elimine el perfil de voz](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

No hay ninguna respuesta.

## <a name="text-independent-verification"></a>Comprobación independiente del texto

A diferencia de la comprobación **dependiente del texto**, la comprobación **independiente del texto**:

* No es preciso que se diga una frase de contraseña determinada, se puede decir cualquier cosa.
* No se necesitan tres muestras de audio, pero *sí* 20 segundos de audio en total.

Para empezar, [cree un perfil de comprobación independiente del texto](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Debería recibir la respuesta siguiente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

A continuación, [inscriba el perfil de voz](/rest/api/speakerrecognition/verification/textindependent/createenrollment). De nuevo, en lugar de enviar tres muestras de audio, debe enviar ejemplos de audio que contengan un total de 20 segundos de audio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Una vez que haya enviado suficientes muestras de audio, debe recibir la siguiente respuesta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Ya está listo para [comprobar una muestra de audio con el perfil de voz](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). De nuevo, no es preciso que esta muestra contenga una frase de contraseña. Puede contener cualquier locución, siempre que incluya un total de al menos cuatro segundos de audio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Debería recibir la respuesta siguiente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept` significa que la comprobación se realizó correctamente. La respuesta también contiene una puntuación de similitud comprendida entre 0,0 y 1,0.

Para finalizar, [elimine el perfil de voz](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

No hay ninguna respuesta.

## <a name="speaker-identification"></a>Identificación del hablante

Speaker Identification se utiliza para determinar **quién** está hablando de un grupo determinado de voces inscritas. El proceso es similar al de **comprobación independiente del texto**. La diferencia principal es que la comprobación se puede realizar con varios perfiles de voz a la vez, en lugar de con uno solo.

Para empezar, [cree un perfil de identificación independiente del texto](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Debería recibir la respuesta siguiente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Luego, se [inscribe el perfil de voz](/rest/api/speakerrecognition/identification/textindependent/createenrollment). De nuevo, debe enviar muestras de audio que contengan un total de 20 segundos de audio. No es necesario que estas muestras contengan una frase de contraseña.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Una vez que haya enviado suficientes muestras de audio, debe recibir la siguiente respuesta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Ya está listo para [identificar una muestra de audio mediante el perfil de voz](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). El comando de identificación acepta una lista delimitada por comas de posibles identificadores del perfil de voz. En este caso, solo tendrá que pasar el identificador del perfil de voz que creó anteriormente. Sin embargo, si lo desea, puede usar varios identificadores de perfil de voz, y cada perfil de voz se inscribe con muestras de audio de una voz diferente.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Debería recibir la respuesta siguiente.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

La respuesta contiene el identificador del perfil de voz que más se aproxime al ejemplo de audio que envió. También contiene una lista de perfiles de voz candidatos, clasificados por orden de similitud.

Para finalizar, [elimine el perfil de voz](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

No hay ninguna respuesta.
