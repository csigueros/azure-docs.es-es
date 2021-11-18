---
title: 'Streaming de audio comprimido con códec mediante el SDK de voz: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo hacer secuencias de audio comprimido al servicio de voz con el SDK de voz. Disponible para C++, C#y Java para Linux, Java en Android y Objective-C en iOS.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-eight
ms.openlocfilehash: 1f04c8c525e094d5e0980c1683632d34f3372580
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551926"
---
# <a name="use-codec-compressed-audio-input"></a>Uso de entrada de audio comprimido con códec

Tanto el SDK de Voz como la CLI de Voz pueden aceptar formatos de audio comprimidos mediante GStreamer. GStreamer descomprime el audio antes de enviarlo mediante la conexión del servicio de Voz como PCM sin procesar.

Plataforma | Idiomas | Versión de GStreamer compatible
| :--- | ---: | :---:
Linux  | C++, C#, Java, Python y Go | [Distribuciones y arquitecturas de destino de Linux admitidas](~/articles/cognitive-services/speech-service/speech-sdk.md) 
Windows (excepto UWP) | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi) 
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/) 

## <a name="installing-gstreamer-on-linux"></a>Instalación de GStreamer en Linux

Para más información, consulte las [instrucciones de instalación de Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c).  

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```
## <a name="installing-gstreamer-on-windows"></a>Instalación de GStreamer en Windows

Para más información, consulte las [instrucciones de instalación de Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). 

* Cree la carpeta c:\gstreamer.
* Descargue el [instalador](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi). 
* Copie el instalador en c:\gstreamer.
* Abra PowerShell como administrador.
* Ejecute el siguiente comando en PowerShell:

```powershell
cd c:\gstreamer
msiexec /passive INSTALLLEVEL=1000 INSTALLDIR=C:\gstreamer /i gstreamer-1.0-msvc-x86_64-1.18.3.msi
```
* Agregue las variables del sistema GST_PLUGIN_PATH con el valor C:\gstreamer\1.0\msvc_x86_64\lib\gstreamer-1.0.
* Agregue las variables del sistema GSTREAMER_ROOT_X86_64 con el valor C:\gstreamer\1.0\msvc_x86_64.
* Agregue otra entrada en la variable path como C:\gstreamer\1.0\msvc_x86_64\bin.
* Reinicie el equipo

## <a name="using-gstreamer-in-android"></a>Uso de GStreamer en Android
En la pestaña Java anterior encontrará información sobre la compilación de libgstreamer_android.so 

Para más información, consulte las [instrucciones de instalación de Android](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c). 

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Versión del SDK de voz necesaria para la entrada de audio comprimido
* Se requiere el SDK de Voz, versión 1.10.0 o posteriores, para RHEL 8 y CentOS 8.
* Se requiere el SDK de Voz, versión 1.11.0 o posteriores, para Windows.
* SDK de Voz, versión 1.16.0 o posteriores, para la versión más reciente de GStreamer en Windows y Android.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer necesario para controlar el audio comprimido

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Ejemplo de código que usa una entrada de audio comprimido con códec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/examples.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a reconocer voz](./get-started-speech-to-text.md)
