---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: b40ba648cc35cf4c4900c6c0c504afdcd05fcd29
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502525"
---
## <a name="install-speech-sdk"></a>Instalación del SDK de Voz

El SDK de Voz para Linux se puede usar para crear aplicaciones de 64 bits y 32 bits. Las bibliotecas y los archivos de encabezado necesarios se pueden descargar como un archivo tar desde https://aka.ms/csspeech/linuxbinary.

Descargue e instale el SDK de la forma siguiente:

1. Seleccione el directorio al que desea extraer los archivos del SDK de Voz y configure la variable de entorno `SPEECHSDK_ROOT` para que apunte a ese directorio. Esta variable facilita la referencia al directorio en futuros comandos. Por ejemplo, si desea usar el directorio `speechsdk` en el directorio principal, use un comando similar al siguiente:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Cree el directorio si aún no existe.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Descargue y extraiga el archivo `.tar.gz` que contienen los archivos binarios del SDK de Voz:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Valide el contenido del directorio de nivel superior del paquete extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   La lista de directorios debe contener los archivos de avisos y licencias de terceros, así como un directorio `include` que contenga archivos de encabezado (`.h`) y un directorio `lib` que contenga bibliotecas.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]
