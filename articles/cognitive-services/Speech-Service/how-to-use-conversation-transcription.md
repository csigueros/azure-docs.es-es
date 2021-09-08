---
title: 'Inicio rápido de la transcripción de conversaciones en tiempo real: servicio de Voz'
titleSuffix: Azure Cognitive Services
description: Aprenda a usar la transcripción de conversaciones en tiempo real con el SDK de voz. La transcripción de conversaciones le permite transcribir reuniones y otras conversaciones con la posibilidad de agregar, quitar e identificar varios participantes mediante streaming de audio al servicio de Voz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: pafarley
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: b3057bc9ee058ece0fd4a005c26ef8b95ec6a4b0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539090"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Introducción a la transcripción de conversaciones en tiempo real

La API **ConversationTranscriber** del SDK de voz le permite transcribir reuniones y conversaciones con la posibilidad de agregar, quitar e identificar varios participantes haciendo streaming de audio al servicio de voz mediante `PullStream` o `PushStream`. En primer lugar, cree firmas de voz para cada participante mediante la API REST y, a continuación, use las firmas de voz con el SDK para transcribir las conversaciones. Consulte la [información general](conversation-transcription.md) sobre transcripción de conversaciones para más información.

## <a name="limitations"></a>Limitaciones

* Solo está disponible en las siguientes regiones de suscripción: `centralus`, `eastasia`, `eastus` y `westeurope`
* Requiere una matriz con varios micrófonos circular de siete micrófonos. La matriz de micrófonos debe cumplir [nuestra especificación](./speech-devices-sdk-microphone.md).
* El [SDK de dispositivos de voz](speech-devices-sdk.md) proporciona dispositivos adecuados y una aplicación de ejemplo que muestra la transcripción de conversaciones.

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se da por sentado que tiene una cuenta de Azure y una suscripción al servicio de voz. Si no dispone de una cuenta y una suscripción, [pruebe el servicio de voz de forma gratuita](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Transcripción de conversaciones asincrónica](how-to-async-conversation-transcription.md)
> [Código de ejemplo del dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
> [Código de ejemplo del kit de desarrollo de Azure Kinect](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)