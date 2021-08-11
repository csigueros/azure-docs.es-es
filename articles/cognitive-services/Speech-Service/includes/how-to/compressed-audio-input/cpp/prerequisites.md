---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 745ff6fddb3b175b4b046be71668f0c85ddd1c63
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854525"
---
El control del audio comprimido se implementa mediante [GStreamer](https://gstreamer.freedesktop.org). Por motivos de licencia, los binarios de GStreamer no se compilan ni se vinculan con el SDK de Voz. Los desarrolladores deben instalar varias dependencias y complementos.

> [!NOTE]
> Para obtener información sobre una configuración general obligatoria en Linux, consulte los [requisitos del sistema y las instrucciones de configuración](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk).

# <a name="ubuntudebian"></a>[Ubuntu/Debian](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos"></a>[RHEL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> - En RHEL/CentOS 7 y RHEL/CentOS 8, en el caso de usar el formato comprimido "ANY", es necesario instalar más complementos de GStreamer si el complemento de formato de transmisión por secuencias de medios no está en los complementos instalados anteriormente. 


---