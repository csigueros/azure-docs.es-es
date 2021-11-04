---
title: 'Cómo etiquetar los datos para la clasificación personalizada: Azure Cognitive Services'
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo etiquetar los datos para usarlos con la API de clasificación de texto personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: bcfa81dc4cf65252179ecb6411ce67f2ba9bdce7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093196"
---
# <a name="tag-text-data-for-training-your-model"></a>Etiquetado de datos de texto para el entrenamiento de un modelo 

Antes de crear un modelo de clasificación de texto personalizado, debe haber etiquetado los datos. Si aún no lo ha hecho, puede etiquetarlos en Language Studio. Los datos etiquetados informan al modelo sobre cómo interpretar el texto y se usan para el entrenamiento y la evaluación.

## <a name="prerequisites"></a>Prerrequisitos

Para poder etiquetar los datos, necesita lo siguiente:
* [Un proyecto creado correctamente](create-project.md) con una cuenta de Azure Blob Storage configurada. 
* Los datos del texto que se [ha cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#application-development-lifecycle) para más información.

<!--Tagging your data will let you [train your model](train-model.md), [evaluate it](train-model.md), and use it to [classify text](call-api.md).-->

## <a name="tag-your-data"></a>Etiquetado de los datos

Después de cargar los datos de entrenamiento en la cuenta de almacenamiento de Azure, deberá etiquetarlos para que el modelo sepa las palabras que estarán asociadas a las clases que necesita. Al etiquetar datos en Language Studio (o etiquetarlos de forma manual), las etiquetas se almacenarán en [el formato JSON](../concepts/data-formats.md) que el modelo usará durante el entrenamiento. 

Al etiquetar los datos, tenga en cuenta lo siguiente:

* En general, cuanto más datos etiquetados haya mejores serán los resultados obtenidos, siempre que se hayan etiquetado de forma precisa.

* Aunque se recomienda tener alrededor de 50 archivos etiquetados por clase, no hay un número fijo que pueda garantizar un rendimiento inmejorable del modelo, ya que su rendimiento también depende de la posible ambigüedad del [esquema](design-schema.md) y de la calidad de los datos etiquetados.

Realice los pasos siguientes para etiquetar los datos:

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/custom-classification).

1. En el menú de la izquierda, seleccione **Tag data** (Etiquetar datos).

3. A la izquierda, encontrará una lista de todos los archivos .txt disponibles en los proyectos. Puede seleccionar el archivo que quiere empezar a etiquetar o bien usar los botones Atrás y Siguiente en la parte inferior de la página para navegar.  
    
4.  Cambie la vista en el menú desplegable **Visualización** para ver todos los archivos o solo aquellos etiquetados. 

    > [!NOTE]
    > Si ha habilitado varios idiomas para el proyecto, encontrará el menú desplegable adicional **Idioma**. Seleccione el idioma de cada documento.

5. Antes de empezar a etiquetar, agregue clases al proyecto desde la esquina superior derecha.


    :::image type="content" source="../media/tag-1.png" alt-text="Captura de pantalla que muestra la pantalla de etiquetado de datos" lightbox="../media/tag-1.png":::

6. Empiece a etiquetar los archivos.

    * **Clasificación de etiqueta única**: el archivo solo se puede etiquetar con una clase; para ello, seleccione uno de los botones de radio junto a la clase con la que quiere etiquetar este archivo.

      :::image type="content" source="../media/tag-single.png" alt-text="Captura de pantalla que muestra el menú de clasificación de etiqueta única" lightbox="../media/tag-single.png":::

    * **Clasificación de varias etiquetas**: el archivo se puede etiquetar con varias clases; para ello, seleccione todas las casillas aplicables junto a las clases con las que quiere etiquetar este archivo.

      :::image type="content" source="../media/tag-multi.png" alt-text="Captura de pantalla que muestra el menú de clasificación de varias etiquetas" lightbox="../media/tag-multi.png":::

Durante el etiquetado, los cambios se sincronizarán periódicamente; si aún no se han guardado, aparecerá una advertencia en la parte superior de la página. Si quiere guardarlos de forma manual, haga clic en el botón Guardar etiquetas en la parte superior de la página.

## <a name="remove-tags"></a>Eliminación de etiquetas

Si quiere quitar una etiqueta, anule la selección del botón situado junto a la clase.

## <a name="delete-or-classes"></a>Eliminación de clases

Para eliminar una clase o cambiarla de nombre:

1. Seleccione la clase que quiere editar en el menú de la derecha.
2. Haga clic en los tres puntos y seleccione la opción que quiera en el menú desplegable.

## <a name="next-steps"></a>Pasos siguientes

Después de etiquetar los datos, puede empezar a [entrenar un modelo](train-model.md) que aprenderá en función de los datos.
