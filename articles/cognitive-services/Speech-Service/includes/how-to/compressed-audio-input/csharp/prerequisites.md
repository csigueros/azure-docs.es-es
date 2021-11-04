---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: eur
ms.openlocfilehash: 3df7dc7e5eeb71dc046ea860927e98bb72b45231
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520151"
---
El control del audio comprimido se implementa mediante [GStreamer](https://gstreamer.freedesktop.org). Por motivos de licencia, los binarios de GStreamer no se compilan ni se vinculan con el SDK de Voz. Los desarrolladores deben instalar varias dependencias y complementos; consulte [Instalación en Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) o [Instalación en Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Los archivos binarios de GStreamer deben estar en la ruta de acceso del sistema, con el fin de que el SDK de Voz pueda cargarlos en tiempo de ejecución. Por ejemplo, en Windows, si el SDK de Voz es capaz de encontrar `libgstreamer-1.0-0.dll` o `gstreamer-1.0-0.dll` (en la versión más reciente de GStreamer) durante el tiempo de ejecución, significa que los archivos binarios de GStreamer se encuentran en la ruta de acceso del sistema.

