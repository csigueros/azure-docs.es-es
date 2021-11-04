---
title: Incorporación de charlas a una knowledge base de respuesta a preguntas personalizada
titleSuffix: Azure Cognitive Services
description: La incorporación de charlas personales al bot lo hace más atractivo y conversacional al crear un proyecto. La respuesta a preguntas personalizada permite agregar fácilmente un conjunto previamente rellenado de las principales charlas a las knowledge bases.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2e46b7b877e3ce8e82782356339261ff5204bf26
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090819"
---
# <a name="use-chitchat-with-a-knowledge-base"></a>Uso de charlas con una knowledge base

La incorporación de charlas al bot lo hace más atractivo y conversacional. La característica de charla de la respuesta a preguntas personalizada permite agregar fácilmente un conjunto previamente rellenado de las principales charlas a la knowledge base (KB). Esto puede ser un punto de partida para la personalidad del bot y permite ahorrar el tiempo y el costo que supone escribirlas desde cero.

Este conjunto de datos tiene aproximadamente 100 escenarios de charla en la voz de varias personas, como profesional, amigable e ingenioso. Elija el rol que más se parezca a la voz del bot. Dada una consulta de usuario, la respuesta a preguntas intenta hacerla coincidir con la pregunta y respuesta de charla conocida más parecida.

A continuación se muestran algunos ejemplos de las distintas personalidades: Puede ver todos los [conjuntos de datos](https://github.com/microsoft/botframework-cli/blob/main/packages/qnamaker/docs/chit-chat-dataset.md) de personalidades junto con los detalles de las personalidades.

Para la consulta del usuario `When is your birthday?`, cada personalidad tiene una respuesta con estilo:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personalidad|Ejemplo|
|--|--|
|Profesional|La edad no va conmigo.|
|Descriptiva|Realmente no tengo una edad.|
|Ingenioso|No tengo caducidad.|
|Cariñoso|No tengo edad.|
|Entusiasta|Soy un bot, así que no tengo edad.|
||

## <a name="language-support"></a>Compatibilidad con idiomas

Los conjuntos de datos de charlas se admiten en los siguientes idiomas:

|Idioma|
|--|
|Chino|
|Inglés|
|Francés|
|Alemania|
|Italiano|
|Japonés|
|Coreano|
|Portugués|
|Español|

## <a name="add-chitchat-source"></a>Incorporación del origen de charla
Después de crear el proyecto, puede agregar orígenes de direcciones URL, archivos, así como charlas del panel **Administración de orígenes**.

> [!div class="mx-imgBorder"]
> ![Incorporación de charlas de origen](../media/chit-chat/add-source.png)

Elija la personalidad que quiera como base de la charla.

> [!div class="mx-imgBorder"]
> ![Menú de las diversas personalidades de charla](../media/chit-chat/personality.png)

## <a name="edit-your-chitchat-questions-and-answers"></a>Edición de las preguntas y respuestas de charla

Al editar la knowledge base, verá un nuevo origen para la charla, según la personalidad que haya seleccionado. Ahora puede agregar preguntas modificadas o editar las respuestas, como con cualquier otro origen.

> [!div class="mx-imgBorder"]
> ![Edición de pares de preguntas de charla](../media/chit-chat/edit-chit-chat.png)

Para activar y desactivar las vistas de contexto y metadatos, seleccione **Mostrar columnas** en la barra de herramientas.

## <a name="add-more-chitchat-questions-and-answers"></a>Incorporación de más preguntas y respuestas de charla

Puede agregar un nuevo par de preguntas de charla que no esté en el conjunto de datos predefinido. Asegúrese de que no va a duplicar un par de preguntas que ya esté presente en el conjunto de la charla. Cuando se agregan nuevos pares de preguntas de charla, se agregan al origen **Editorial**. Para asegurarse de que el clasificador comprende que se trata de charla, agregue el par clave-valor de metadatos "Editorial: chitchat", como se muestra en la imagen siguiente:

:::image type="content" source="../media/chit-chat/add-new-chit-chat.png" alt-text="Incorporación de pares de respuestas a preguntas de charla" lightbox="../media/chit-chat/add-new-chit-chat.png":::

## <a name="delete-chitchat-from-your-project"></a>Eliminación de charlas del proyecto

Seleccione el panel **Administración de orígenes** y elija el origen de charla. El origen de charla específico aparece como un archivo tsv, con el nombre de la personalidad seleccionado. Seleccione **Eliminar** en la barra de herramientas.

> [!div class="mx-imgBorder"]
> ![Eliminación del origen de charla](../media/chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Importación de una base de conocimiento](./migrate-knowledge-base.md)
