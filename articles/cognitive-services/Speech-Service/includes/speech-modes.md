---
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: eur
ms.openlocfilehash: 9481d0db9f21b90b38dd89cba028b5fd1059e03f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508179"
---
**Interactivo**
- Diseñado para escenarios de comandos y controles.
- Tiene un valor de tiempo de espera de segmentación de X.
- Al final de una expresión reconocida, el servicio detiene el procesamiento de audio desde ese identificador de solicitud y finaliza el turno. La conexión no se cierra.
- El límite máximo para el reconocimiento es de 20 s.
- La llamada de Carbon típica que se va a invocar es `RecognizeOnceAsync`.

**Conversación**
- Diseñado para los reconocimientos de larga duración.
- Tiene un valor de tiempo de espera de segmentación de Y. (Y != X)
- Procesará varias expresiones completas sin finalizar el turno.
- Finalizará el turno para un silencio demasiado grande.
- Carbon continuará con un nuevo identificador de solicitud y reproducirá el audio según sea necesario.
- El servicio se desconectará forzosamente después de 10 minutos de reconocimiento de voz.
- Carbon volverá a conectarse y reproducirá audio no confirmado.
- Se invoca en Carbon con `StartContinuousRecognition`.

**Dictado**
- Permite a los usuarios especificar la puntuación dictándola.
- Se invoca en Carbon especificando `EnableDictation` en el objeto `SpeechConfig` independientemente de la llamada de API que inicia el reconocimiento.
- El clúster de primera entidad devuelve mensajes de `speech.fragment` para los resultados intermedios, la tercera entidad devuelve mensajes de `speech.hypothesis`.
