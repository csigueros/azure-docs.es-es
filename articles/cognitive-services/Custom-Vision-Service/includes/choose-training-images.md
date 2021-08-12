---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 68965017dc3f6d062f5a5c18acb1774d38e385c4
ms.sourcegitcommit: 20abee54e48f9b40b83d39c5b970bd0193812cb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2021
ms.locfileid: "113659521"
---
Como mínimo, se recomienda que use 30 imágenes por etiqueta en el conjunto de entrenamiento inicial. También conviene recopilar algunas imágenes adicionales para probar el modelo una vez que está entrenado.

Para entrenar el modelo de forma eficaz, use imágenes con variedad visual. Seleccione imágenes que varíen en:
* ángulos de cámara
* iluminación
* background
* estilo visual
* sujetos individuales o grupos
* tamaño
* type

Además, asegúrese de que todas las imágenes de entrenamiento cumplen los criterios siguientes:
* formato .jpg, .png, .bmp o .gif
* tienen menos de 6 MB de tamaño (4 MB en el caso de imágenes de predicción)
* tienen más de 256 píxeles en el borde más corto. Custom Vision Service escalará verticalmente y de forma automática todas las imágenes que sean más cortas

> [!NOTE]
> ¿Necesita un conjunto de imágenes más amplio para completar el entrenamiento? Trove, un proyecto de Microsoft Garage, le permite recopilar y comprar conjuntos de imágenes con fines de aprendizaje. Una vez que haya recopilado sus imágenes, puede descargarlas y, a continuación, importarlas en el proyecto de Custom Vision de la manera habitual. Visite la [página de Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) para más información.
