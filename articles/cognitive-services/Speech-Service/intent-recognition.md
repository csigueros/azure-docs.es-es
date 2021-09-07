---
title: 'Introducción a Reconocimiento de la intención: servicio Voz'
titleSuffix: Azure Cognitive Services
description: Reconocimiento de la intención le permite reconocer los objetivos de usuario que ha definido previamente. En este artículo encontrará información general sobre las ventajas y funcionalidades del servicio de reconocimiento de intenciones.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: reconocimiento de la intención
ms.openlocfilehash: c4a81c5f666b9cb42b642390ba79ca2a1bf7232b
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710081"
---
# <a name="what-is-intent-recognition"></a>¿Qué es el reconocimiento de intenciones?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

En esta introducción, descubrirá las ventajas y las funcionalidades del reconocimiento de intenciones. El SDK de Voz de Cognitive Services proporciona dos maneras de reconocer intenciones, que se describen a continuación. Una intención es algo que el usuario quiere hacer: reservar un vuelo, comprobar el tiempo o hacer una llamada. Con el reconocimiento de intenciones, las aplicaciones, herramientas y dispositivos pueden determinar lo que el usuario quiere iniciar o hacer en función de las opciones que defina en el reconocedor de intenciones o en LUIS.

## <a name="pattern-matching"></a>Detección de patrones
El SDK proporciona un buscador de coincidencias de patrones insertado que puede usar para reconocer intenciones de una manera muy estricta. Resulta útil cuando se necesita una solución sin conexión rápida. Funciona especialmente bien cuando el usuario se va a entrenar de algún modo o cuando se puede esperar que use frases específicas para desencadenar intenciones. Por ejemplo: "Ir a la séptima planta" o "Encender la luz", etc. Se recomienda empezar aquí y, si ya no satisface sus necesidades, cambiar al uso de LUIS o una combinación de los dos. 

## <a name="luis-language-understanding-intent-service"></a>LUIS (Language Understanding Intelligence Service)
El servicio LUIS de Microsoft está disponible como un servicio completo de intenciones de IA que funciona bien cuando el dominio de posibles intenciones es grande y no está realmente seguro de lo que dirá el usuario. Admite muchos escenarios complejos, intenciones y entidades.

### <a name="luis-key-required"></a>Se requiere la clave de LUIS

* LUIS se integra con el servicio Voz para reconocer las intenciones a partir de contenido de voz. No necesita una suscripción al servicio Voz, solo LUIS.
* El reconocimiento de intenciones de Voz se integra con el SDK de Voz. Puede usar una clave de LUIS con el servicio de voz.
* El reconocimiento de intenciones mediante el SDK de Voz se [ofrece en un subconjunto de regiones admitidas por LUIS](./regions.md#intent-recognition).

## <a name="get-started"></a>Introducción
Consulte este [procedimiento](how-to-use-simple-language-pattern-matching.md) para empezar a trabajar con la coincidencia de patrones.

Consulte este [inicio rápido](get-started-intent-recognition.md) para empezar a usar el reconocimiento de intenciones de LUIS.

## <a name="sample-code"></a>Código de ejemplo

Código de ejemplo para el reconocimiento de intenciones:

* [Inicio rápido: Uso de automatización del hogar compilada previamente](../luis/luis-get-started-create-app.md)
* [Reconocimiento de intenciones a partir de contenido de voz mediante el SDK de Voz para C#](./how-to-recognize-intents-from-speech-csharp.md)
* [Reconocimiento de la intención y otros servicios de Voz con Unity en C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Reconocimiento de la intención con el SDK de Voz para Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Reconocimiento de la intención y otros servicios de Voz con el SDK de Voz para C++ en Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Reconocimiento de la intención y otros servicios de Voz con el SDK de Voz para Java en Windows o Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Reconocimiento de la intención y otros servicios de Voz con el SDK de Voz para JavaScript en un explorador web](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Documentos de referencia

* [Acerca del SDK de Voz](./speech-sdk.md)

## <a name="next-steps"></a>Pasos siguientes

* Complete el [inicio rápido](get-started-intent-recognition.md) sobre reconocimiento de intenciones.
* [Obtenga una clave de suscripción gratuita a los servicios de Voz](overview.md#try-the-speech-service-for-free)
* [Obtención del SDK de voz](speech-sdk.md)
