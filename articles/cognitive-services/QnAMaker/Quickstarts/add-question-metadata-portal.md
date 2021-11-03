---
title: Adición de preguntas y respuestas en el portal de QnA Maker
description: Este artículo le indica cómo agregar pares de preguntas y respuestas con metadatos para que los usuarios puedan encontrar la respuesta correcta a su pregunta.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9f7d1c7238e742ab8d04cf349bd8560db861b801
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080714"
---
# <a name="add-questions-and-answer-with-qna-maker-portal"></a>Adición de preguntas y respuestas con el portal de QnA Maker

Una vez creada una base de conocimiento, agregue pares de preguntas y respuestas (QnA) con metadatos para filtrar la respuesta. Las preguntas de la tabla siguiente son acerca de los límites de servicio de Azure, pero cada una tiene que ver con un servicio de Azure Search diferente.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="qna-table"></a>

|Par|Preguntas|Respuesta|Metadatos|
|--|--|--|--|
|N.° 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|N.° 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Una vez que los metadatos se agregan a un par de QnA, la aplicación cliente puede:

* Solicitar respuestas que solo coincidan con determinados metadatos
* Recibir todas las respuestas, pero procesarlas posteriormente en función de los metadatos de cada una


## <a name="prerequisites"></a>Prerrequisitos

* Complete el [inicio rápido anterior](./create-publish-knowledge-base.md).

## <a name="sign-in-to-the-qna-maker-portal"></a>Inicio de sesión en el portal de QnA Maker

1. Inicie sesión en el [portal de QnA Maker](https://www.qnamaker.ai).

1. Seleccione la base de conocimiento existente del [inicio rápido anterior](./create-publish-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Adición de preguntas alternativas adicionales

La base de conocimiento actual tiene los pares de QnA de solución de problemas de QnA Maker. Estos pares se crearon al agregar la dirección URL a la base de conocimiento durante el proceso de creación.

Cuando se importó esta dirección URL, solo se creó una pregunta con una respuesta. En este procedimiento, agregue preguntas adicionales.

1. En la página **Edit** (Editar), use el cuadro de texto de búsqueda situado encima de los pares de preguntas y respuestas para encontrar la pregunta `How large a knowledge base can I create?`.

1. En la columna **Question** (Pregunta), seleccione **+ Add alternative phrasing** (+Agregar expresión alternativa) y, luego, agregue cada nueva expresión, que se proporciona en la tabla siguiente.

    |Expresión alternativa|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Seleccione **Save and train** (Guardar y entrenar) para volver a entrenar la base de conocimiento.

1. Seleccione **Test** (Probar), escriba una pregunta parecida a la nueva expresión alternativa, pero no con las mismas palabras:

    `What GB size can a knowledge base be?`

    La respuesta correcta se devuelve en formato Markdown:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`

    Si selecciona **Inspect** (Inspeccionar) en la respuesta devuelta, puede ver que hay más respuestas que corresponden a la pregunta, pero no con el mismo nivel de confianza.

    No agregue todas las combinaciones posibles de expresiones alternativas. Active el [aprendizaje activo](../how-to/improve-knowledge-base.md) de QnA Maker para encontrar las expresiones alternativas que mejor ayuden a su base de conocimiento a satisfacer las necesidades de los usuarios.

1. Seleccione de nuevo **Test** (Probar) para cerrar la ventana de prueba.

## <a name="add-metadata-to-filter-the-answers"></a>Adición de metadatos para filtrar las respuestas

Agregar metadatos a un par de preguntas y respuestas permite a la aplicación cliente solicitar respuestas filtradas. Este filtro se aplica antes de que se apliquen los [clasificadores primero y segundo](../concepts/query-knowledge-base.md#ranker-process).

1. Agregue el segundo par de preguntas y respuestas, sin los metadatos, de la [primera tabla de este inicio rápido](#qna-table) y, luego, continúe con los pasos siguientes.

1. Seleccione **View options** (Ver opciones) y, luego, seleccione **Show metadata** (Mostrar metadatos).

1. Para el para de QnA que acaba de agregar, seleccione **Add metadata tags** (Agregar etiquetas de metadatos) y, luego, agregue el nombre de `service` y el valor de `search`. Su aspecto es similar a este: `service:search`.

1. Agregue otra etiqueta de metadatos con el nombre de `link_in_answer` y un valor de `false`. Su aspecto es similar a este: `link_in_answer:false`.

1. Busque la primera respuesta en la tabla, `How large a knowledge base can I create?`.

1. Agregue pares de metadatos para las mismas dos etiquetas de metadatos:

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    Ahora tiene dos preguntas con las mismas etiquetas de metadatos con valores diferentes.

1. Seleccione **Save and train** (Guardar y entrenar) para volver a entrenar la base de conocimiento.

1. Seleccione **Publish** (Publicar) en el menú superior para ir a la página de publicación.
1. Seleccione el botón **Publish** (Publicar) para publicar la base de conocimiento actual en el punto de conexión.
1. Después de publicar la base de conocimiento, continúe con el siguiente inicio rápido para más información sobre cómo generar una respuesta a partir de la base de conocimiento.

## <a name="what-did-you-accomplish"></a>¿Qué ha logrado?

Ha modificado la base de conocimiento para que admita más preguntas y ha proporcionado pares de nombre-valor para ayudar al filtrado durante la búsqueda de la respuesta principal o durante el procesamiento posterior, una vez devueltas las respuestas.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a continuar con el siguiente inicio rápido, elimine los recursos de QnA Maker y Bot Framework en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtención de una respuesta con Postman o cURL](get-answer-from-knowledge-base-using-url-tool.md)
