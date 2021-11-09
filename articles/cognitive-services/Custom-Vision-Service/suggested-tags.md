---
title: Etiquetado más rápido de imágenes con Smart Labeler
titleSuffix: Azure Cognitive Services
description: En esta guía, aprenderá a usar Smart Labeler para generar etiquetas sugeridas para las imágenes. Esto le permite etiquetar un gran número de imágenes más rápidamente al entrenar un modelo de Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: be1e97aead471012e6d0e92931d670306cb1ddf4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458311"
---
# <a name="label-images-faster-with-smart-labeler"></a>Etiquetado más rápido de imágenes con Smart Labeler

En esta guía, aprenderá a usar Smart Labeler para generar etiquetas sugeridas para las imágenes. Esto permite etiquetar un gran número de imágenes más rápidamente al entrenar un modelo de Custom Vision.

Al etiquetar las imágenes para un modelo de Custom Vision, el servicio utiliza la iteración entrenada más reciente del modelo para predecir las etiquetas de las imágenes sin etiquetar. Después, muestra estas predicciones como etiquetas sugeridas según el umbral de confianza seleccionado y la incertidumbre de la predicción. Después puede confirmar o cambiar las sugerencias y acelerar el proceso de etiquetado manual de las imágenes para el entrenamiento.

## <a name="when-to-use-smart-labeler"></a>Cuándo usar Smart Labeler

Tenga presentes las siguientes limitaciones:

* Solo debería solicitar etiquetas sugeridas para las imágenes cuyo contenido ya se ha entrenado una vez. No obtenga sugerencias para una nueva etiqueta que acaba de entrenar.

> [!IMPORTANT]
> Smart Labeler usa el mismo [modelo de precios](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) que las predicciones normales. La primera vez que desencadene etiquetas sugeridas para un conjunto de imágenes, se le cobrará lo mismo que para las llamadas de predicción. Después, el servicio almacena los resultados de las imágenes seleccionadas en una base de datos durante 30 días y puede acceder a ellas en cualquier momento y de forma gratuita dentro de ese período. Después de 30 días, se le cobrará si solicita de nuevo sus etiquetas sugeridas.

## <a name="smart-labeler-workflow"></a>Flujo de trabajo de Smart Labeler

Estos pasos le muestran cómo usar Smart Labeler:

1. Cargue todas las imágenes de entrenamiento en el proyecto de Custom Vision.
1. Etiquete parte del conjunto de datos y elija un número igual de imágenes para cada etiqueta.
    > [!TIP]
    > Asegúrese de usar todas las etiquetas de las que desea obtener sugerencias más adelante.
1. Inicie el proceso de entrenamiento.
1. Una vez completado el entrenamiento, vaya a la vista **Sin etiqueta** y seleccione el botón **Get suggested tags** (Obtener etiquetas sugeridas) en el panel izquierdo.
    > [!div class="mx-imgBorder"]
    > ![El botón de etiquetas sugeridas se muestra en la pestaña de imágenes sin etiquetar.](./media/suggested-tags/suggested-tags-button.png)
1. En la ventana emergente que aparece, establezca el número de imágenes para las que quiere obtener sugerencias. Solo debe obtener sugerencias de las etiquetas iniciales para una parte de las imágenes sin etiquetar. Obtendrá mejores sugerencias de etiqueta a medida que recorra en iteración este proceso.
1. Confirme las etiquetas sugeridas y corrija las que no sean correctas.
    > [!TIP]
    > Las imágenes con etiquetas sugeridas se ordenan por su incertidumbre de predicción (los valores más bajos indican una mayor confianza). Puede cambiar el criterio de ordenación con la opción **Sort by uncertainty** (Ordenar por incertidumbre). Si establece el orden en **alto a bajo**, puede corregir primero las predicciones de alta incertidumbre y, después, confirmar rápidamente las incertidumbres bajas.
    * En los proyectos de clasificación de imágenes, puede seleccionar y confirmar las etiquetas en lotes. Filtre la vista por una etiqueta sugerida determinada, anule la selección de las imágenes que se etiqueten incorrectamente y, después, confirme el resto en un lote.
        > [!div class="mx-imgBorder"]
        > ![Las etiquetas sugeridas se muestran en modo por lotes para IC con filtros.](./media/suggested-tags/ic-batch-mode.png)

        También puede usar etiquetas sugeridas en el modo de imagen individual mediante la selección de una imagen de la galería.

        ![Las etiquetas sugeridas se muestran en modo de imagen individual para IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * En los proyectos de detección de objetos, no se admiten las confirmaciones por lotes, pero todavía puede filtrar y ordenar por etiquetas sugeridas para una experiencia de etiquetado más organizada. Las miniaturas de las imágenes no etiquetadas mostrarán una superposición de los cuadros de límite que indican las ubicaciones de las etiquetas sugeridas. Si no selecciona un filtro de etiqueta sugerido, todas las imágenes no etiquetadas aparecerán sin que se superpongan los cuadros de límite.
        > [!div class="mx-imgBorder"]
        > ![Las etiquetas sugeridas se muestran en modo por lotes para OD con filtros.](./media/suggested-tags/od-batch-mode.png)

        Para confirmar las etiquetas de detección de objetos, debe aplicarlas a cada imagen individual de la galería.

        ![Las etiquetas sugeridas se muestran en modo de imagen individual para OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Inicie de nuevo el proceso de entrenamiento.
1. Repita los pasos anteriores hasta que esté satisfecho con la calidad de la sugerencia.

## <a name="next-steps"></a>Pasos siguientes

Siga un inicio rápido para empezar a crear y entrenar un proyecto de Custom Vision.

* [Compilación de un clasificador](getting-started-build-a-classifier.md)
* [Creación de un detector de objetos](get-started-build-detector.md)