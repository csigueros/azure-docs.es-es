---
title: Procedimiento para etiquetar los datos para el Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Aprenda a etiquetar los datos para usarlos con Reconocimiento de entidades con nombre (NER) personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 2328971a659bd4d4b147f9a708c1b5910e8ce12c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091488"
---
# <a name="tag-your-data-for-custom-named-entity-recognition-ner-in-language-studio"></a>Etiquetado de los datos para el Reconocimiento de entidades con nombre (NER) personalizado en Language Studio

Antes de crear los modelos de extracción de entidades personalizados, debe tener datos etiquetados. Si los datos todavía no están etiquetados, puede hacerlo en Language Studio. Para etiquetar los datos, debe haber [creado un proyecto](../quickstart.md).

## <a name="prerequisites"></a>Prerrequisitos

Para poder etiquetar los datos, necesita lo siguiente:

* Un [proyecto creado](create-project.md) correctamente con una cuenta de Azure Blob Storage configurada
    * Datos del texto que [se ha cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#application-development-lifecycle) para más información.

## <a name="tag-your-data"></a>Etiquetado de los datos

Después de cargar los datos de entrenamiento en la cuenta de almacenamiento de Azure, tendrá que etiquetarlos para que el modelo sepa las palabras que estarán asociadas a las clases que necesita. Al etiquetar datos en Language Studio (o de forma manual), las etiquetas se almacenarán en [el formato JSON](../concepts/data-formats.md) que el modelo usará durante el entrenamiento.  

A medida que etiquete los datos, recuerde lo siguiente:

* **Etiquetar con precisión**: etiquete cada entidad en su tipo correcto siempre. Incluya solo lo que quiera extraer y evite datos innecesarios en la etiqueta.
* **Etiquetar de forma coherente**: la misma entidad debe tener la misma etiqueta en todos los archivos.
* **Etiquetar por completo**: etiquete todas las instancias de la entidad en todos los archivos.

La precisión, la coherencia y la integridad de los datos etiquetados son factores clave para determinar el rendimiento del modelo. Para etiquetar los datos:

1. Vaya a la página de proyectos en [Language Studio](https://aka.ms/custom-extraction) y seleccione el proyecto.

2. En el menú de la izquierda, seleccione **Tag data** (Etiquetar datos).

3. A la izquierda, encontrará una lista de todos los archivos `.txt` disponibles en los proyectos. Puede seleccionar el archivo que quiere empezar a etiquetar, o bien usar los botones **Atrás** y **Siguiente** de la parte inferior de la página para navegar.

4. Para iniciar el etiquetado, haga clic en **Add entities** (Agregar entidades) en la esquina superior derecha. Cambie la vista en el menú desplegable **Viewing** (Visualización) para ver todos los archivos o solo los etiquetados.

>[!TIP]
> * No hay un número estándar de etiquetas que necesite; considere la posibilidad de empezar con 50 etiquetas por entidad. El número de etiquetas que necesitará depende de lo distintas que sean las entidades y de la facilidad con que se puedan diferenciar entre sí. También depende del etiquetado, que debe ser coherente y completo.

:::image type="content" source="../media/tagging-screen.png" alt-text="Captura de pantalla en la que se muestra la pantalla de Language Studio para el etiquetado de datos." lightbox="../media/tagging-screen.png":::

Si ha habilitado varios idiomas para el proyecto, encontrará una lista desplegable **Language** (Idioma), que le permite seleccionar el idioma de cada documento.

Durante el etiquetado, los cambios se sincronizarán periódicamente; si aún no se han guardado, aparecerá una advertencia en la parte superior de la página. Si quiere guardarlos de forma manual, haga clic en el botón **Guardar etiquetas** en la parte superior de la página.

## <a name="tagging-options"></a>Opciones de etiquetado

Tiene dos opciones para etiquetar el documento:


|Opción |Descripción  |
|---------|---------|
|Etiquetado mediante un pincel     | Seleccione el icono de pincel situado junto a una entidad en la esquina superior derecha de la pantalla y, después, resalte las palabras del documento que quiera asociar a la entidad.           |
|Etiquetado mediante un menú    | Resalte la palabra que quiera etiquetar como entidad y aparecerá un menú. Seleccione la etiqueta que quiera asignar para esta entidad.        |

En la captura de pantalla siguiente se muestra el etiquetado mediante un pincel.

:::image type="content" source="../media/tag-options.png" alt-text="Captura de pantalla en la que se muestran las opciones de etiquetado que se ofrecen en NER personalizado." lightbox="../media/tag-options.png":::

## <a name="remove-tags"></a>Eliminación de etiquetas

Para quitar una etiqueta

1. Seleccione la entidad de la que quiera quitar una etiqueta.
2. Desplácese por el menú que aparece y seleccione **Quitar etiqueta**.

## <a name="delete-or-rename-entities"></a>Eliminación o cambio de nombre de entidades

Para eliminar o cambiar el nombre de una entidad:

1. Seleccione la entidad que quiera editar en la esquina superior derecha del menú.
2. Haga clic en los tres puntos situados junto a la entidad y seleccione la opción que quiera en el menú desplegable.


## <a name="next-steps"></a>Pasos siguientes

Después de etiquetar los datos, puede empezar a [entrenar un modelo](train-model.md) que aprenderá en función de los datos.
