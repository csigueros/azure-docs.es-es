---
author: laujan
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: lajanuar
ms.openlocfilehash: 6d00bf01bcbcc344e4f066f3dab7d140af042a94
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071861"
---
En este inicio rápido, usará el [SDK de Voz](~/articles/cognitive-services/speech-service/speech-sdk.md) y el servicio Language Understanding (LUIS) para reconocer los intentos de los datos de audio capturados de un micrófono. En concreto, usará el SDK de Voz para capturar la voz y un dominio predefinido de LUIS para identificar las intenciones de automatización doméstica, como encender y apagar una luz. 

Tras cumplir algunos requisitos previos, no es preciso seguir muchos pasos para el reconocimiento de voz y la identificación de intenciones a través de un micrófono:

> [!div class="checklist"]
>
> * Cree un objeto `SpeechConfig` a partir de la clave y la región de suscripción.
> * Cree un objeto `IntentRecognizer` con el objeto `SpeechConfig` anterior.
> * Con el objeto `IntentRecognizer`, inicie el proceso de reconocimiento de una única expresión.
> * Inspeccione el objeto `IntentRecognitionResult` devuelto.

