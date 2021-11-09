---
title: 'Selección de un dominio para un proyecto de Custom Vision: Computer Vision'
titleSuffix: Azure Cognitive Services
description: En este artículo se mostrará cómo selecciona un dominio para un proyecto en el servicio Custom Vision.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 5838185328920587b11bb66d21c9402ce93e9b6b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458254"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selección de un dominio para un proyecto de Custom Vision

Desde la pestaña de configuración del proyecto de Custom Vision, puede seleccionar un dominio para el proyecto. Elija el dominio más cercano a su escenario. Si accede a Custom Vision mediante una biblioteca de cliente o API REST, tendrá que especificar un identificador de dominio al crear el proyecto. Puede obtener una lista de identificadores de dominio con [Obtener dominios](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab), o bien usar la tabla siguiente.

## <a name="image-classification"></a>Clasificación de imágenes

|Domain|Propósito|
|---|---|
|__General__| Optimizado para una amplia gama de tareas de clasificación de imágenes. Si ninguno de los otros dominios específicos es adecuado o si no está seguro de cuál elegir, seleccione uno de los dominios Generales. Identificador: `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__General [A1]__| Optimizado para una mejor precisión con un tiempo de inferencia comparable al del dominio General. Se recomienda para conjuntos de datos más grandes o escenarios de usuario más difíciles. Este dominio requiere más tiempo de entrenamiento. Identificador: `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__General [A2]__| Optimizado para mejorar la precisión con un tiempo de inferencia más rápido que los dominios generales [A1] y general. Recomendado para la mayoría de los conjuntos de datos. Este dominio requiere menos tiempo de entrenamiento que los dominios generales y generales [A1]. Identificador: `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__Alimentos__|Optimizado para fotos de platos tal y como los vería en el menú de un restaurante. Si quiere clasificar fotos de frutas o verduras individuales, use el dominio de alimentos. Identificador: `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Puntos de referencia__|Optimizado para lugares de interés reconocibles, tanto naturales como artificiales. Este dominio funciona mejor cuando el punto de referencia es claramente visible en la foto. Este dominio funciona incluso si hay gente delante del punto de referencia que obstruye parcialmente su visión. Identificador: `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Minoristas__|Optimizado para imágenes que se encuentran en un catálogo de compra o un sitio web de compras. Si quiere una clasificación de alta precisión entre vestidos, pantalones y camisas, use este dominio. Identificador: `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Dominios compactos__| Optimizado para las restricciones de clasificación en tiempo real en dispositivos perimetrales.|


> [!NOTE]
> Los dominios General[A1] y General[A2] se pueden usar para un amplio conjunto de escenarios y se optimizan para que sean más precisos. Use el modelo General[A2] para mejorar la velocidad de inferencia y el tiempo de entrenamiento más corto. En el caso de conjuntos de valores de mayor tamaño, es posible que desee usar General[A1] para representar una mejor precisión que el General[A2], aunque requiere más aprendizaje y tiempo de inferencia. El modelo general requiere más tiempo de inferencia que General [A1] y General[A2].

## <a name="object-detection"></a>Detección de objetos

|Domain|Propósito|
|---|---|
|__General__| Optimizado para una amplia variedad de tareas de detección de objetos. Si ninguno de los otros dominios es adecuado, o no está seguro de cuál elegir, seleccione el dominio General. Identificador: `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__General [A1]__| Optimizado para una mejor precisión con un tiempo de inferencia comparable al del dominio General. Se recomienda para necesidades de ubicación de región más precisas, conjuntos de datos más grandes o escenarios de usuario más difíciles. Este dominio requiere más tiempo de entrenamiento, y los resultados no son deterministas: espere una diferencia en el valor promedio de la precisión media del +-1 % con los mismos datos de entrenamiento proporcionados. Identificador: `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logotipo__|Optimizado para buscar logotipos de marca en imágenes. Identificador: `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Productos en las estanterías__|Optimizado para detectar y clasificar los productos que están en las estanterías. Identificador: `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Dominios compactos__| Optimizados para las restricciones de detección de objetos en tiempo real en dispositivos perimetrales.|

## <a name="compact-domains"></a>Dominios compactos

Los modelos generados por los dominios compactos se pueden exportar para ejecutarse localmente. En la API en versión preliminar pública de Custom Vision 3.4, puede obtener una lista de las plataformas exportables para dominios compactos mediante una llamada a la API GetDomains.

Todos los dominios siguientes admiten la exportación en los formatos ONNX, TensorFlow, TensorFlowLite, TensorFlow.js, CoreML y VAIDK, con la excepción de que el dominio **General (compacto) de detección de objetos** no admite VAIDK.

El rendimiento varía en función del dominio seleccionado. En la tabla siguiente, se indican el tamaño del modelo y el tiempo de inferencia en una CPU para equipos de sobremesa de Intel y en una GPU de NVidia \[1\]. Estas cifras no incluyen el tiempo de preprocesamiento y el de posprocesamiento.

|Tarea|Domain|ID|Tamaño del modelo|Tiempo de inferencia de CPU|Tiempo de inferencia de GPU|
|---|---|---|---|---|---|
|clasificación|General (compact) (General [compacto])|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 ms|5 ms|
|clasificación|General (compacto) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 ms|5 ms|
|Detección de objetos|General (compact) (General [compacto])|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 ms|5 ms|
|Detección de objetos|General (compacto) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 ms|7 ms|

>[!NOTE]
>El dominio __General (compact)__ de Object Detection (Detección de objetos) requiere una lógica de posprocesamiento especial. Para más información, vea cualquier script de ejemplo del paquete comprimido exortado. Si necesita un modelo sin lógica de posprocesamiento, use __General (compacto) [S1]__ .

>[!IMPORTANT]
>No se garantiza que los modelos exportados proporcionen exactamente el mismo resultado que la API de predicción en la nube. Una leve diferencia en la plataforma de ejecución o en la implementación del preprocesamiento puede provocar una diferencia mayor en las salidas de los modelos. Para más información sobre la lógica de preprocesamiento, consulte [este documento](quickstarts/image-classification.md).

\[1\] CPU Intel Xeon E5-2690 CPU y NVIDIA Tesla M60
