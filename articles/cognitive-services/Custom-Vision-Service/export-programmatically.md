---
title: Exportación de un modelo mediante programación
titleSuffix: Azure Cognitive Services
description: Use la biblioteca cliente de Custom Vision para exportar un modelo entrenado.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: pafarley
ms.openlocfilehash: b67c4e6165b90a580a3a29583bdfe93a98d327c8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361394"
---
# <a name="export-a-model-programmatically"></a>Exportación de un modelo mediante programación

Todas las opciones de exportación disponibles en el [sitio web de Custom Vision](https://www.customvision.ai/) también se pueden realizar mediante programación a través de las bibliotecas cliente. Es posible que quiera hacer esto para poder automatizar completamente el proceso de volver a entrenar y actualizar la iteración del modelo que se usa en un dispositivo local.

En esta guía se muestra cómo exportar el modelo a un archivo ONNX con el SDK de Python.

## <a name="create-a-training-client"></a>Creación de un cliente de entrenamiento

Si desea exportar una iteración del modelo, debe tener un objeto [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient). Cree variables para las claves de suscripción y punto de conexión de Azure de los recursos de entrenamiento de Custom Vision y úselas para crear el objeto de cliente.

```python
ENDPOINT = "PASTE_YOUR_CUSTOM_VISION_TRAINING_ENDPOINT_HERE"
training_key = "PASTE_YOUR_CUSTOM_VISION_TRAINING_SUBSCRIPTION_KEY_HERE"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)
```

> [!IMPORTANT]
> Cuando termine, recuerde quitar las claves del código y nunca hacerlas públicas. En el caso de producción, considere la posibilidad de usar alguna forma segura de almacenar las credenciales, y acceder a ellas. Para más información, consulte el artículo sobre la [seguridad](../cognitive-services-security.md) de Cognitive Services.

## <a name="call-the-export-method"></a>Llamada al método de exportación

Llame al método **export_iteration**.
* Proporcione el identificador del proyecto y el identificador de iteración del modelo que desea exportar. 
* El parámetro *platform* especifica la plataforma a la que se realizará la exportación. Los valores permitidos son `CoreML`, `TensorFlow`, `DockerFile`, `ONNX`, `VAIDK` y `OpenVino`. 
* El parámetro *flavor* especifica el formato del modelo exportado. Los valores permitidos son: `Linux`, `Windows`, `ONNX10`, `ONNX12`, `ARM`, `TensorFlowNormal` y `TensorFlowLite`.
* El parámetro *raw* le ofrece la opción de recuperar la respuesta JSON sin formato junto con la respuesta del modelo de objetos.

```python
project_id = "PASTE_YOUR_PROJECT_ID"
iteration_id = "PASTE_YOUR_ITERATION_ID"
platform = "ONNX"
flavor = "ONNX10"
export = trainer.export_iteration(project_id, iteration_id, platform, flavor, raw=False)
```

Para más información, consulte el método **[export_iteration](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#export-iteration-project-id--iteration-id--platform--flavor-none--custom-headers-none--raw-false----operation-config-)** .

## <a name="download-the-exported-model"></a>Descarga del modelo exportado

Luego, llamará al método **get_exports** para comprobar el estado de la operación de exportación. La operación se ejecuta de manera asincrónica, por lo que debe sondear este método hasta que se complete la operación. Cuando se complete, puede recuperar el URI en el puede descargar la iteración del modelo en el dispositivo.

```python
while (export.status == "Exporting"):
    print ("Waiting 10 seconds...")
    time.sleep(10)
    exports = trainer.get_exports(project_id, iteration_id)
    # Locate the export for this iteration and check its status  
    for e in exports:
        if e.platform == export.platform and e.flavor == export.flavor:
            export = e
            break
    print("Export status is: ", export.status)
```

Para más información, consulte el método **[get_exports](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#get-exports-project-id--iteration-id--custom-headers-none--raw-false----operation-config-)** .

A continuación, puede descargar mediante programación el modelo exportado a una ubicación del dispositivo.

```python
if export.status == "Done":
    # Success, now we can download it
    export_file = requests.get(export.download_uri)
    with open("export.zip", "wb") as file:
        file.write(export_file.content)
```

## <a name="next-steps"></a>Pasos siguientes

Explore uno de los siguientes artículos o ejemplos para integrar el modelo exportado en una aplicación:

* [Uso del modelo Tensorflow con Python](export-model-python.md)
* [Uso del modelo ONNX con Machine Learning de Windows](custom-vision-onnx-windows-ml.md)
* Consulte el ejemplo del [modelo CoreML en una aplicación de iOS](https://go.microsoft.com/fwlink/?linkid=857726) para la clasificación de imágenes en tiempo real con Swift
* Consulte el ejemplo del [modelo Tensorflow en una aplicación de Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) para la clasificación de imágenes en tiempo real en Android.
* Consulte el ejemplo del [modelo CoreML con Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) para la clasificación de imágenes en tiempo real en una aplicación Xamarin iOS.