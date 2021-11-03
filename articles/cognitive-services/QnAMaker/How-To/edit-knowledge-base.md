---
title: 'Edición de una base de conocimiento: QnA Maker'
description: QnA Maker le permite administrar el contenido de la base de conocimiento, proporcionando una experiencia de edición sencilla.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b67744b66eaf5563505f72b6b151f40ed44f602
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069242"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Edición de pares de QnA en la base de conocimiento

QnA Maker le permite administrar el contenido de la base de conocimiento, proporcionando una experiencia de edición sencilla.

Los pares de QnA se agregan desde un origen de datos, como un archivo o una dirección URL, o lo hacen como un origen editorial. Un origen editorial indica que el par de QnA se agregó manualmente en el portal de QnA. Todos los pares de QnA están disponibles para su edición.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="add-an-editorial-qna-set"></a>

## <a name="question-and-answer-pairs"></a>Pares de preguntas y respuestas

Una base de conocimiento consta de pares de pregunta y respuesta (PyR).  Cada par contiene una respuesta, además de toda la información asociada a dicha _respuesta_. Una respuesta se parece un poco a una fila de base de datos o una instancia de estructura de datos. La configuración **obligatoria** en un par de pregunta y respuesta (PyR) son:

* una **pregunta**: texto de una consulta de usuario que se usa para que el aprendizaje automático de QnA Maker lo alinee con el texto de las preguntas de usuarios formuladas de forma distinta, pero con la misma respuesta.
* la **respuesta**: respuesta del par que se devuelve cuando una consulta de usuario coincide con la pregunta asociada.

Cada par se representa mediante un **id.**

La configuración **opcional** de un par incluye:

* **Formas alternativas de la pregunta**: esto ayuda a que QnA Maker devuelva la respuesta correcta para una variedad más amplia de estructuras de preguntas.
* **Metadatos**: los metadatos son etiquetas asociadas a un par de QnA y se representan como pares de clave-valor. Las etiquetas de metadatos se usan para filtrar los pares de QnA y limitar el conjunto sobre el que se realiza la coincidencia de la consulta.
* **Avisos multiturno**, que se usan para continuar una conversación de varios turnos.

![Bases de conocimiento de QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="add-an-editorial-qna-pair"></a>Adición de un par de QnA editorial

Si no tiene contenido creado previamente para rellenar la base de conocimiento, puede redactar y agregar pares de QnA en el portal de QnA Maker.

1. Inicie sesión en el [portal de QnA](https://www.qnamaker.ai/) y, luego, seleccione la base de conocimiento en la que quiere agregar el par de QnA.
1. En la página **EDIT** (EDITAR) de la base de conocimiento, seleccione **Add QnA pair** (Agregar par de QnA) para agregar un nuevo par de QnA.

    > [!div class="mx-imgBorder"]
    > ![Adición de un par de QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. En la fila del nuevo par de QnA, agregue los campos de pregunta y respuesta obligatorios. Todos los demás campos son opcionales. Todos los campos se pueden cambiar en cualquier momento.

1. También tiene la opción de agregar **[frases alternativas](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)** . Las frases alternativas son cualquier forma de la pregunta que sea bastante diferente de la pregunta original, pero deben proporcionar la misma respuesta.

    Cuando se publica la base de conocimiento y se habilita el [aprendizaje activo](use-active-learning.md), QnA Maker recopila opciones de frases alternativas para que las acepte. Estas opciones se seleccionan para aumentar la precisión de la predicción.

1. También tiene la opción de agregar **[metadatos](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)** . Para ver los metadatos, seleccione **View options** (Ver opciones) en el menú contextual. Los metadatos proporcionan filtros para las respuestas que facilita la aplicación cliente, como un bot de chat.

1. También tiene la opción de agregar **[avisos de seguimiento](multiturn-conversation.md)** . Los avisos de seguimiento proporcionan rutas de conversación adicionales a las que presenta la aplicación cliente al usuario.

1. Seleccione **Save and train** (Guardar y entrenar) para ver las predicciones que incluye el nuevo par de QnA.

## <a name="rich-text-editing-for-answer"></a>Edición de texto enriquecido para la respuesta

La edición de texto enriquecido del texto de respuesta le permite aplicar estilos a Markdown desde una barra de herramientas simple.

1. Seleccione el área de texto de una respuesta, se muestra la barra de herramientas del editor de texto enriquecido en la fila del par de QnA.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla del editor de texto enriquecido con la pregunta y la respuesta de una fila de par de QnA.](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    Cualquier texto que ya esté en la respuesta se muestra correctamente, ya que el usuario lo verá desde un bot.

1. Modifique el texto. Seleccione características de formato en la barra de herramientas de edición de texto enriquecido o use la característica de alternancia para cambiar a la sintaxis de Markdown.

    > [!div class="mx-imgBorder"]
    > ![Use el editor de texto enriquecido para escribir y dar formato al texto y guardarlo como Markdown.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Características del editor de texto enriquecido|Métodos abreviados de teclado|
    |--|--|
    |Alternar entre el editor de texto enriquecido y Markdown. `</>`|CTRL+M|
    |Negrita. **B**|CTR+LB|
    |Cursiva, se indica con una **_I_** en cursiva|CTRL+I|
    |Lista sin ordenar||
    |Lista ordenada||
    |Estilo de párrafo||
    |Imagen: agregar una imagen disponible en una dirección URL pública.|CTRL+G|
    |Agregar vínculo a una dirección URL disponible públicamente.|CTRL+K|
    |Emoticono: agregar desde una selección de emoticonos.|CTRL+E|
    |Menú avanzado: deshacer|CTRL+Z|
    |Menú avanzado: rehacer|CTRL+Y|

1. Agregue una imagen a la respuesta con el icono de imagen de la barra de herramientas de texto enriquecido. El editor en contexto necesita la dirección URL de la imagen de acceso público y el texto alternativo para la imagen.


    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla que muestra el editor en contexto con la dirección URL de la imagen de acceso público y el texto alternativo de la imagen especificados.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Para agregar un vínculo a una dirección URL, seleccione el texto de la respuesta y luego el icono de vínculo en la barra de herramientas o seleccione el icono de vínculo en la barra de herramientas y escriba el nuevo texto y la dirección URL.

    > [!div class="mx-imgBorder"]
    > ![Use el editor de texto enriquecido para agregar una imagen de acceso público y su texto alternativo.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>Edición de un par de QnA

Se puede editar cualquier campo de cualquier par de QnA, sin importar el origen de datos original. Puede que algunos campos no estén visibles debido a la configuración actual de **View Options** (Ver opciones), que se encuentra en la barra de herramientas de contexto.

## <a name="delete-a-qna-pair"></a>Eliminación de un par de QnA

Para eliminar un QnA, haga clic en el icono **eliminar** en el extremo derecho de la fila QnA. Esta es una operación permanente. No se puede deshacer. Considere la posibilidad de exportar la base de conocimiento desde la página **Publish** (Publicar) antes de eliminar los pares.

![Eliminación de un par de QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Búsqueda del identificador del par de QnA

Si necesita encontrar el identificador del par de QnA, puede buscarlo en dos lugares:

* Mantenga el puntero sobre el icono de eliminación en la fila del par de QnA que le interese. El texto de navegación incluye el identificador del par de QnA.
* Exporte la base de conocimiento. Cada par de QnA de knowledge base incluye el identificador del par de QnA.

## <a name="add-alternate-questions"></a>Agregar preguntas alternativas

Agregue preguntas alternativas a un par de QnA existente para mejorar la probabilidad de encontrar una coincidencia para una consulta de usuario.

![Agregar preguntas alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Vinculación de pares de QnA

La vinculación de pares de QnA se proporciona con [avisos de seguimiento](multiturn-conversation.md). Esta es una conexión lógica entre los pares de QnA, administrada en el nivel de la base de conocimiento. Puede editar los avisos de seguimiento en el portal de QnA Maker.

No se pueden vincular pares de QnA en los metadatos de la respuesta.

## <a name="add-metadata"></a>Agregar metadatos

Para agregar pares de metadatos, seleccione primero **View options** (Ver opciones) y, luego, seleccione **Show metadata** (Mostrar metadatos). Como resultado, se muestra la columna de metadatos. A continuación, seleccione el signo **+** para agregar un par de metadatos. Este par consta de una clave y un valor.

Obtenga más información sobre los metadatos en el inicio rápido del portal de QnA Maker sobre metadatos:
* [Creación: adición de metadatos a un par de QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Predicción de consultas: filtrar respuestas por metadatos](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>Guardar los cambios en los pares de QnA

Seleccione periódicamente **Save and train** (Guardar y entrenar) después de realizar modificaciones para evitar perder los cambios.

![Agregar metadatos](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Cuándo usar la edición de texto enriquecido frente a Markdown

La [edición de texto enriquecido](#add-an-editorial-qna-set) de las respuestas le permite, como autor, usar una barra de herramientas de formato para seleccionar y dar formato al texto rápidamente.

[Markdown](../reference-markdown-format.md) es una mejor herramienta cuando se necesita generar contenido automáticamente para crear bases de conocimiento que se van a importar como parte de una canalización de CI/CD o para [pruebas por lotes](../index.yml).

## <a name="editing-your-knowledge-base-locally"></a>Edición de la base de conocimiento localmente

Una vez que se crea una base de conocimiento, es recomendable realizar modificaciones en el texto de dicha base en el [portal de QnA Maker](https://qnamaker.ai), en lugar de exportar y volver a importar a través de archivos locales. Sin embargo, puede haber ocasiones en las que necesite editar una base de conocimiento localmente.

Exporte la base de conocimiento desde la página **Configuración** y luego edite dicha base con Microsoft Excel. Si decide utilizar otra aplicación para editar el archivo exportado, la aplicación puede introducir errores de sintaxis porque no es totalmente compatible con TSV. Por lo general, los archivos TSV de Microsoft Excel no introducen errores de formato.

Cuando termine de realizar las modificaciones, vuelva a importar el archivo TSV desde la página **Configuración**. Esto reemplazará completamente la base de conocimiento actual por la base de conocimiento importada.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Colaborar en una base de conocimiento](../index.yml)

* [Administración de recursos de Azure que se usan en QnA Maker](set-up-qnamaker-service-azure.md)
