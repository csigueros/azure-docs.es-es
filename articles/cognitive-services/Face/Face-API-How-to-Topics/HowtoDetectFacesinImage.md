---
title: 'Llamada a la API de detección: face'
titleSuffix: Azure Cognitive Services
description: En esta guía se muestra cómo usar la detección de caras para extraer de una imagen determinada atributos como la edad, la emoción o la posición de la cabeza.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe1cddcbd194ec4b8ca25edfc4907631cb18df1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744825"
---
# <a name="call-the-detect-api"></a>Llamada a la API de detección

En esta guía se muestra cómo usar la API de detección de caras para extraer de una imagen determinada atributos como la edad, la emoción o la posición de la cabeza. Conocerá las distintas maneras de configurar el comportamiento de esta API para satisfacer sus necesidades.

Los fragmentos de código de esta guía están escritos en C# con la biblioteca cliente Face de Azure Cognitive Services. La misma funcionalidad está disponible mediante la [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).


## <a name="setup"></a>Configurar

En esta guía se da por supuesto que ya ha construido un objeto [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) llamado `faceClient`, con una clave de suscripción y una dirección URL de punto de conexión de Face. Para obtener instrucciones sobre cómo configurar esta característica, siga uno de los inicios rápidos.

## <a name="submit-data-to-the-service"></a>Envío de datos al servicio

Para encontrar caras y obtener su ubicación en una imagen, llame a los métodos [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) o [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync). **DetectWithUrlAsync** toma una cadena de dirección URL como entrada, mientras que **DetectWithStreamAsync** toma la secuencia de bytes sin formato de una imagen.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Puede consultar los objetos [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) devueltos para obtener sus identificadores únicos y un rectángulo que proporciona las coordenadas en píxeles de la cara. De este modo, puede saber qué Id. se asigna a cada cara de la imagen original.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Para obtener información sobre cómo analizar la ubicación y las dimensiones de la cara, consulte [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Normalmente, este rectángulo contiene los ojos, las cejas, la nariz y la boca. La parte superior de la cabeza, las orejas y la barbilla no están incluidas necesariamente. Para usar el rectángulo facial con el fin de recortar una cabeza completa u obtener un retrato intermedio, expanda el rectángulo en cada dirección.

## <a name="determine-how-to-process-the-data"></a>Determinación de cómo procesar los datos

Esta guía se centra en los detalles de la llamada de detección, como los argumentos que se pueden pasar y lo que puede hacer con los datos devueltos. Se recomienda consultar solo las características que necesita. Cada operación tarda más tiempo en completarse.

### <a name="get-face-landmarks"></a>Obtención de los puntos de referencia faciales

Los [puntos de referencia faciales](../concepts/face-detection.md#face-landmarks) son un conjunto de puntos fáciles de encontrar en una cara como, por ejemplo, las pupilas o la punta de la nariz. Para obtener datos de los puntos de referencia faciales, establezca el parámetro _detectionModel_ en `DetectionModel.Detection01` y el parámetro _returnFaceLandmarks_ en `true`.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

### <a name="get-face-attributes"></a>Obtención de los atributos de la cara

Además de los rectángulos y los puntos de referencia faciales, la API de detección de caras puede analizar varios atributos conceptuales de una cara. Para obtener una lista completa, consulte la sección conceptual [Atributos de una cara](../concepts/face-detection.md#attributes).

Para analizar los atributos de una cara, establezca el parámetro _detectionModel_ en `DetectionModel.Detection01` y el parámetro _returnFaceAttributes_ en una lista de valores [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype).

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::


## <a name="get-results-from-the-service"></a>Obtención de resultados a partir del servicio

### <a name="face-landmark-results"></a>Resultados de los puntos de referencia faciales

El código siguiente muestra cómo podría recuperar las ubicaciones de la nariz y las pupilas:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

También puede usar los datos de los puntos de referencia faciales para calcular con precisión la dirección de la cara. Por ejemplo, se puede definir la rotación de la cara como un vector desde el centro de la boca hasta el centro de los ojos. El código siguiente calcula este vector:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Cuando conoce la dirección de la cara, puede girar el marco rectangular de la cara para alinearlo más correctamente. Para recortar caras de una imagen, puede girar mediante programación la imagen para que siempre aparezcan en vertical.


### <a name="face-attribute-results"></a>Resultados del atributo face

En el siguiente código se muestra cómo recuperar los datos del atributo face que solicitó en la llamada original.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Para más información acerca de cada uno de los atributos, consulte la guía conceptual [Detección de caras y atributos](../concepts/face-detection.md).

## <a name="next-steps"></a>Pasos siguientes

En esta guía ha obtenido información sobre cómo utilizar las diversas funcionalidades de la detección y el análisis de caras. A continuación, integre estas características en una aplicación para agregar datos faciales de los usuarios.

- [Tutorial: Incorporación de usuarios a un servicio de Face](../enrollment-overview.md)

## <a name="related-articles"></a>Artículos relacionados

- [Documentación de referencia (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentación de referencia (SDK para .NET)](/dotnet/api/overview/azure/cognitiveservices/face-readme)
