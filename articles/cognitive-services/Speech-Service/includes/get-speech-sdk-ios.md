---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 942bf206454047f54765d096a2ac97fcabb895ab
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252343"
---
:::row:::
    :::column span="3":::
        Cuando se realizan desarrollos para iOS, están disponibles los siguientes SDK de Voz. El SDK de Voz de Objective-C/Swift está disponible de forma nativa en forma de paquete de CocoaPod para iOS. Como alternativa, se puede usar el SDK de Voz de .NET con marcos de aplicaciones de Xamarin.iOS y Unity.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Para obtener más información sobre el uso del SDK de Voz de Objective-C con Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importación de Objective-C en Swift </a>.

### <a name="system-requirements"></a>Requisitos del sistema

- Versión macOS 10.3 o posterior
- iOS de destino 9.3 o posterior

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        El paquete de CocoaPod para iOS está disponible para su descarga y uso con el entorno de desarrollo integrado (IDE) de <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 o posterior </a>. En primer lugar, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">descargue el archivo binario de CocoaPod </a>. Extraiga el pod en el mismo directorio para su uso previsto, cree un *Podfile* y enumere `pod` como `target`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS expone el SDK de iOS completo para los desarrolladores de .NET. Compile aplicaciones iOS totalmente nativas mediante C# en Visual Studio. Para más información, vea <a href="/xamarin/ios/" target="_blank">Xamarin.iOS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp; ❤️ &nbsp;         <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Recursos adicionales

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Código fuente de Objective-C en la guía de inicio rápido del SDK de Voz de iOS </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">Código fuente de Swift en la guía de inicio rápido del SDK de Voz de iOS </a>
