---
title: Uso del punto de conexión de predicción para probar imágenes mediante programación con un clasificador (Custom Vision)
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo usar la API para probar las imágenes mediante programación con el clasificador de Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 56c2e5849381becfcd561828300cf4780a1e168b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708824"
---
# <a name="call-the-prediction-api"></a>Llamada a la API de predicción

Después de entrenar el modelo, puede probar imágenes mediante programación mediante su envío al punto de conexión de la API de predicción. En esta guía, aprenderá a llamar a la API de predicción para puntuar una imagen. Conocerá las distintas maneras en que puede configurar el comportamiento de esta API para satisfacer sus necesidades.


> [!NOTE]
> En este documento, se muestra el uso de la biblioteca cliente de .NET para C# para enviar una imagen a la API de predicción. Para más información y ejemplos, consulte la [referencia de Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="setup"></a>Configurar

### <a name="publish-your-trained-iteration"></a>Publicación de la iteración entrenada

En la [página web de Custom Vision](https://customvision.ai), seleccione el proyecto y luego seleccione la pestaña __Rendimiento__.

Para enviar imágenes a la API de predicción, primero debe publicar la iteración para la predicción; para ello, seleccione __Publish__ (Publicar) y especifique un nombre para la iteración publicada. Esto hará que el modelo sea accesible a la instancia de Prediction API del recurso de Azure de Custom Vision.

![Se muestra la pestaña de rendimiento, con un rectángulo rojo que rodea el botón Publish (Publicar).](./media/use-prediction-api/unpublished-iteration.png)

Una vez que el modelo se ha publicado correctamente, verá que aparece una etiqueta "Published" (Publicado) junto a la iteración en la barra lateral izquierda y que su nombre aparece en la descripción de la iteración.

![Se muestra la pestaña de rendimiento, con un rectángulo rojo que rodea la etiqueta Published (Publicado) y el nombre de la iteración publicada.](./media/use-prediction-api/published-iteration.png)

### <a name="get-the-url-and-prediction-key"></a>Obtención de una clave de predicción y dirección URL

Una vez que se ha publicado el modelo, puede recuperar la información necesaria mediante la selección de __Prediction URL__ (Dirección URL de predicción). Se abre un cuadro de diálogo con información sobre el uso de Prediction API, incluidas las opciones __Prediction URL__ (Dirección URL de predicción) y __Prediction-Key__ (Clave de predicción).

![Se muestra la pestaña de rendimiento con un rectángulo rojo que rodea el botón de dirección URL de predicción.](./media/use-prediction-api/published-iteration-prediction-url.png)

![La pestaña de rendimiento se muestra con un rectángulo rojo que rodea la dirección URL de predicción para el uso de un archivo de imagen y el valor de la clave de predicción.](./media/use-prediction-api/prediction-api-info.png)

## <a name="submit-data-to-the-service"></a>Envío de datos al servicio

En esta guía, se da por supuesto que ya ha construido un objeto **[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)** , llamado `predictionClient`, con la clave de predicción y la dirección URL del punto de conexión de Custom Vision. Para obtener instrucciones sobre cómo configurar esta característica, siga uno de los [inicios rápidos](quickstarts/image-classification.md).

En esta guía, usará una imagen local, por lo que debe descargar una imagen que quiera enviar al modelo entrenado. El código siguiente solicita al usuario que especifique una ruta de acceso local y obtiene la secuencia de bytes del archivo en esa ruta de acceso.

```csharp
Console.Write("Enter image file path: ");
string imageFilePath = Console.ReadLine();
byte[] byteData = GetImageAsByteArray(imageFilePath);
```

Incluya el siguiente método auxiliar:

```csharp
private static byte[] GetImageAsByteArray(string imageFilePath)
{
    FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
    BinaryReader binaryReader = new BinaryReader(fileStream);
    return binaryReader.ReadBytes((int)fileStream.Length);
}
```

El método **[ClassifyImageAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclientextensions.classifyimageasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_CustomVisionPredictionClientExtensions_ClassifyImageAsync_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_ICustomVisionPredictionClient_System_Guid_System_String_System_IO_Stream_System_String_System_Threading_CancellationToken_)** toma el identificador del proyecto y la imagen almacenada localmente, y puntúa la imagen según el modelo especificado.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = predictionApi.ClassifyImageAsync(project.Id, publishedModelName, byteData);
```

## <a name="determine-how-to-process-the-data"></a>Determinación de cómo se procesan los datos

Opcionalmente, puede configurar cómo realiza el servicio la operación de puntuación eligiendo métodos alternativos (consulte los métodos de la clase **[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet)** ). 

Puede usar una versión no asincrónica del método anterior para simplificar, pero esto puede hacer que el programa se bloquee durante un período de tiempo apreciable.

Los métodos **-WithNoStore** requieren que el servicio no conserve la imagen de predicción una vez completada la predicción. Normalmente, el servicio conserva estas imágenes, por lo que tiene la opción de agregarlas como datos de entrenamiento para futuras iteraciones del modelo.

Los métodos **-WithHttpMessages** devuelven la respuesta HTTP sin procesar de la llamada API.

## <a name="get-results-from-the-service"></a>Obtención de resultados a partir del servicio

El servicio devuelve los resultados en forma de un objeto **[ImagePrediction](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-dotnet)** . La propiedad **Predictions** contiene una lista de objetos **[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet)** , cada uno de los cuales representa la predicción de un solo objeto. Incluyen el nombre de la etiqueta y las coordenadas del rectángulo delimitador donde se detectó el objeto en la imagen. A continuación, la aplicación puede analizar estos datos para, por ejemplo, mostrar la imagen con los campos de objeto etiquetados en una pantalla. 

## <a name="next-steps"></a>Pasos siguientes

En esta guía, ha aprendido a enviar imágenes a su clasificador o detector de imágenes personalizado y a recibir una respuesta mediante programación con el SDK de C#. A continuación, aprenda a realizar escenarios de un extremo a otro con C# o comience a usar un SDK de un lenguaje diferente.

* [Inicio rápido: SDK de Custom Vision](quickstarts/image-classification.md)