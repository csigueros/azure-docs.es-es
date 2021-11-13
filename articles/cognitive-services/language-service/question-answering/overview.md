---
title: ¿Qué es la respuesta a preguntas?
description: La respuesta a preguntas es un servicio de procesamiento de lenguaje natural (NLP) basado en la nube que crea con facilidad una capa natural de conversación con los datos. Se puede usar para encontrar la respuesta más apropiada para una entrada de lenguaje natural determinada, desde la base de conocimiento personalizada (KB) de información.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
keywords: qna maker, low code chat bots, multi-turn conversations
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 8249fa4f276b7474740e2c8d882abc4e69c9487d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476497"
---
# <a name="what-is-question-answering"></a>¿Qué es la respuesta a preguntas?

La respuesta a preguntas proporciona procesamiento de lenguaje natural (NLP) basado en la nube que le permite crear una capa natural de conversación con los datos. Se utiliza para encontrar la respuesta más apropiada para una entrada de la knowledge base personalizada (KB) de información.

La respuesta a preguntas se usa normalmente para crear aplicaciones cliente de conversación, entre las que se incluyen aplicaciones de medios sociales, bots de chat y aplicaciones de escritorio habilitadas para voz. Se han agregado varias características nuevas, como la relevancia mejorada mediante un clasificador de aprendizaje profundo, las respuestas precisas y la compatibilidad con regiones de un extremo a otro.

Esta documentación contiene los siguientes tipos de artículos:

* Los [inicios rápidos](./quickstart/sdk.md) son instrucciones paso a paso que permiten realizar llamadas al servicio y obtener los resultados en un breve período de tiempo.
* Las [guías paso a paso](./how-to/manage-knowledge-base.md) contienen instrucciones para usar el servicio de maneras más específicas o personalizadas.
* Los [artículos conceptuales](./concepts/precise-answering.md) proporcionan explicaciones detalladas de la funcionalidad y las características del servicio.
* Los [**tutoriales**](./tutorials/bot-service.md) son guías más largas que muestran cómo usar el servicio como un componente en soluciones empresariales más amplias. 

## <a name="when-to-use-question-answering"></a>Cuándo usar la respuesta a preguntas

* **Cuando tiene información estática**: use la respuesta a preguntas cuando tenga información estática en la knowledge base de las respuestas. Esta base de conocimiento está personalizada para sus necesidades, que ha creado con documentos como archivos PDF y direcciones URL.
* **Si desea proporcionar la misma respuesta a una solicitud, una pregunta o un comando**: cuando distintos usuarios envían la misma pregunta, se devuelve la misma respuesta.
* **Si desea filtrar la información estática en función de la metainformación**: agregue etiquetas de [metadatos](./tutorials/multiple-domains.md) para proporcionar opciones de filtrado adicionales relacionadas con los usuarios de la aplicación cliente y la información. La información común de los metadatos incluye el tipo de contenido o formato [de charla](./how-to/chit-chat.md), y el propósito y la actualización del contenido. <!--TODO: Fix Link-->
* **Si desea administrar una conversación de bot que incluya información estática**: la base de conocimiento toma el texto o el comando de conversación de un usuario y lo responde. Si la respuesta forma parte de un flujo de conversación determinado previamente, representado en la base de conocimiento con [contexto multiturno](./tutorials/guided-conversations.md), el bot puede proporcionar fácilmente este flujo.

## <a name="what-is-a-knowledge-base"></a>¿Qué es una base de conocimiento?

La respuesta a preguntas [importa el contenido](./how-to/manage-knowledge-base.md) en una knowledge base de pares de preguntas y respuestas. En el proceso de importación se extrae información sobre la relación entre las partes del contenido estructurado y semiestructurado para insinuar relaciones entre los conjuntos de preguntas y respuestas. Puede editar estos pares de preguntas y respuestas o agregar otros nuevos.

El contenido del par de preguntas y respuestas incluye:
* Todas las formas alternativas de la pregunta
* Etiquetas de metadatos utilizadas para filtrar las opciones de respuesta durante la búsqueda
* Indicaciones de seguimiento para continuar con el perfeccionamiento de la búsqueda

Después de publicar la base de conocimiento, una aplicación cliente envía una pregunta de usuario al punto de conexión. El servicio de respuesta a preguntas procesa la pregunta y responde con la mejor respuesta.

## <a name="create-a-chat-bot-programmatically"></a>Creación de un bot de chat mediante programación

Una vez publicada una knowledge base de respuesta a preguntas, una aplicación cliente envía una pregunta al punto de conexión de la knowledge base y recibe los resultados como una respuesta JSON. Una aplicación cliente común para responder preguntas es un bot de chat.

![Formulación de una pregunta a un bot y respuesta del contenido de la base de conocimiento](../../qnamaker/media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Paso|Acción|
|:--|:--|
|1|La aplicación cliente envía la _pregunta_  del usuario (texto en sus propias palabras) "Cómo actualizar mi base de conocimiento mediante programación?" al punto de conexión de la base de conocimiento.|
|2|La respuesta a preguntas usa la knowledge base entrenada para proporcionar la respuesta correcta y las solicitudes de seguimiento que se pueden usar para refinar la búsqueda de la mejor respuesta. La respuesta a preguntas devuelve una respuesta con formato JSON.|
|3|La aplicación cliente usa la respuesta JSON para tomar decisiones acerca de cómo continuar con la conversación. Estas decisiones pueden incluir mostrar la respuesta principal y presentar más opciones para refinar la búsqueda de la mejor respuesta. |
|||

## <a name="build-low-code-chat-bots"></a>Creación de bots de chat con poco código

El portal de Language Studio proporciona la experiencia de creación completa de proyecto y knowledge base. Puede importar documentos en su formulario actual a la base de conocimiento. Estos documentos (como las preguntas frecuentes, el manual del producto, la hoja de cálculo o la página web) se convierten en pares de preguntas y respuestas. En cada par se analizan los mensajes de seguimiento y cada par se conecta a otros pares. El formato de _marcado_ final admite una presentación enriquecida que incluye imágenes y vínculos.

Una vez editada la base de conocimiento, publíquela [en un bot de Azure Web App](https://azure.microsoft.com/services/bot-service/) en funcionamiento sin escribir ningún código. Pruebe el bot en [Azure Portal](https://portal.azure.com) o descárguelo y continúe el desarrollo.

## <a name="high-quality-responses-with-layered-ranking"></a>Respuestas de alta calidad con clasificación por capas

El sistema de respuesta a preguntas usa un enfoque de clasificación por capas. Los datos se almacenan en la búsqueda de Azure, que también actúa como la primera capa de clasificación. Los resultados principales de la búsqueda de Azure se pasan en el modelo de reclasificación NLP de respuesta a preguntas para generar los resultados finales y la puntuación de confianza.

## <a name="multi-turn-conversations"></a>Conversaciones multiturno

La respuesta a preguntas proporciona solicitudes multiturno y aprendizaje activo para ayudarle a mejorar sus pares de preguntas y respuestas básicos.

**Los mensajes multiturno** le ofrecen la oportunidad de conectar pares de preguntas y respuestas. Esta conexión permite a la aplicación cliente proporcionar una respuesta superior y proporciona más preguntas para refinar la búsqueda de una respuesta final.

Una vez que la knowledge base recibe las preguntas de los usuarios en el punto de conexión publicado, la respuesta a preguntas aplica el **aprendizaje activo** a estas preguntas reales para sugerir cambios en la knowledge base y mejorar la calidad.

## <a name="development-lifecycle"></a>Ciclo de vida del desarrollo

La respuesta a preguntas permite la creación, el entrenamiento y la publicación, además de ofrecer permisos de colaboración para integrarse en el ciclo de vida de desarrollo completo.

> [!div class="mx-imgBorder"]
> ![Imagen conceptual del ciclo de desarrollo](../../qnamaker/media/qnamaker-overview-learnabout/development-cycle.png)

## <a name="complete-a-quickstart"></a>Completar una guía de inicio rápido

Ofrecemos guías de inicio rápido en los lenguajes de programación más populares, cuyo diseño individual le permite ejecutar el código en menos de 10 minutos.

* [Introducción a la biblioteca cliente de respuesta a preguntas](./quickstart/sdk.md)

## <a name="next-steps"></a>Pasos siguientes
La respuesta a preguntas proporciona todo lo que necesita para compilar, administrar e implementar la knowledge base personalizada.

> [!div class="nextstepaction"]
> [Revisar los últimos cambios](../whats-new.md)
