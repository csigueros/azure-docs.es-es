---
title: Adición de conversaciones guiadas con solicitudes de turno múltiple
description: En este tutorial aprenderá a realizar conversaciones guiadas con solicitudes de varios turnos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 5c664dd2d08bb743ebe3e813808c559a30d901fc
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235297"
---
# <a name="add-guided-conversations-with-multi-turn-prompts"></a>Adición de conversaciones guiadas con solicitudes de turno múltiple

 En este tutorial, usaremos las [preguntas frecuentes del Lápiz para Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98) para crear una knowledge base.

En este ejemplo supongamos que los usuarios solicitan detalles adicionales sobre el producto Lápiz para Surface, en especial cómo solucionar problemas del Lápiz para Surface, pero no obtienen las respuestas correctas. Por lo tanto, agregamos más solicitudes para admitir escenarios adicionales y guiar a los usuarios hacia las respuestas correctas mediante solicitudes de varios turnos.

## <a name="view-qnas-with-context"></a>Visualización de preguntas y respuestas con contexto
Al crear la knowledge base para las [preguntas frecuentes del Lápiz para Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98), elegimos habilitar la extracción en varios turnos del documento de origen. Para más información, siga [Creación de una conversación de varios turnos a partir del documento](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure). Aquí se enumeran las solicitudes de varios turnos asociados a pares de pregunta y respuesta, que se pueden ver mediante **Mostrar contexto** en **Opciones de vista**.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla con opciones de vista que muestran el contexto descrito en cuadros rojos]( ../media/guided-conversations/show-context.png) ]( ../media/guided-conversations/show-context.png#lightbox)

Se muestra el árbol de contexto donde se muestran todas las solicitudes de seguimiento vinculadas a un par de pregunta y respuesta: 

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la página de preguntas frecuentes del Lápiz para Surface con respuestas a preguntas comunes]( ../media/guided-conversations/source.png) ]( ../media/guided-conversations/source.png#lightbox)

## <a name="add-new-qna-pair-with-follow-up-prompts"></a>Adición de un nuevo par de pregunta y respuesta con solicitudes de seguimiento

Para ayudar al usuario a resolver problemas con el Lápiz para Surface, agregamos solicitudes de seguimiento:

1.  Adición de un nuevo par de pregunta y respuesta con dos solicitudes de seguimiento
2.  Adición de una solicitud de seguimiento a una de las solicitudes recién agregadas

**Paso 1**: Agregue un nuevo par de pregunta y respuesta con dos solicitudes de seguimiento, **Comprobar compatibilidad** y **Check Pen Settings** (Comprobar configuración del Lápiz). Mediante el editor, hacemos clic en **Add QnA pair** (Agregar par de pregunta y respuesta) para agregar un nuevo par de pregunta y respuesta con una solicitud de seguimiento.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la interfaz de usuario con "Agregar par de pregunta y respuesta" resaltado en un cuadro rojo]( ../media/guided-conversations/add-pair.png) ]( ../media/guided-conversations/add-pair.png#lightbox)

Se crea una nueva fila en **Editorial**, donde se puede escribir el par de pregunta y respuesta, como se muestra a continuación:

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la interfaz de usuario con "Agregar solicitud de seguimiento" resaltado en un cuadro rojo]( ../media/guided-conversations/follow-up.png) ]( ../media/guided-conversations/follow-up.png#lightbox)

A continuación, agregamos una solicitud de seguimiento al par de pregunta y respuesta recién creado; para ello, seleccionamos **Add follow-up prompt** (Agregar solicitud de seguimiento). Al hacer clic allí, se rellenan los detalles de la solicitud, como se muestra:

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la interfaz de usuario con "Agregar solicitud de seguimiento" resaltado en un cuadro rojo]( ../media/guided-conversations/follow-up.png) ]( ../media/guided-conversations/follow-up.png#lightbox)

Indicamos **Comprobar compatibilidad** como "Texto para mostrar" para la solicitud e intentamos vincularlo a una pregunta y respuesta. Puesto que no hay ningún par de pregunta y respuesta relacionado disponible para vincular a la solicitud, cuando buscamos "Comprobar la compatibilidad del Lápiz para Surface", creamos un nuevo par de pregunta y respuesta al hacer clic en **Crear nuevo**. Luego de **Guardar** los cambios, se presenta la siguiente pantalla, donde se puede especificar un nuevo par de pregunta y respuesta para la solicitud de seguimiento, como se muestra a continuación:

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de una pregunta y respuesta sobre la comprobación de la compatibilidad del Lápiz para Surface]( ../media/guided-conversations/check-compatibility.png) ]( ../media/guided-conversations/check-compatibility.png#lightbox)

Del mismo modo, agregamos otra solicitud **Check Pen Settings** (Comprobar configuración del Lápiz) para ayudar al usuario a solucionar los problemas del Lápiz para Surface y agregarle un par de pregunta y respuesta.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de una pregunta y respuesta sobre la comprobación de la configuración del Lápiz para Surface]( ../media/guided-conversations/check-pen-settings.png) ]( ../media/guided-conversations/check-pen-settings.png#lightbox)

**Paso 2**: Agregue otra solicitud de seguimiento a la solicitud recién creada. Ahora agregamos "Replace Pen tips" (Reemplazar puntas para el Lápiz) como solicitud de seguimiento a la solicitud creada anteriormente "Check Pen Settings".

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de una pregunta y respuesta sobre cómo comprobar la configuración del Lápiz de Surface con un cuadro rojo alrededor de la información relacionada con el reemplazo de las puntas para el Lápiz]( ../media/guided-conversations/replace-pen-tips.png) ]( ../media/guided-conversations/replace-pen-tips.png#lightbox)

Por último, guardamos los cambios y probamos estas solicitudes en el panel Prueba: 

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla del panel de prueba de QnAMaker]( ../media/guided-conversations/test-pane.png) ]( ../media/guided-conversations/test-pane.png#lightbox)

Para una consulta de usuario **Issues with Surface Pen** (Problemas con el Lápiz de Surface), el sistema devuelve una respuesta y presenta al usuario las solicitudes recién agregadas. A continuación, el usuario selecciona uno de las solicitudes **Check Pen Settings** y se devuelve al usuario la respuesta relacionada con otra solicitud **Replace Pen Tips**, que cuando se selecciona proporciona más información al usuario. Por lo tanto, se usan varios turnos para ayudar al usuario y guiarlo hacia la respuesta deseada.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Enriquecimiento de la knowledge base con el aprendizaje activo](active-learning.md)