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
ms.openlocfilehash: a6a4473e6d7dfa55fc75934f3fbdfd44c89ab1c4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091505"
---
# <a name="frequently-asked-questions"></a>Preguntas más frecuentes

Encuentre respuestas a preguntas más frecuentes sobre conceptos y escenarios relacionados con la clasificación de texto personalizada en Azure Cognitive Service for Language.

## <a name="how-many-tagged-files-are-needed"></a>¿Cuántos archivos etiquetados se necesitan?

Por lo general, los [datos etiquetados](how-to/tag-data.md) diversos y representativos generan mejores resultados, dado que el etiquetado se realiza de forma precisa, coherente y completa. No hay ningún número establecido de clases etiquetadas que hagan que todos los modelos se ejecuten bien. El rendimiento depende en gran medida del esquema y de la ambigüedad del esquema. Las clases ambiguas necesitan más etiquetas. El rendimiento también depende de la calidad del etiquetado. El número recomendado de instancias etiquetadas por entidad es 50. 

## <a name="what-are-the-service-limits"></a>¿Cuáles son los límites de servicio?

Para obtener más información, consulte el [artículo sobre límites del servicio](service-limits.md).

## <a name="what-to-do-if-my-model-scores-poorly"></a>¿Qué puedo hacer si mi modelo tiene una puntuación deficiente?

Es posible que la evaluación del modelo no siempre sea completa, especialmente si falta una clase específica o no está representada en el conjunto de pruebas. Plantéese agregar más datos etiquetados al modelo para mejorar el rendimiento y tener un conjunto de pruebas más representativo.

## <a name="how-do-i-improve-model-performance"></a>¿Cómo puedo mejorar el rendimiento del modelo?

Vea la [matriz de confusión](how-to/view-model-evaluation.md) para identificar la ambigüedad del esquema. A continuación, [revise el conjunto de pruebas](how-to/improve-model.md) para ver las clases predichas y etiquetadas en paralelo para que pueda obtener una mejor idea del rendimiento del modelo y decidir si es necesario realizar algún cambio en el esquema o en las etiquetas.  

## <a name="i-trained-my-model-but-i-cant-test-it"></a>He entrenado mi modelo, pero no puedo probarlo

Debe [implementar el modelo](quickstart.md#deploy-your-model) para poder probarlo. 

## <a name="how-do-i-use-the-analyze-api"></a>¿Cómo uso la API de análisis?

Después de implementar el modelo, [llame a la API en tiempo de ejecución](how-to/call-api.md). Para obtener más información, consulte la [referencia de la API de análisis](https://aka.ms/ct-runtime-swagger).

## <a name="data-privacy-and-security"></a>Seguridad y privacidad de datos

Los datos solo se almacenan en la cuenta de almacenamiento de Azure. La clasificación personalizada solo tiene acceso para leer en ella durante el entrenamiento y la evaluación. 

<!-- ## How to clone my project?

To clone your project you need to [export]() project assests and then [import]() them into a new project. -->

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la clasificación personalizada de texto](overview.md)
* [quickstart](quickstart.md)
