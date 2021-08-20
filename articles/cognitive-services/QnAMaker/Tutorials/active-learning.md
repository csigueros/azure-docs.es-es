---
title: Enriquecimiento de la base de conocimiento con el aprendizaje activo
description: En este tutorial, aprenderá a enriquecer las bases de conocimiento con el aprendizaje activo.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: f2af689bc0052decd91e8e8e5238ab88f828d261
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235335"
---
# <a name="enrich-your-knowledge-base-with-active-learning"></a>Enriquecimiento de la base de conocimiento con el aprendizaje activo

En este tutorial, se muestra cómo mejorar las bases de conocimiento con el aprendizaje activo. Si observa que los clientes hacen preguntas que no forman parte de la base de conocimiento. A menudo, hay variaciones de preguntas que se parafrasean de manera diferente. 

Estas variaciones, cuando se agregan como preguntas alternativas al par PyR pertinente, ayudan a optimizar la base de conocimiento para responder a consultas de usuario reales. Puede agregar manualmente preguntas alternativas a los pares PyR mediante el editor. Al mismo tiempo, también puede usar la característica de aprendizaje activo para generar sugerencias de aprendizaje activo basadas en las consultas de los usuarios. Sin embargo, la característica de aprendizaje activo requiere que la base de conocimiento reciba tráfico regular de los usuarios para generar sugerencias.

## <a name="enable-active-learning"></a>Habilitación del aprendizaje activo
El aprendizaje activo está activado de forma predeterminada para la característica Respuesta a preguntas personalizadas. Sin embargo, debe actualizar manualmente la configuración del aprendizaje activo para la versión de disponibilidad general de QnA Maker. Puede encontrar más detalles aquí: [Activación del aprendizaje activo para preguntas alternativas](../how-to/use-active-learning.md#turn-on-active-learning-for-alternate-questions).

Para probar las sugerencias de aprendizaje activo, puede importar el archivo siguiente a la base de conocimiento: [SampleActiveLearning.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/SampleActiveLearning.tsv). Para más información sobre cómo importar a una base de conocimiento, consulte[Migración a una nueva base de conocimiento](migrate-knowledge-base.md).

## <a name="view-and-addreject-active-learning-suggestions"></a>Visualización y adición o rechazo de sugerencias de aprendizaje activo
Una vez que las sugerencias de aprendizaje activo están disponibles, se pueden ver en **View Options** > **Show active learning suggestions** (Ver opciones > Mostrar sugerencias de aprendizaje activo).

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla con los elementos Ver opciones y Mostrar sugerencias de aprendizaje activo resaltados en cuadros rojos]( ../media/active-learning/view-options.png) ]( ../media/active-learning/view-options.png#lightbox)

Al hacer clic en **Show active learning suggestions** (Mostrar sugerencias de aprendizaje activo), se habilita la opción para filtrar los pares PyR que tienen sugerencias. Si el aprendizaje activo está deshabilitado o no hay sugerencias, la opción **Show active learning suggestions** (Mostrar sugerencias de aprendizaje activo) estará deshabilitada.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla con las opciones de filtrado resaltadas en un cuadro rojo]( ../media/active-learning/filter-by-suggestions.png) ]( ../media/active-learning/filter-by-suggestions.png#lightbox)

Podemos optar por filtrar solo los pares PyR que tienen preguntas alternativas como sugiere el aprendizaje activo, por lo que se muestra la lista filtrada de pares PyR:

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla con la opción Ayuda con el Lápiz para Surface resaltada en un cuadro rojo]( ../media/active-learning/help.png) ]( ../media/active-learning/help.png#lightbox)


Ahora podemos aceptar estas sugerencias o rechazarlas mediante la marca de comprobación o la marca de verificación cruzada. Esto se puede hacer individualmente en cada par PyR o mediante la opción **Accept / Reject** (Aceptar / Rechazar) en la parte superior.

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla con la opción para aceptar o rechazar resaltada en rojo]( ../media/active-learning/accept-reject.png) ]( ../media/active-learning/accept-reject.png#lightbox)

La base de conocimiento no cambia a menos que decidamos agregar o editar las sugerencias como sugiere el aprendizaje activo. Por último, haga clic en Save and train (Guardar y entrenar) para guardar los cambios.

> [!NOTE] 
> Para comprobar la versión y la configuración del servicio para el aprendizaje activo, consulte el artículo sobre [cómo usar el aprendizaje activo](../how-to/use-active-learning.md).

## <a name="add-alternate-questions-using-editor"></a>Adición de preguntas alternativas mediante el editor

Aunque el aprendizaje activo sugiere automáticamente preguntas alternativas en función de las consultas de los usuarios que acceden a la base de conocimiento, también podemos agregar variaciones de una pregunta mediante el editor.
Seleccione el par PyR donde se va a agregar la pregunta alternativa y seleccione **Add alternative phrasing** (Agregar expresión alternativa).

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla con la opción para agregar una expresión alternativa resaltada en rojo ]( ../media/active-learning/add-alternative-phrasing.png) ]( ../media/active-learning/add-alternative-phrasing.png#lightbox)

Las preguntas alternativas agregadas al par PyR se muestran de la siguiente manera:

> [!div class="mx-imgBorder"]
> [ ![Captura de pantalla con el texto Dibujar con el lápiz resaltado en rojo]( ../media/active-learning/draw-with-pen.png) ]( ../media/active-learning/draw-with-pen.png#lightbox)

Al agregar preguntas alternativas junto con el aprendizaje activo, enriquecemos aún más la base de conocimiento con variaciones de una pregunta que ayudan a proporcionar la misma respuesta a una consulta de usuario similar.


> [!NOTE] 
> Las preguntas alternativas tienen muchas palabras irrelevantes, por lo que podrían no afectar a la precisión de la respuesta según lo previsto. Por lo tanto, si la única diferencia entre las preguntas alternativas se encuentra en las palabras irrelevantes, estas preguntas alternativas no son necesarias.

La lista de palabras irrelevantes se puede encontrar aquí: Lista de [palabras irrelevantes](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md).


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Mejora de la calidad de las respuestas con sinónimos](adding-synonyms.md)