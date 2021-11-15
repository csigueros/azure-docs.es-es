---
title: 'Prueba y reentrenamiento de un modelo: Custom Vision Service'
titleSuffix: Azure Cognitive Services
description: Aprenda a probar una imagen y, a continuación, utilizarla para volver a entrenar el modelo en Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: b326a610d3ac946d8fd610b072e882df3a4b5281
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450464"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Prueba y reentrenamiento de un modelo con Custom Vision Service

Después de entrenar el modelo, puede probarlo rápidamente con una imagen almacenada de forma local o una imagen en línea. En la prueba se usa la iteración entrenada más recientemente de su modelo.

## <a name="test-your-model"></a>Comprobación del modelo

1. En la [página web de Custom Vision](https://customvision.ai), seleccione su proyecto. Seleccione **Quick Test** (Prueba rápida) a la derecha de la barra de menú superior. Esta acción abre una ventana llamada **Quick Test** (Prueba rápida).

    ![El botón Quick Test (Prueba rápida) se muestra en la esquina superior derecha de la ventana.](./media/test-your-model/quick-test-button.png)

2. En la ventana **Quick Test** (Prueba rápida), haga clic en el campo **Submit Image** (Enviar imagen) y escriba la dirección URL de la imagen que quiere usar para la prueba. Si en su lugar quiere usar una imagen almacenada localmente, haga clic en el botón **Browse local files** (Examinar archivos locales) y seleccione un archivo de imagen local.

    ![Imagen de la página de envío de imagen](./media/test-your-model/submit-image.png)

La imagen seleccionada aparece en el medio de la página. A continuación, los resultados aparecen debajo de la imagen en forma de una tabla con dos columnas, llamadas **Tags** (Etiquetas) y **Confidence** (Confianza). Después de ver los resultados, puede cerrar la ventana **Quick Test** (Prueba rápida).

Puede agregar ahora esta imagen de prueba al modelo y, a continuación, volver a entrenarlo.

## <a name="use-the-predicted-image-for-training"></a>Uso de la imagen prevista para el entrenamiento

Para usar la imagen enviada anteriormente para el entrenamiento, siga estos pasos:

1. Para ver las imágenes enviadas al clasificador, abra la [página web de Custom Vision](https://customvision.ai) y seleccione la pestaña __Predictions__ (Predicciones).

    ![Imagen de la pestaña de predicciones](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > La vista predeterminada muestra imágenes de la iteración actual. Puede usar el campo de lista desplegable __Iteración__ para ver las imágenes enviadas durante las iteraciones anteriores.

2. Mantenga el mouse sobre una imagen para ver las etiquetas previstas por el clasificador.

    > [!TIP]
    > Las imágenes se clasifican, y aquellas que pueden aportar más ganancias al clasificador se colocan en primer lugar. Para seleccionar una clasificación diferente, use la sección __Ordenar__.

    Para agregar una imagen a los datos de entrenamiento, seleccione la imagen, seleccione la etiqueta y, a continuación, seleccione __Guardar y cerrar__. La imagen se quita de __Predictions__ (Predicciones) y se agrega a las imágenes de entrenamiento. Para verla, seleccione la pestaña __Training Images__ (Imágenes de entrenamiento).

    ![Imagen de la página de etiquetado](./media/test-your-model/tag-image.png)

3. Use el botón __Entrenar__ para reentrenar el clasificador.

## <a name="next-steps"></a>Pasos siguientes

[Mejora del modelo](getting-started-improving-your-classifier.md)
