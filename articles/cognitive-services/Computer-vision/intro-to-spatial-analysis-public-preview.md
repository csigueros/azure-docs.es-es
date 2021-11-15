---
title: ¿Qué es Spatial Analysis?
titleSuffix: Azure Cognitive Services
description: En este documento se explican los conceptos básicos y las características de un contenedor de Azure Spatial Analysis.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/06/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: fd160aa2ba4a626e12db638694cb6d971b19fbd2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469946"
---
# <a name="what-is-spatial-analysis"></a>¿Qué es Spatial Analysis?

Puede usar el análisis espacial de Computer Vision para ingerir vídeo de streaming de cámaras, extraer información y generar eventos que puedan usar otros sistemas. El servicio detecta la presencia y los movimientos de las personas del vídeo. Puede hacer cosas como contar el número de personas que entran en un espacio o medir el cumplimiento de las directrices de mascarilla y distancia social. Al procesar secuencias de vídeo de espacios físicos, puede aprender cómo los usan los usuarios y maximizar el valor del espacio para su organización. 

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Qué hace
El análisis espacial ingiere vídeo y, luego, detecta personas en él. Cuando esto sucede, el sistema rastrea a la gente a medida que se mueve en el tiempo y, luego, genera eventos cuando interactúa con las regiones de interés. Todas las operaciones proporcionan información detallada desde el campo de visión de una sola cámara. 

### <a name="people-counting"></a>Recuento de personas
Esta operación cuenta el número de personas de una zona específica a lo largo del tiempo mediante la operación PersonCount. Se genera un recuento independiente para cada fotograma procesado sin intentar realizar un seguimiento de las personas entre fotogramas. Esta operación se puede usar para calcular el número de personas en un espacio o generar una alerta cuando aparezca una persona.

![El análisis espacial cuenta el número de personas en el campo de visión de las cámaras](https://user-images.githubusercontent.com/11428131/139924111-58637f2e-f2f6-42d8-8812-ab42fece92b4.gif)

### <a name="entrance-counting"></a>Recuento de entradas
Esta característica supervisa cuánto tiempo permanecen las personas en un área o cuándo entran por una puerta. Esta supervisión se puede realizar mediante las operaciones PersonCrossingPolygon o PersonCrossingLine. En escenarios comerciales, estas operaciones se pueden usar para medir los tiempos de espera en una línea de caja o la interacción en una pantalla. Además, estas operaciones podrían medir el tráfico peatonal en un vestíbulo o en una planta determinada en otros escenarios de edificio comercial.

![El análisis espacial mide el tiempo de permanencia en la línea de caja](https://user-images.githubusercontent.com/11428131/137016574-0d180d9b-fb9a-42a9-94b7-fbc0dbc18560.gif)

### <a name="social-distancing-and-facemask-detection"></a>Detección de mascarilla y distancia social 
Esta característica analiza la manera en que las personas siguen las directrices de distancia social en un espacio. Con la operación PersonDistance, el sistema se calibra automáticamente a medida que las personas se mueven en el espacio. A continuación, identifica cuándo las personas infringen un umbral de distancia determinado (2 o 3 metros).

![El análisis espacial visualiza los eventos de infracción de la distancia social y muestra líneas entre las personas para indicar la distancia](https://user-images.githubusercontent.com/11428131/139924062-b5e10c0f-3cf8-4ff1-bb58-478571c022d7.gif)

El análisis espacial también se puede configurar para detectar si una persona lleva alguna protección para la cara, como una mascarilla. Existe la posibilidad de habilitar un clasificador de mascarillas para las operaciones PersonCount, PersonCrossingLine y PersonCrossingPolygon mediante la configuración del parámetro `ENABLE_FACE_MASK_CLASSIFIER`.

![El análisis espacial clasifica si las personas llevan mascarilla en un ascensor](https://user-images.githubusercontent.com/11428131/137015842-ce524f52-3ac4-4e42-9067-25d19b395803.png)

## <a name="get-started"></a>Introducción

Siga el [inicio rápido](spatial-analysis-container.md) para configurar el contenedor de Spatial Analysis y empezar a analizar vídeo.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Uso responsable de la tecnología de análisis espacial

Para aprender a usar la tecnología de análisis espacial de forma responsable, consulte la [nota de transparencia](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Las notas de transparencia de Microsoft le ayudan a comprender cómo funciona nuestra tecnología de inteligencia artificial y las elecciones que los propietarios del sistema pueden hacer para influir en el rendimiento y el comportamiento del sistema. Se centran en la importancia de pensar en el sistema en su conjunto, incluida la tecnología, las personas y el entorno.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Inicio rápido: Contenedor de análisis espacial](spatial-analysis-container.md)
