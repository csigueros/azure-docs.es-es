---
title: Adición de conversaciones guiadas con solicitudes de turno múltiple
description: En este tutorial aprenderá a realizar conversaciones guiadas con solicitudes de varios turnos.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 952f642b20cab149547cee1443c7c2ca594e3b34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093638"
---
# <a name="add-guided-conversations-with-multi-turn-prompts"></a>Adición de conversaciones guiadas con solicitudes de turno múltiple

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Agregar nuevos pares de preguntas y respuestas al proyecto existente
> * Agregar avisos de seguimiento para crear conversaciones guiadas
> * Prueba de los avisos de varios turnos

## <a name="prerequisites"></a>Prerrequisitos

 En este tutorial, usaremos las [preguntas frecuentes del Lápiz para Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98) para crear una knowledge base.

Si nunca ha creado un proyecto de respuesta a preguntas antes, se recomienda comenzar con el artículo de [introducción](../how-to/create-test-deploy.md), que le llevará paso a paso por el proceso.

## <a name="view-question-answer-pair-context"></a>Visualización del contexto del par de respuestas a preguntas

En este ejemplo supongamos que los usuarios solicitan detalles adicionales sobre el producto Lápiz para Surface, en especial cómo solucionar problemas del Lápiz para Surface, pero no obtienen las respuestas correctas. Por lo tanto, agregamos más solicitudes para admitir escenarios adicionales y guiar a los usuarios hacia las respuestas correctas mediante solicitudes de varios turnos.

Para ver los mensajes de varios turnos asociados a pares de preguntas y respuestas, seleccione **Mostrar columnas** > **Contexto.** De forma predeterminada, ya debería estar habilitado en la página **Editar knowledge base** en la interfaz de respuesta a preguntas de Language Studio.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la interfaz de usuario mostrar columnas con el contexto resaltado en rojo]( ../media/guided-conversations/context.png) ]( ../media/guided-conversations/context.png#lightbox)

Se muestra el árbol de contexto donde se muestran todas las solicitudes de seguimiento vinculadas a un par de pregunta y respuesta: 

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla de la página de preguntas frecuentes del Lápiz para Surface con respuestas a preguntas comunes]( ../media/guided-conversations/surface-source.png) ]( ../media/guided-conversations/surface-source.png#lightbox)

## <a name="add-question-pair-with-follow-up-prompts"></a>Adición de un par de preguntas con solicitudes de seguimiento

Para ayudar a los usuarios a resolver problemas con el Lápiz para Surface, agregamos solicitudes de seguimiento:

- Adición de un nuevo par de preguntas con dos solicitudes de seguimiento
- Adición de una solicitud de seguimiento a una de las solicitudes recién agregadas

1. Adición de un nuevo par de pregunta y respuesta con dos solicitudes de seguimiento, **Comprobar compatibilidad** y **Comprobar configuración del Lápiz**. Mediante el editor, agregamos un par PyR con una solicitud de seguimiento al hacer clic en **Agregar par de preguntas**.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la interfaz de usuario con "Agregar par de preguntas" resaltado en un cuadro rojo]( ../media/guided-conversations/add-question.png)
    
    Se crea una nueva fila en **Editorial**, donde se puede escribir el par de pregunta y respuesta, como se muestra a continuación:
    
    |Campo|Valor|
    |-----|----|
    |Preguntas | Corrección de problemas con Surface |
    |Respuestas y avisos | Estas son algunas cosas que debe probar primero si Surface Pen no escribe, no abre aplicaciones o no se conecta a Bluetooth.|
    
2. A continuación, agregamos una solicitud de seguimiento al par de preguntas recién creadas; para ello, seleccionamos **Agregar solicitud de seguimiento**. Rellene los detalles de la solicitud como se muestra:
    
    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de la interfaz de usuario con "Agregar solicitud de seguimiento" resaltado en un cuadro rojo]( ../media/guided-conversations/add-prompts.png) ]( ../media/guided-conversations/add-prompts.png#lightbox)
    
    Indicamos **Comprobar compatibilidad** como "Texto para mostrar" para la solicitud e intentamos vincularlo a una pregunta y respuesta. Puesto que no hay ningún par de pregunta y respuesta relacionado disponible para vincular a la solicitud, cuando buscamos "Comprobar la compatibilidad del Lápiz para Surface", creamos un nuevo par de preguntas al hacer clic en **Crear enlace al nuevo par** y seleccione **Listo**. A continuación, seleccione **Save changes** (Guardar cambios).
    
    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de una pregunta y respuesta sobre la comprobación de la compatibilidad del Lápiz para Surface]( ../media/guided-conversations/compatability-check.png) ]( ../media/guided-conversations/compatability-check.png#lightbox)
    
3. Del mismo modo, agregamos otra solicitud **Comprobar configuración del Lápiz** para ayudar al usuario a solucionar los problemas del Lápiz para Surface y agregarle un par de preguntas.
    
    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de una pregunta y respuesta sobre la comprobación de la configuración del Lápiz para Surface]( ../media/guided-conversations/pen-settings.png) ]( ../media/guided-conversations/check-pen-settings.png#lightbox)

4. Agregue otra solicitud de seguimiento a la solicitud recién creada. Ahora agregamos "Replace Pen tips" (Reemplazar puntas para el Lápiz) como solicitud de seguimiento a la solicitud creada anteriormente "Check Pen Settings".

    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de una pregunta y respuesta sobre la creación de una solicitud de seguimiento para reemplazar las sugerencias de lápiz]( ../media/guided-conversations/replace-pen.png) ]( ../media/guided-conversations/replace-pen.png#lightbox)
    
5. Por último, guarde los cambios y pruebe estas solicitudes en el panel **Prueba**:
    
    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de la interfaz de usuario de respuesta de preguntas con guardar cambios resaltados en un cuadro rojo]( ../media/guided-conversations/save-changes.png) ]( ../media/guided-conversations/save-changes.png#lightbox)
    
    Para una consulta de usuario **Issues with Surface Pen** (Problemas con el Lápiz de Surface), el sistema devuelve una respuesta y presenta al usuario las solicitudes recién agregadas. A continuación, el usuario selecciona uno de las solicitudes **Check Pen Settings** y se devuelve al usuario la respuesta relacionada con otra solicitud **Replace Pen Tips**, que cuando se selecciona proporciona más información al usuario. Por lo tanto, se usan varios turnos para ayudar al usuario y guiarlo hacia la respuesta deseada.
    
    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla del interfaz de usuario de la prueba de chat]( ../media/guided-conversations/test.png) ]( ../media/guided-conversations/test.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Enriquecimiento de la knowledge base con el aprendizaje activo](active-learning.md)
