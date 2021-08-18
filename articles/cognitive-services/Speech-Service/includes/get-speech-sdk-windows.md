---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: lajanuar
ms.custom: devx-track-csharp
ms.openlocfilehash: d25d88f3c58310d1f1d1b9d4077355784ed7bc03
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067803"
---
:::row:::
    :::column span="3":::
        El SDK de voz admite Windows 10 y Windows Server 2016, o las versiones posteriores. Las versiones anteriores **no** se admiten oficialmente. Es posible usar partes del SDK de voz con versiones anteriores de Windows, aunque no se recomienda.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Requisitos del sistema

El SDK de voz en Windows requiere <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ Redistributable para Visual Studio 2019</a> en el sistema.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Instalación para x86 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Instalación para x64 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Instalación para ARMx64 </a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Para la entrada de micrófono, las bibliotecas de Media Foundation deben estar instaladas. Estas bibliotecas forman parte de Windows 10 y Windows Server 2016. Es posible usar Speech SDK sin estas bibliotecas, siempre y cuando no se use un micrófono como dispositivo de entrada de audio.

Los archivos necesarios del SDK de Voz se pueden implementar en el mismo directorio que la aplicación. De esta forma la aplicación puede acceder directamente a las bibliotecas. Asegúrese de seleccionar la versión correcta (x86/x64) que coincida con la aplicación.

| Nombre                                            | Función                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | SDK básico, necesario para la implementación nativa y administrada |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Necesario para la implementación administrada                      |

> [!NOTE]
> A partir de la versión 1.3.0, ya no es necesario incluir el archivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (incluido en versiones anteriores). La funcionalidad está ahora integrada en el SDK principal.

> [!IMPORTANT]
> Para el proyecto C# de la aplicación Windows Forms (.NET Framework), asegúrese de que las bibliotecas estén incluidas en la configuración de implementación de su proyecto. Puede comprobar esto en `Properties -> Publish Section`. Haga clic en el botón `Application Files` y busque las bibliotecas correspondientes en la lista desplegable. Asegúrese de que el valor esté establecido en `Included`. Visual Studio incluirá el archivo cuando se publique o implemente el proyecto.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
