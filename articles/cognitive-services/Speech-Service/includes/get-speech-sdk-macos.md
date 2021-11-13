---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 642fa02cf8f40d7cc6a39a1951499cb7921ed100
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252457"
---
Cuando se realizan desarrollos para macOS, están disponibles los siguientes SDK de Voz.

- El SDK de Voz de Objective-C/Swift está disponible de forma nativa como paquete de CocoaPod para Mac x64 y silicios basados en ARM. Para más información sobre el uso del SDK de Voz de Objective-C con Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importación de Objective-C en Swift</a>.
- El SDK de Voz de .NET está disponible a través de un paquete de NuGet y se puede usar con marcos de aplicaciones **Xamarin.Mac** y **Unity**.
- El SDK de Voz de Python está disponible como módulo PyPI para las versiones 3.7, y posteriores, de Python.
- El SDK de Voz de Java está disponible a través del repositorio de Maven en forma de paquete JAR.

### <a name="system-requirements"></a>Requisitos del sistema

- macOS 10.14, o cualquier versión posterior

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        El paquete CocoaPod de macOS está disponible para su descarga y uso con el entorno de desarrollo integrado (IDE) de <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 o posterior </a>. En primer lugar, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">descargue el archivo binario de CocoaPod </a>. Extraiga el pod en el mismo directorio para su uso previsto, cree un *Podfile* y enumere `pod` como `target`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :osx, 10.14
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac expone el SDK de macOS completo para que los desarrolladores de .NET puedan compilar aplicaciones Mac nativas mediante C#. Para más información, vea <a href="/xamarin/mac/" target="_blank">Xamarin.Mac </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Recursos adicionales

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Código fuente de Objective-C en la guía de inicio rápido del SDK de voz de macOS </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">Código fuente de Swift en la guía de inicio rápido del SDK de voz de macOS </a>
