---
title: Uso de la coincidencia de patrones de lenguaje simples con el SDK de Voz de C++
titleSuffix: Azure Cognitive Services
description: En esta guía, aprenderá a reconocer intenciones y entidades a partir de patrones simples.
services: cognitive-services
author: chschrae
manager: travisw
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: chschrae
ms.custom: devx-track-cpp
ms.openlocfilehash: abb89fc460915d60402e6b259a8d34c0c5cdf05c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713366"
---
# <a name="how-to-use-simple-language-pattern-matching-with-the-c-speech-sdk"></a>Uso de la coincidencia de patrones de lenguaje simples con el SDK de Voz de C++

El [SDK de Voz](speech-sdk.md) de Cognitive Services tiene una característica integrada para proporcionar **reconocimiento de intenciones** con **coincidencia de patrones de lenguaje simples**. Una intención es algo que el usuario quiere hacer: cerrar una ventana, marcar una casilla, insertar texto, etc.

En esta guía, se usa el SDK de Voz para desarrollar una aplicación de consola de C++ que deriva las intenciones de las expresiones de los usuarios mediante el micrófono del dispositivo. Aprenderá a:

> [!div class="checklist"]
>
> - Crear un proyecto de Visual Studio que haga referencia al paquete NuGet del SDK de Voz
> - Crear una configuración de voz y obtener un reconocedor de intenciones
> - Agregar intenciones y patrones a través de la API del SDK de Voz
> - Reconocer la voz a partir de un archivo
> - Usar el reconocimiento asincrónico, continuo y controlado por eventos

## <a name="when-should-you-use-this"></a>¿Cuándo se debe usar?

Use este código de ejemplo si: 
* Solo le interesa hacer coincidir muy estrictamente lo que ha dicho el usuario. Estos patrones coinciden de forma más agresiva que LUIS.
* No tiene acceso a una aplicación de LUIS, pero aun así quiere intenciones. Esto puede resultar útil, ya que está insertado en el SDK.
* No puede o no quiere crear una aplicación de LUIS, pero aun así le interesa alguna funcionalidad de comandos de voz.

Si no tiene acceso a una aplicación de LUIS, pero de todos modos quiere intenciones, esto puede resultarle útil, ya que está insertado en el SDK.


## <a name="prerequisites"></a>Requisitos previos

Asegúrese de disponer de los siguientes elementos antes de empezar esta guía:

- Un [recurso de Azure Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) o un [recurso de Voz unificada](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (cualquier edición).

## <a name="speech-and-simple-patterns"></a>Voz y patrones simples

Los patrones simples son una característica del SDK de Voz y necesitan un recurso de Cognitive Services o un recurso de Voz unificada.

Un patrón es una frase que incluye una entidad en algún lugar dentro de él. Para definir una entidad, se encapsula una palabra entre llaves. Por ejemplo:

```
    Take me to the {floorName}
```

Esto define una entidad con el identificador "floorName", que distingue mayúsculas de minúsculas.

Se omitirán todos los demás caracteres especiales y signos de puntuación.

Las intenciones se agregarán mediante llamadas a la API IntentRecognizer->AddIntent().

## <a name="create-a-speech-project-in-visual-studio"></a>Creación de un proyecto de contenido de voz en Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

Abra el proyecto en Visual Studio.

Inicie Visual Studio 2019.
Cargue el proyecto y abra helloworld.cpp.
Comience con código reutilizable. Vamos a agregar código que funcione como esqueleto para el proyecto. Tenga en cuenta que ha creado un método asincrónico denominado recognizeIntent().

## <a name="open-your-project-in-visual-studio"></a>Abra el proyecto en Visual Studio.

Después abra el proyecto en Visual Studio.

1. Inicie Visual Studio 2019.
2. Cargue el proyecto y abra `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Inicio con código reutilizable

Vamos a agregar código que funcione como el esqueleto del proyecto.

```cpp
    #include <iostream>
    #include <speechapi_cxx.h>

    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Intent;

    int main()
    {
        std::cout << "Hello World!\n";

        auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    }
```

## <a name="create-a-speech-configuration"></a>Creación de una configuración de Voz

Para poder inicializar un objeto `IntentRecognizer`, es preciso crear una configuración que use la clave y la ubicación del recurso de predicción de Cognitive Services.

* Reemplace `"YOUR_SUBSCRIPTION_KEY"` por la clave de predicción de Cognitive Services.
* Reemplace `"YOUR_SUBSCRIPTION_REGION"` por la región del recurso de Cognitive Services.

En este ejemplo se usa el método `FromSubscription()` para compilar la clase `SpeechConfig`. Para ver una lista completa de los métodos disponibles, consulte [Clase SpeechConfig](/cpp/cognitive-services/speech/speechconfig).

## <a name="initialize-an-intentrecognizer"></a>Inicialización de IntentRecognizer

Ahora cree un objeto `IntentRecognizer`. Inserte este código justo debajo de la configuración de Voz.

```cpp
    auto intentRecognizer = IntentRecognizer::FromConfig(config);
```

## <a name="add-some-intents"></a>Adición de algunas intenciones

Debe asociar algunos patrones al objeto `IntentRecognizer` mediante una llamada a `AddIntent()`.
Agregaremos dos intenciones con el mismo identificador para cambiar las plantas, y otra intención con un identificador independiente para abrir y cerrar puertas.

```cpp
    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");
```

> [!NOTE]
> No hay ningún límite en el número de entidades que se pueden declarar, pero coincidirán de forma flexible. Si agrega una frase como "{action} door" ("{acción} puerta"), coincidirá cada vez que haya texto delante de la palabra "door". Las intenciones se evalúan en función de su número de entidades. Si coinciden dos patrones, se devolverá el que tenga entidades más definidas.

## <a name="recognize-an-intent"></a>Reconocimiento de una intención

En el objeto `IntentRecognizer`, va a llamar al método `RecognizeOnceAsync()`. Este método solicita al servicio de Voz que reconozca la voz en una sola frase y que deje de reconocer la voz una vez que la frase se haya identificado. Por motivos de simplicidad, esperaremos a que se complete la devolución futura.

Inserte este código debajo de las intenciones:

```cpp
    std::cout << "Say something ..." << std::endl;
    auto result = intentRecognizer->RecognizeOnceAsync().get();
```

## <a name="display-the-recognition-results-or-errors"></a>Visualización de los resultados (o errores) del reconocimiento

Cuando el servicio de Voz devuelva el resultado del reconocimiento, imprimiremos el resultado.

Inserte este código debajo de `auto result = intentRecognizer->RecognizeOnceAsync().get();`:

```cpp
auto entities = result->GetEntities();

switch (result->Reason)
{
case ResultReason::RecognizedSpeech:
case ResultReason::RecognizedIntent:
    std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
    std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
    if (entities.find("floorName") != entities.end())
    {
        std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
    }

    if (entities.find("action") != entities.end())
    {
        std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
    }

    break;
case ResultReason::NoMatch:
{
    auto noMatch = NoMatchDetails::FromResult(result);
    switch (noMatch->Reason)
    {
    case NoMatchReason::NotRecognized:
        std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
        break;
    case NoMatchReason::InitialSilenceTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::InitialBabbleTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::KeywordNotRecognized:
        std::cout << "NOMATCH: Keyword not recognized" << std::endl;
        break;
    }
    break;
}
case ResultReason::Canceled:
{
    auto cancellation = CancellationDetails::FromResult(result);

    if (!cancellation->ErrorDetails.empty())
    {
        std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
        std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
    }
}
default:
    break;
}
```

## <a name="check-your-code"></a>Comprobación del código

En este momento, el código debe tener esta apariencia:

```cpp
#include <iostream>
#include <speechapi_cxx.h>

using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Intent;

int main()
{
    auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    auto intentRecognizer = IntentRecognizer::FromConfig(config);

    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");

    std::cout << "Say something ..." << std::endl;

    auto result = intentRecognizer->RecognizeOnceAsync().get();
    auto entities = result->GetEntities();

    switch (result->Reason)
    {
    case ResultReason::RecognizedSpeech:
    case ResultReason::RecognizedIntent:
        std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
        std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
        if (entities.find("floorName") != entities.end())
        {
            std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
        }

        if (entities.find("action") != entities.end())
        {
            std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
        }

        break;
    case ResultReason::NoMatch:
    {
        auto noMatch = NoMatchDetails::FromResult(result);
        switch (noMatch->Reason)
        {
        case NoMatchReason::NotRecognized:
            std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
            break;
        case NoMatchReason::InitialSilenceTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::InitialBabbleTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::KeywordNotRecognized:
            std::cout << "NOMATCH: Keyword not recognized." << std::endl;
            break;
        }
        break;
    }
    case ResultReason::Canceled:
    {
        auto cancellation = CancellationDetails::FromResult(result);

        if (!cancellation->ErrorDetails.empty())
        {
            std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
            std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }
    default:
        break;
    }
}
```
## <a name="build-and-run-your-app"></a>Compilación y ejecución de la aplicación

Ya está listo para compilar la aplicación y probar el reconocimiento de voz con el servicio Voz.

1. **Compile el código**: en la barra de menús de Visual Studio, elija **Compilar** > **Compilar solución**.
2. **Inicie la aplicación**: en la barra de menús, elija **Depurar** > **Iniciar depuración** o presione <kbd>F5</kbd>.
3. **Inicie el reconocimiento**: se le pedirá que diga algo. El idioma predeterminado es el inglés. La voz se envía al servicio Voz, se transcribe como texto y se representa en la consola.

Por ejemplo, si dice "Take me to floor 7" (Lléveme a la planta 7), la salida debería ser esta:

```
Say something ...
RECOGNIZED: Text = Take me to floor 7.
  Intent Id = ChangeFloors
  Floor name: = seven
```
