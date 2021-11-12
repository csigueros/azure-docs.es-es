---
title: Preguntas frecuentes sobre la clasificación de texto personalizada
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre las preguntas más frecuentes al usar la API de clasificación de texto personalizada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 9320fee8eb7ad44b33246f28091dfb0b4bd72bb2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434396"
---
# <a name="frequently-asked-questions"></a>Preguntas más frecuentes

Encuentre respuestas a preguntas más frecuentes sobre conceptos y escenarios relacionados con la clasificación de texto personalizada en Azure Cognitive Service for Language.

## <a name="how-do-i-get-started-with-the-service"></a>¿Cómo puedo empezar a usar el servicio?

Consulte el [inicio rápido](./quickstart.md) para crear rápidamente su primer proyecto o ver [cómo crear proyectos](how-to/create-project.md) para obtener más detalles.

## <a name="what-are-the-service-limits"></a>¿Cuáles son los límites de servicio?

Para obtener más información, consulte el [artículo sobre límites del servicio](service-limits.md).

## <a name="which-languages-are-supported-in-this-feature"></a>¿Qué idiomas se admiten en esta característica?

Consulte el artículo sobre [compatibilidad de lenguaje](./language-support.md).

## <a name="how-many-tagged-files-are-needed"></a>¿Cuántos archivos etiquetados se necesitan?

Por lo general, los [datos etiquetados](how-to/tag-data.md) diversos y representativos generan mejores resultados, dado que el etiquetado se realiza de forma precisa, coherente y completa. No hay ningún número establecido de clases etiquetadas que hagan que todos los modelos se ejecuten bien. El rendimiento depende en gran medida del esquema y de la ambigüedad del esquema. Las clases ambiguas necesitan más etiquetas. El rendimiento también depende de la calidad del etiquetado. El número recomendado de instancias etiquetadas por clase es 50. 

## <a name="training-is-taking-a-long-time-is-this-expected"></a>El entrenamiento está tardando mucho tiempo, ¿es esto lo esperado?

Este proceso de entrenamiento puede tardar algún tiempo. Como estimación aproximada, el tiempo de entrenamiento esperado para los archivos con una longitud combinada de 12 800 000 caracteres es de 6 horas.

## <a name="how-do-i-build-my-custom-model-programmatically"></a>¿Cómo puedo crear mi modelo personalizado mediante programación?

Puede usar las [API REST](https://aka.ms/ct-authoring-swagger) para compilar los modelos personalizados. Siga este [inicio rápido](quickstart.md?pivots=rest-api) para empezar a crear un proyecto y crear un modelo a través de las API para obtener ejemplos de cómo llamar a la API de creación. 


## <a name="what-is-the-recommended-cicd-process"></a>¿Cuál es el proceso de CI/CD recomendado?

Puede entrenar varios modelos en el mismo conjunto de datos dentro del mismo proyecto. Después de haber entrenado el modelo correctamente, puede [ver su evaluación](how-to/view-model-evaluation.md). Puede [implementar y probar](quickstart.md#deploy-your-model) el modelo en [Language Studio](https://aka.ms/languageStudio). Puede agregar o quitar etiquetas de los datos, así como entrenar un **nuevo** modelo y probarlo. Vea los [límites del servicio](service-limits.md) para obtener información sobre el número máximo de modelos entrenados con el mismo proyecto. Al entrenar un nuevo modelo, el conjunto de datos se [divide](how-to/train-model.md#data-splits) aleatoriamente en conjuntos de entrenamiento y pruebas. Por ello, no hay garantía de que la evaluación del modelo se realice en el mismo conjunto de pruebas, por lo que los resultados no son comparables. Se recomienda desarrollar su propio conjunto de pruebas y usarlo para evaluar ambos modelos para poder medir la mejora.

## <a name="does-a-low-or-high-model-score-guarantee-bad-or-good-performance-in-production"></a>¿Una puntuación de modelo baja o alta garantiza un rendimiento bajo o bueno en producción?

Es posible que la evaluación del modelo no siempre sea completa. Esto depende de: 
* Si el **conjunto de pruebas** es demasiado pequeño, las puntuaciones buenas o malas no son representativas del rendimiento real del modelo. Además, si falta una clase específica o está infrarrepresentada en el conjunto de pruebas, el rendimiento del modelo resultará afectado.
* **Diversidad de datos**: si los datos solo abarcan algunos escenarios o ejemplos del texto que espera en producción, el modelo no estará expuesto a todos los escenarios posibles y podría tener un rendimiento deficiente en los escenarios en los que no se ha entrenado.
* **Representación de datos**: si el conjunto de datos usado para entrenar el modelo no es representativo de los datos que se introducirían en el modelo en producción, el rendimiento del modelo se verá afectado en gran medida.

Consulte el artículo sobre [selección de datos y diseño de esquemas](how-to/design-schema.md) para obtener más información.

## <a name="how-do-i-improve-model-performance"></a>¿Cómo puedo mejorar el rendimiento del modelo?

* Vea el modelo [matriz de confusión](how-to/view-model-evaluation.md), si observa que una determinada clase se clasifica frecuentemente de forma incorrecta, considere la posibilidad de agregar más instancias etiquetadas para esta clase. Si observa que dos clases se clasifican con frecuencia como la otra, esto significa que el esquema es ambiguo; así pues, considere la posibilidad de fusionarlas en una sola clase para mejorar el rendimiento.

*  [Examinar la distribución de datos](how-to/improve-model.md#examine-data-distribution-from-language-studio): si una de las clases tiene muchas más instancias etiquetadas que las demás, el modelo puede estar sesgado hacia esta clase. Agregue más datos a las otras clases o quite la mayoría de los ejemplos de la clase dominante. 

* Obtenga más información sobre la selección de datos y el diseño de esquemas [aquí](how-to/design-schema.md).

* [Revise el conjunto de pruebas](how-to/improve-model.md) para ver las clases predichas y etiquetadas en paralelo para que pueda obtener una mejor idea del rendimiento del modelo y decidir si es necesario realizar algún cambio en el esquema o en las etiquetas.

## <a name="when-i-retrain-my-model-i-get-different-results-why-is-this"></a>Cuando vuelvo a entrenar mi modelo obtengo resultados diferentes, ¿a qué se debe esto?

* Al entrenar un nuevo modelo, el conjunto de datos [se divide](how-to/train-model.md#data-splits) aleatoriamente en conjuntos de entrenamiento y pruebas, por lo que no hay ninguna garantía de que la evaluación del modelo reflejado sea sobre el mismo conjunto de prueba; así pues, los resultados no son comparables.

* Si vuelve a entrenar el mismo modelo, el conjunto de pruebas será el mismo, pero es posible que observe un pequeño cambio en las predicciones realizadas por el modelo. Esto se debe a que el modelo entrenado no es lo suficientemente sólido, lo cual es un factor de lo representativos y distintos que son los datos y de la calidad de los datos etiquetados. 

## <a name="how-do-i-get-predictions-in-different-languages"></a>¿Cómo obtener predicciones en distintos idiomas?

En primer lugar, debe habilitar la opción multilingüe al [crear el proyecto](how-to/create-project.md), o puede habilitarla más adelante desde la página de configuración del proyecto. Después de entrenar e implementar el modelo, puede empezar a consultarlo en [varios idiomas](language-support.md#multiple-language-support). Puede obtener resultados variados para distintos idiomas. Para mejorar la precisión de cualquier idioma, agregue más instancias etiquetadas al proyecto en ese idioma para introducir el modelo entrenado a más sintaxis de ese idioma.

## <a name="i-trained-my-model-but-i-cant-test-it"></a>He entrenado mi modelo, pero no puedo probarlo

Debe [implementar el modelo](quickstart.md#deploy-your-model) para poder probarlo. 

## <a name="how-do-i-use-my-trained-model-to-make-predictions"></a>¿Cómo puedo usar mi modelo entrenado para realizar predicciones?

Después de implementar el modelo, [llame a la API de predicción](how-to/call-api.md). Para obtener más información, consulte la [referencia de la API de predicción](https://aka.ms/ct-runtime-swagger).

## <a name="data-privacy-and-security"></a>Seguridad y privacidad de datos

La clasificación de texto personalizada es un procesador de datos para los fines del Reglamento general de protección de datos (RGPD). En cumplimiento con las directivas del RGPD, los usuarios de la clasificación personalizada tienen control total para ver, exportar o eliminar cualquier contenido de usuario a través de [Language Studio](https://aka.ms/languageStudio) o mediante programación mediante las [API REST](https://aka.ms/ct-authoring-swagger).

Los datos solo se almacenan en la cuenta de Azure Storage. La clasificación personalizada solo tiene acceso para leer desde ella durante el entrenamiento.

## <a name="how-to-clone-my-project"></a>¿Cómo puedo clonar mi proyecto?

Para clonar el proyecto, debe usar la API de exportación para exportar los recursos del proyecto y, a continuación, importarlos en un nuevo proyecto. Consulte la referencia [API REST](https://aka.ms/ct-authoring-swagger) para ambas operaciones.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la clasificación personalizada de texto](overview.md)
* [Guía de inicio rápido](quickstart.md)
