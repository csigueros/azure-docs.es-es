---
title: 'Respuestas precisas mediante la detección del intervalo de respuestas: respuesta a preguntas'
description: Descripción de la característica de respuestas precisas disponible en respuesta a preguntas.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: d55c178456336c5a1c81845ae3eee611e8d9ee33
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093335"
---
# <a name="precise-answering"></a>Respuestas precisas

La característica de respuestas precisas introducida permite obtener la respuesta corta precisa del pasaje de la respuesta más adecuada presente en la base de conocimiento para cualquier consulta de usuario. Esta característica usa un modelo de aprendizaje profundo en tiempo de ejecución, que entiende la intención de la consulta del usuario y detecta la respuesta corta precisa en el pasaje de la respuesta, si hay una respuesta corta presente como hecho en el pasaje de la respuesta.

Esta característica es útil tanto para los desarrolladores de contenido como para los usuarios finales. Ahora, no es preciso que los desarrolladores de contenido mantengan manualmente pares de preguntas y respuestas específicos para cada hecho presente en la base de conocimiento, ni que el usuario final tenga que examinar todo el pasaje de la respuesta devuelto por el servicio para encontrar el hecho real que responde a la consulta del usuario.

## <a name="precise-answering-via-the-portal"></a>Respuesta precisa desde el portal

En el [portal de Language Studio](https://aka.ms/languageStudio), al abrir el panel de pruebas, verá una opción **Include short answer response** (Incluir respuesta a pregunta corta) en la parte superior sobre las opciones avanzadas.

Si escribe una consulta en el panel de pruebas, verá una respuesta corta junto con el pasaje de la respuesta, en caso de que haya una respuesta corta en el pasaje de la respuesta.

>[!div class="mx-imgBorder"]
>[![Captura de pantalla del panel de prueba con la respuesta corta activada y una pregunta que contiene una respuesta a pregunta corta.](../media/precise-answering/short-answer.png)](../media/precise-answering/short-answer.png#lightbox)

Puede anular la selección de la opción **Include short answer response** (Incluir respuesta a pregunta corta) si solo quiere ver el pasaje de **Respuesta larga** en el panel de pruebas.

El servicio también devuelve la puntuación de confianza de la respuesta precisa como una **puntuación de confianza del intervalo de respuesta**, que puede comprobar si selecciona la opción **Inspect** (Inspeccionar) y después **additional information** (información adicional).

>[!div class="mx-imgBorder"]
>[![Captura de pantalla del panel de inspección en el que se muestra la puntuación de confianza del intervalo de respuesta.](../media/precise-answering/answer-confidence-score.png)](../media/precise-answering/answer-confidence-score.png#lightbox)

## <a name="deploying-a-bot"></a>Implementación de un bot

Cuando se publica un bot, se obtiene de forma predeterminada la experiencia habilitada de la respuesta precisa en la aplicación, donde verá una respuesta corta junto con el pasaje de la respuesta. Consulte la referencia de API de la API REST para ver cómo usar la respuesta precisa (denominada AnswerSpan) en la respuesta. El usuario tiene la flexibilidad de elegir otras experiencias mediante la actualización de la plantilla a través del servicio de aplicaciones de bots.

## <a name="language-support"></a>Compatibilidad con idiomas

Actualmente, la característica de respuesta precisa solo se admite en inglés.
