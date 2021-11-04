---
title: Preguntas más frecuentes sobre Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre las preguntas más frecuentes al usar Reconocimiento de entidades con nombre personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c413e03e9106a3063e9cfdf26a1ab4b7fd7d5f25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093518"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>Preguntas más frecuentes sobre Reconocimiento de entidades con nombre personalizado

Encuentre respuestas a preguntas más frecuentes sobre conceptos y escenarios relacionados con NER personalizado en Azure Cognitive Service for Language.

## <a name="how-many-tagged-files-are-needed"></a>¿Cuántos archivos etiquetados se necesitan?

Por lo general, los [datos etiquetados](how-to/tag-data.md) diversos y representativos generan mejores resultados, dado que el etiquetado se realiza de forma precisa, coherente y completa. No hay ningún número establecido de entidades etiquetadas que hagan que todos los modelos se ejecuten bien. El rendimiento depende en gran medida del esquema y de la ambigüedad de las entidades. Los tipos de entidad ambiguos necesitan más etiquetas. El rendimiento también depende de la calidad del etiquetado. El número recomendado de instancias etiquetadas por entidad es 200. 

## <a name="what-are-the-service-limits"></a>¿Cuáles son los límites de servicio?

Para obtener más información, consulte el [artículo sobre límites del servicio](service-limits.md).

## <a name="what-to-do-if-my-model-scores-poorly"></a>¿Qué puedo hacer si mi modelo tiene una puntuación deficiente?

Es posible que la evaluación del modelo no siempre sea completa, especialmente si falta una clase específica o no está representada en el conjunto de pruebas. Plantéese agregar más datos etiquetados al modelo para mejorar el rendimiento y tener un conjunto de pruebas más representativo.

## <a name="how-do-i-improve-model-performance"></a>¿Cómo puedo mejorar el rendimiento del modelo?

Vea la [matriz de confusión](how-to/view-model-evaluation.md) para identificar la ambigüedad del esquema. A continuación, [revise el conjunto de pruebas](how-to/improve-model.md) para ver las entidades predichas y etiquetadas en paralelo para que pueda obtener una mejor idea del rendimiento del modelo y decidir si es necesario realizar algún cambio en el esquema o en las etiquetas.  

## <a name="i-trained-my-model-but-i-cant-test-it"></a>He entrenado mi modelo, pero no puedo probarlo

Debe [implementar el modelo](quickstart.md#deploy-your-model) para poder probarlo. 

## <a name="how-do-i-use-the-analyze-api"></a>¿Cómo uso la API de análisis?

Después de implementar el modelo, [llame a la API en tiempo de ejecución](how-to/call-api.md). Para obtener más información, consulte la [referencia de la API de análisis](https://aka.ms/ct-runtime-swagger).

## <a name="data-privacy-and-security"></a>Seguridad y privacidad de datos

Los datos solo se almacenan en la cuenta de almacenamiento de Azure. NER personalizado solo tiene acceso para leer en ella durante el entrenamiento y la evaluación. 

<!-- ## How to clone my project?

To clone your project you need to [export]() project assests and then [import]() them into a new project. -->

## <a name="next-steps"></a>Pasos siguientes

[Introducción a NER personalizado](overview.md)
