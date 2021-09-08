---
title: 'Direct Line Speech: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Introducción a las características, las funcionalidades y las restricciones de los asistentes de voz mediante Direct Line Speech con el kit de desarrollo de software (SDK) de Voz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: pafarley
ms.openlocfilehash: 016f827a9f83aac6e460fe32ef26df6bbcf726f1
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544058"
---
# <a name="what-is-direct-line-speech"></a>¿Qué es Direct Line Speech?

**Direct Line Speech** es una solución sólida de un extremo a otro para crear un asistente de voz flexible y extensible. Se basa en la tecnología de Bot Framework y su canal Direct Line Speech, que está optimizado para la interacción de voz de entrada y salida con bots.

[Los asistentes de voz](voice-assistants.md) escuchan a los usuarios y realizan una acción en respuesta, lo que a menudo implica una respuesta. Utilizan [conversión de voz en texto](speech-to-text.md) para transcribir la voz del usuario y, a continuación, realizan una acción en función de su comprensión del lenguaje natural del texto. Esta acción suele incluir una respuesta hablada del asistente generada con la [conversión de texto en voz](text-to-speech.md).

Direct Line Speech ofrece los niveles más altos de personalización y sofisticación para los asistentes de voz. Está diseñado para escenarios de conversaciones no estructuradas, naturales o híbridas con finalización de tareas o uso de comandos y controles. Este alto grado de flexibilidad implica una mayor complejidad y los escenarios que se limitan a tareas bien definidas mediante la entrada de lenguaje natural quizá prefieran considerar los [comandos personalizados](custom-commands.md) para una experiencia de solución simplificada.

## <a name="getting-started-with-direct-line-speech"></a>Introducción a Direct Line Speech

Los primeros pasos para crear un asistente de voz con Direct Line Speech son [obtener una clave de suscripción de voz](overview.md#try-the-speech-service-for-free), crear un nuevo bot asociado a esa suscripción y conectar el bot al canal Direct Line Speech.

   ![Diagrama conceptual del flujo de servicio de orquestación de Direct Line Speech](media/voice-assistants/overview-directlinespeech.png "El flujo de canales de voz")

Para obtener una completa guía paso a paso sobre la creación de un asistente de voz sencillo mediante Direct Line Speech, vea [el tutorial para habilitar la voz en un bot mediante el SDK de voz y el canal Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

También se ofrecen inicios rápidos diseñados para que ejecute código y conozca las API rápidamente. Esta tabla incluye una lista de inicios rápidos sobre el asistente de voz organizados por idioma y plataforma.

| Guía de inicio rápido | Plataforma | Referencia de API |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Browse](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Browse](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Código de ejemplo

El código de ejemplo para crear un asistente de voz está disponible en GitHub. Estos ejemplos abarcan la aplicación cliente para conectarse al asistente en varios lenguajes de programación conocidos.

* [Ejemplos del asistente de voz (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [Tutorial: Habilitación del asistente de voz mediante el SDK de Voz, C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personalización

Los asistentes de voz creados mediante el servicio de voz pueden usar la amplia variedad de opciones de personalización disponibles para la [conversión de voz en texto](speech-to-text.md), la [conversión de texto en voz](text-to-speech.md) y la [selección de palabras claves personalizadas](./custom-keyword-basics.md).

> [!NOTE]
> Las opciones de personalización varían según el idioma o la configuración regional (consulte los [idiomas admitidos](./language-support.md)).

Direct Line Speech y su funcionalidad asociada para los asistentes de voz constituyen un complemento perfecto para la [solución Virtual Assistant y la plantilla empresarial](/azure/bot-service/bot-builder-enterprise-template-overview). Aunque Direct Line Speech puede funcionar con cualquier bot compatible, estos recursos ofrecen una línea de base reutilizable para experiencias conversacionales de alta calidad, así como habilidades y modelos complementarios comunes para comenzar rápidamente.

## <a name="reference-docs"></a>Documentos de referencia

* [Acerca del SDK de Voz](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/)

## <a name="next-steps"></a>Pasos siguientes

* [Obtenga una clave de suscripción gratuita a los servicios de Voz](overview.md#try-the-speech-service-for-free)
* [Obtención del SDK de voz](speech-sdk.md)
* [Creación e implementación de un bot básico](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [Obtención de la solución Virtual Assistant y la plantilla empresarial](https://github.com/Microsoft/AI)
