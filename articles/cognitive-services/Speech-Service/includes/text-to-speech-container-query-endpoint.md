---
title: Consulta del punto de conexión del contenedor Conversión de texto a voz
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: eur
ms.openlocfilehash: e81b2bfb8781b3c8d14d182d8d6da14b48484953
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501097"
---
El contenedor proporciona [API de punto de conexión basadas en REST](../rest-text-to-speech.md). Hay muchos [proyectos de código fuente de ejemplo](https://github.com/Azure-Samples/Cognitive-Speech-TTS) disponibles para las variaciones de lenguaje, marco y plataforma.

Con los contenedores de texto a voz estándar y texto a voz neuronal, debe utilizar como base la configuración regional y la voz de la etiqueta de imagen que descargó. Por ejemplo, si descargó la etiqueta `latest`, la configuración regional predeterminada es `en-US` y la voz `AriaNeural`. El argumento `{VOICE_NAME}` sería [`en-US-AriaNeural`](../language-support.md#neural-voices). Vea el SSML de ejemplo siguiente:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```
