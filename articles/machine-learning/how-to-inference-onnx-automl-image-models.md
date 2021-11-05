---
title: Inferencia local mediante ONNX para la imagen de AutoML
titleSuffix: Azure Machine Learning
description: Use ONNX con ML automatizado de Azure Machine Learning para hacer predicciones en modelos de Computer Vision para la clasificación, la detección de objetos y la segmentación.
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: 01839f2a6f16584148d4cab86e07f3da0eefee43
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076554"
---
# <a name="make-predictions-with-onnx-on-computer-vision-models-from-automl"></a>Obtención de predicciones con ONNX en modelos de Computer Vision desde AutoML 

En este artículo, aprenderá a usar Open Neural Network Exchange (ONNX) para hacer predicciones en modelos de Computer Vision generados con el aprendizaje automático automatizado (AutoML) en Azure Machine Learning. 

Para usar ONNX para las predicciones, debe hacer lo siguiente: 
 
1. Descargue los archivos del modelo de ONNX de una ejecución de entrenamiento de AutoML.
1. Comprenda las entradas y las salidas de un modelo de ONNX.
1. Preprocese los datos para que estén en el formato necesario para las imágenes de entrada.
1. Haga la inferencia con ONNX Runtime for Python.
1. Visualice las predicciones para las tareas de detección y segmentación de objetos.

[ONNX](https://onnx.ai/about.html) es un estándar abierto para los modelos de aprendizaje automático y aprendizaje profundo. Permite la importación y exportación los de modelos (interoperabilidad) en los marcos de IA populares. Para obtener más información, explore el [proyecto GitHub ONNX](https://github.com/onnx/onnx).

[ONNX Runtime](https://onnxruntime.ai/index.html) es un proyecto de código abierto que admite la inferencia multiplataforma. ONNX Runtime proporciona las API en lenguajes de programación (incluidos Python, C++, C#, C, Java y JavaScript). Puede usar estas API para hacer inferencias en imágenes de entrada. Una vez que tenga el modelo que se exportó al formato ONNX, puede usar estas API en cualquier lenguaje de programación que necesite el proyecto. 

En esta guía, aprenderá a usar las [API de Python para ONNX Runtime](https://onnxruntime.ai/docs/get-started/with-python.html) para hacer las predicciones en las imágenes para las tareas de visión populares. Puede usar estos modelos exportados de ONNX entre lenguajes.

## <a name="prerequisites"></a>Prerrequisitos

* Obtenga un modelo de Computer Vision entrenado por AutoML para cualquiera de las tareas de imagen admitidas: clasificación, detección de objetos o segmentación. [Obtenga más información sobre la compatibilidad de AutoML con tareas de Computer Vision](how-to-auto-train-image-models.md).

* Instale el paquete [onnxruntime](https://onnxruntime.ai/docs/get-started/with-python.html). Los métodos de este artículo se han probado con las versiones 1.3.0 a 1.8.0.

## <a name="download-onnx-model-files"></a>Descarga de archivos del modelo de ONNX

Puede descargar archivos del modelo de ONNX de las ejecuciones de AutoML mediante la UI de Estudio de Azure Machine Learning o el SDK de Python de Azure Machine Learning. Se recomienda descargar mediante el SDK con el nombre del experimento y el identificador de ejecución primario. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

En Estudio de Azure Machine Learning, vaya al experimento mediante el hipervínculo al experimento generado en el cuaderno de entrenamiento o mediante la selección del nombre del experimento en la pestaña **Experiments** (Experimentos) en **Assets** (Recursos). A continuación, seleccione la mejor ejecución secundaria. 

En la mejor ejecución secundaria, vaya a **Outputs+logs** > **train_artifacts**. Use el botón **Download** para descargar manualmente los siguientes archivos:

- *labels.json*: archivo que contiene todas las clases o etiquetas del conjunto de datos de entrenamiento.
- *model.onnx*: modelo en formato ONNX. 

![Captura de pantalla que muestra las selecciones para descargar archivos del modelo de ONNX.](./media/how-to-inference-onnx-automl-image-models/onnx-files-manual-download.png)

Guarde los archivos descargados del modelo en un directorio. En el ejemplo de este artículo se usa el directorio *./automl_models*. 

### <a name="azure-machine-learning-python-sdk"></a>SDK de Python de Azure Machine Learning

Con el SDK, puede seleccionar la mejor ejecución secundaria (por métrica principal) con el nombre del experimento y el id. de ejecución primario. A continuación, puede descargar los archivos *labels.json* y *model.onnx.*

El código siguiente devuelve la mejor ejecución secundaria en función de la métrica principal pertinente.

```python
# Select the best child run
from azureml.train.automl.run import AutoMLRun

run_id = "" # Specify the run ID
automl_image_run = AutoMLRun(experiment=experiment, run_id=run_id)
best_child_run = automl_image_run.get_best_child()
```

Descargue el archivo *labels.json* que contiene todas las clases y etiquetas del conjunto de datos de entrenamiento.

```python
import json

labels_file = "automl_models/labels.json"
best_child_run.download_file(name="train_artifacts/labels.json", output_file_path=labels_file)

```

Descargue el archivo *model.onnx*.

```python
onnx_model_path = "automl_models/model.onnx"
best_child_run.download_file(name="train_artifacts/model.onnx", output_file_path=onnx_model_path)
```

Después del paso de descarga del modelo, use el paquete de Python de ONNX Runtime para hacer la inferencia mediante el archivo *model.onnx*. Con fines de demostración, en este artículo se usan los conjuntos de datos de [Preparación de conjuntos de datos de imagen](how-to-prepare-datasets-for-automl-images.md) para cada tarea de visión. 

Hemos entrenado los modelos para todas las tareas de visión con sus respectivos conjuntos de datos para demostrar la inferencia de los modelos de ONNX.
 
## <a name="load-the-labels-and-onnx-model-files"></a>Carga de las etiquetas y los archivos del modelo de ONNX

El siguiente fragmento de código carga *labels.json*, en el que se ordenan los nombres de clase. Es decir, si el modelo de ONNX predice un id. de etiqueta como 2, corresponde al nombre de etiqueta especificado en el tercer índice del archivo *labels.json*.

```python
import onnxruntime

labels_file = "automl_models/labels.json"
with open(labels_file) as f:
    classes = json.load(f)
print(classes)
try:
    session = onnxruntime.InferenceSession(onnx_model_path)
    print("ONNX model loaded...")
except Exception as e: 
    print("Error loading ONNX file: ",str(e))
```

## <a name="get-expected-input-and-output-details-for-an-onnx-model"></a>Obtención de los detalles de entrada y salida esperados para un modelo de ONNX

Cuando tenga el modelo, es importante conocer algunos detalles específicos del modelo y específicos de la tarea. Estos detalles incluyen el número de entradas y el número de salidas, la forma o el formato de entrada esperados para preprocesar la imagen y la forma de salida para que conozca las salidas específicas del modelo o específicas de la tarea.

```python
sess_input = session.get_inputs()
sess_output = session.get_outputs()
print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")

for idx, input_ in enumerate(range(len(sess_input))):
    input_name = sess_input[input_].name
    input_shape = sess_input[input_].shape
    input_type = sess_input[input_].type
    print(f"{idx} Input name : { input_name }, Input shape : {input_shape}, \
    Input type  : {input_type}")  

for idx, output in enumerate(range(len(sess_output))):
    output_name = sess_output[output].name
    output_shape = sess_output[output].shape
    output_type = sess_output[output].type
    print(f" {idx} Output name : {output_name}, Output shape : {output_shape}, \
    Output type  : {output_type}") 
``` 

### <a name="expected-input-and-output-formats-for-the-onnx-model"></a>Formatos de entrada y salida esperados para el modelo de ONNX

Cada modelo de ONNX tiene un conjunto predefinido de formatos de entrada y salida.

# <a name="multi-class-image-classification"></a>[Clasificación de imágenes de varias clases](#tab/multi-class)

En este ejemplo se aplica el modelo entrenado en el conjunto de datos [fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/fridgeObjects.zip) con 134 imágenes y 4 clases o etiquetas para explicar la inferencia del modelo de ONNX. Para obtener más información sobre cómo entrenar una tarea de clasificación de imágenes, consulte el [cuaderno de clasificación de imágenes de varias clases](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multiclass).

### <a name="input-format"></a>Formato de entrada
    
La entrada es una imagen preprocesada.

| Nombre de entrada  | Forma de entrada  | Tipo de entrada | Descripción |
| -------- |----------:|-----|--------|
| input1 | `(batch_size, num_channels, height, width)` | ndarray(float) | La entrada es una imagen preprocesada, con la forma `(1, 3, 224, 224)` para un tamaño de lote de 1 y un alto y ancho de 224. Estos números corresponden a los valores usados para `crop_size` del ejemplo de entrenamiento. |
    

### <a name="output-format"></a>Formato de salida

La salida es una matriz de logits para todas las clases o etiquetas.
         
| Nombre de salida   | Forma de salida  | Tipo de salida | Descripción |
| -------- |----------|-----|------|
| output1 | `(batch_size, num_classes)` | ndarray(float) | El modelo devuelve logits (sin `softmax`). Por ejemplo, para las clases de tamaño de lote 1 y 4, devuelve `(1, 4)`. |

# <a name="multi-label-image-classification"></a>[Clasificación de imágenes con varias etiquetas](#tab/multi-label)

En este ejemplo se aplica el modelo entrenado en el [conjunto de datos fridgeObjects de varias etiquetas](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/multilabelFridgeObjects.zip) con 128 imágenes y 4 clases o etiquetas para explicar la inferencia del modelo de ONNX. Para obtener más información sobre el entrenamiento de modelos para la clasificación de imágenes de varias etiquetas, consulte el [cuaderno de clasificación de imágenes de varias etiquetas](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multilabel).

### <a name="input-format"></a>Formato de entrada

La entrada es una imagen preprocesada.

| Nombre de entrada       | Forma de entrada  | Tipo de entrada | Descripción |
| -------- |----------|-----|--------|
| input1 | `(batch_size, num_channels, height, width)` | ndarray(float) | La entrada es una imagen preprocesada, con la forma `(1, 3, 224, 224)` para un tamaño de lote de 1 y un alto y ancho de 224. Estos números corresponden a los valores usados para `crop_size` del ejemplo de entrenamiento. |
        
### <a name="output-format"></a>Formato de salida

La salida es una matriz de logits para todas las clases o etiquetas.
    
      
| Nombre de salida       | Forma de salida  | Tipo de salida | Descripción |
| -------- |----------|-----|------
| output1 | `(batch_size, num_classes)` | ndarray(float) | El modelo devuelve logits (sin `sigmoid`). Por ejemplo, para las clases de tamaño de lote 1 y 4, devuelve `(1, 4)`. |


# <a name="object-detection-with-faster-r-cnn"></a>[Detección de objetos con Faster R-CNN](#tab/object-detect-cnn)

En este ejemplo de detección de objetos se aplica el modelo entrenado en el [conjunto de datos de detección fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) de 128 imágenes y 4 clases o etiquetas para explicar la inferencia del modelo de ONNX. En este ejemplo se entrenan modelos de Faster R-CNN para mostrar los pasos de la inferencia. Para obtener más información sobre los modelos de detección de objetos de entrenamiento, vea el [cuaderno de detección de objetos](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection).

### <a name="input-format"></a>Formato de entrada

La entrada es una imagen preprocesada.

| Nombre de entrada       | Forma de entrada  | Tipo de entrada | Descripción |
| -------- |----------|-----|--------|
| Entrada | `(batch_size, num_channels, height, width)` | ndarray(float) | La entrada es una imagen preprocesada, con la forma `(1, 3, 600, 800)` para un tamaño de lote de 1 y un alto de 600 y un ancho de 800.|
        
    
### <a name="output-format"></a>Formato de salida

La salida es una tupla de rectángulos, etiquetas y puntuaciones.
  
| Nombre de salida       | Forma de salida  | Tipo de salida | Descripción |
| -------- |----------|-----|------|
| Cuadros | `(n_boxes, 4)`, donde cada rectángulo tiene `x_min, y_min, x_max, y_max` | ndarray(float) | El modelo devuelve *n* rectángulos con sus coordenadas superior izquierda e inferior derecha. |
| Etiquetas | `(n_boxes)`| ndarray(float) | Id. de la etiqueta o de la clase de un objeto en cada rectángulo. |  
| Puntuaciones | `(n_boxes)` | ndarray(float) | Puntuación de confianza de un objeto en cada rectángulo. |    


# <a name="object-detection-with-yolo"></a>[Detección de objetos con YOLO](#tab/object-detect-yolo)

En este ejemplo de detección de objetos se aplica el modelo entrenado en el [conjunto de datos de detección fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) de 128 imágenes y 4 clases o etiquetas para explicar la inferencia del modelo de ONNX. En este ejemplo se entrenan modelos de YOLO para mostrar los pasos de la inferencia. Para obtener más información sobre los modelos de detección de objetos de entrenamiento, vea el [cuaderno de detección de objetos](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection). 

### <a name="input-format"></a>Formato de entrada

La entrada es una imagen preprocesada, con la forma `(1, 3, 640, 640)` para un tamaño de lote de 1 y un alto y ancho de 640. Estos números corresponden a los valores usados en el ejemplo de entrenamiento.        

| Nombre de entrada       | Forma de entrada  | Tipo de entrada | Descripción |
| -------- |----------|-----|--------|
| Entrada | `(batch_size, num_channels, height, width)` | ndarray(float) | La entrada es una imagen preprocesada, con la forma `(1, 3, 600, 800)` para un tamaño de lote de 1 y un alto de 600 y un ancho de 800.|
        
### <a name="output-format"></a>Formato de salida
La salida es una lista de rectángulos, etiquetas y puntuaciones. Para YOLO, necesita la primera salida para extraer los rectángulos, las etiquetas y las puntuaciones.
    
| Nombre de salida       | Forma de salida  | Tipo de salida | Descripción |
| -------- |----------|-----|------|
| Output | `(n_boxes, 6)`, donde cada rectángulo tiene `x_min, y_min, x_max, y_max, confidence_score, class_id` | ndarray(float) | El modelo devuelve *n* rectángulos con sus coordenadas superior izquierda e inferior derecha junto con las puntuaciones de confianza de los objetos, los id. de las clases o los id. de las etiquetas |

# <a name="instance-segmentation"></a>[Segmentación de instancias](#tab/instance-segmentation)

En este ejemplo de la segmentación de instancias, se usa el modelo Mask R-CNN que se ha entrenado en el [conjunto de datos fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjectsMask.zip) con 128 imágenes y 4 clases o etiquetas para explicar la inferencia del modelo de ONNX. Para obtener más información sobre el entrenamiento del modelo de la segmentación de instancias, consulte el [cuaderno de segmentación de instancias](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-instance-segmentation).

### <a name="input-format"></a>Formato de entrada

La entrada es una imagen preprocesada. El modelo de ONNX para Mask R-CNN se ha exportado para trabajar con imágenes de diferentes formas. Se recomienda cambiar su tamaño a un tamaño fijo coherente con los tamaños de las imágenes de entrenamiento, para mejorar el rendimiento.
    
| Nombre de entrada       | Forma de entrada  | Tipo de entrada | Descripción |
| -------- |----------|-----|--------|
| Entrada | `(batch_size, num_channels, height, width)` | ndarray(float) | La entrada es una imagen preprocesada, con la forma `(1, 3, input_image_height, input_image_width)` para un tamaño de lote de 1 y un alto y ancho similares a los de una imagen de entrada. |
        
    
### <a name="output-format"></a>Formato de salida

La salida es una tupla de rectángulos (instancias), etiquetas y puntuaciones.
    
| Nombre de salida       | Forma de salida  | Tipo de salida | Descripción |
| -------- |----------|-----|------|
| Cuadros | `(n_boxes, 4)`, donde cada rectángulo tiene `x_min, y_min, x_max, y_max` | ndarray(float) | El modelo devuelve *n* rectángulos con sus coordenadas superior izquierda e inferior derecha. |
| Etiquetas | `(n_boxes)`| ndarray(float) | Id. de la etiqueta o de la clase de un objeto en cada rectángulo. |  
| Puntuaciones | `(n_boxes)` | ndarray(float) | Puntuación de confianza de un objeto en cada rectángulo. |    
| Máscaras | `(n_boxes, 1, height, width)` | ndarray(float) | Máscaras (polígonos) de objetos detectados con el alto y el ancho de la forma de una imagen de entrada. |    

---

## <a name="preprocessing"></a>Preprocessing (Preprocesamiento)

# <a name="multi-class-image-classification"></a>[Clasificación de imágenes de varias clases](#tab/multi-class)

Siga los siguientes pasos de preprocesamiento para la inferencia del modelo de ONNX:

1. Convierta la imagen a RGB.
2. Cambie el tamaño de la imagen a los valores `valid_resize_size` y `valid_resize_size` correspondientes a los valores usados en la transformación del conjunto de datos de validación durante el entrenamiento. El valor predeterminado para `valid_resize_size` es 256.
3. Recorte en el centro la imagen a `height_onnx_crop_size` y `width_onnx_crop_size`. Esto corresponde a `valid_crop_size` con el valor predeterminado de 224.
4. Cambio de `HxWxC` a `CxHxW`.
5. Convierta al tipo float.
6. Normalice con `mean` = `[0.485, 0.456, 0.406]` y `std` = `[0.229, 0.224, 0.225]` de ImageNet.

Si elige valores distintos para los [hiperparámetros](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `valid_resize_size` y `valid_crop_size` durante el entrenamiento, se deben usar esos valores.

Obtenga la forma de entrada necesaria para el modelo de ONNX.

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>Sin PyTorch

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>Con PyTorch

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

#height and width will be the same for classification
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```

# <a name="multi-label-image-classification"></a>[Clasificación de imágenes con varias etiquetas](#tab/multi-label)

Siga los siguientes pasos de preprocesamiento para la inferencia del modelo de ONNX. Estos pasos son los mismos para la clasificación de imágenes de varias clases.

1. Convierta la imagen a RGB.
2. Cambie el tamaño de la imagen a los valores `valid_resize_size` y `valid_resize_size` correspondientes a los valores usados en la transformación del conjunto de datos de validación durante el entrenamiento. El valor predeterminado para `valid_resize_size` es 256.
3. Recorte en el centro la imagen a `height_onnx_crop_size` y `width_onnx_crop_size`. Esto corresponde a `valid_crop_size` con el valor predeterminado de 224.
4. Cambio de `HxWxC` a `CxHxW`.
5. Convierta al tipo float.
6. Normalice con `mean` = `[0.485, 0.456, 0.406]` y `std` = `[0.229, 0.224, 0.225]` de ImageNet.

Si elige valores distintos para los [hiperparámetros](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `valid_resize_size` y `valid_crop_size` durante el entrenamiento, se deben usar esos valores.

Obtenga la forma de entrada necesaria para el modelo de ONNX.

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>Sin PyTorch

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>Con PyTorch

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```


# <a name="object-detection-with-faster-r-cnn"></a>[Detección de objetos con Faster R-CNN](#tab/object-detect-cnn)

Para la detección de objetos con el algoritmo de Faster R-CNN, siga los mismos pasos de preprocesamiento que la clasificación de imágenes, excepto el recorte de imágenes. Puede cambiar el tamaño de la imagen con un alto de `600` y un ancho de `800`, y obtener el alto y el ancho de entrada esperados con el código siguiente.

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

A continuación, siga los pasos del preprocesamiento.

```python
def preprocess(image, height_onnx, width_onnx):
    """perform pre-processing on raw input image
    
    :param image: raw input image
    :type image: PIL image
    :param height_onnx: expected height of an input image in onnx model
    :type height_onnx: Int
    :param width_onnx: expected width of an input image in onnx model
    :type width_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((width_onnx, height_onnx))
    np_image = np.array(image)
    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # Normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
        
        
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, height_onnx, width_onnx)
```

# <a name="object-detection-with-yolo"></a>[Detección de objetos con YOLO](#tab/object-detect-yolo)

Para la detección de objetos con el algoritmo de YOLO, siga los mismos pasos de preprocesamiento que la clasificación de imágenes, excepto el recorte de imágenes. Puede cambiar el tamaño de la imagen con un alto de `600` y un ancho de `800`, y obtener el alto y el ancho de entrada esperados con el código siguiente.

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

Para el preprocesamiento necesario para YOLO, consulte [yolo_onnx_preprocessing_utils.py](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection).

```python
from yolo_onnx_preprocessing_utils import preprocess

test_image_path = "automl_models_od_yolo/test_image.jpg"
img_data, pad = preprocess(test_image_path)
```

# <a name="instance-segmentation"></a>[Segmentación de instancias](#tab/instance-segmentation)

Siga los siguientes pasos de preprocesamiento para la inferencia del modelo de ONNX:

1. Convierta la imagen a RGB.
2. Cambie el tamaño de la imagen. 
3. Cambio de `HxWxC` a `CxHxW`.
4. Convierta al tipo float.
5. Normalice con `mean` = `[0.485, 0.456, 0.406]` y `std` = `[0.229, 0.224, 0.225]` de ImageNet.

Para `resize_height` y `resize_width`, también puede usar los valores que usó durante el entrenamiento, enlazados por los [hiperparámetros](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `min_size` y `max_size` para Mask R-CNN.

```python
def preprocess(image, resize_height, resize_width):
    """perform pre-processing on raw input image
        
    :param image: raw input image
    :type image: PIL image
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((resize_width,resize_height))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image
# use height and width based on the trained model
resize_height, resize_width = 600, 800 
test_image_path = 'automl_models/test_image.jpg'
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_height, resize_width)

```

---

## <a name="inference-with-onnx-runtime"></a>Inferencia con ONNX Runtime

La inferencia con ONNX Runtime difiere para cada tarea de Computer Vision.

# <a name="multi-class-image-classification"></a>[Clasificación de imágenes de varias clases](#tab/multi-class)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="multi-label-image-classification"></a>[Clasificación de imágenes con varias etiquetas](#tab/multi-label)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-faster-r-cnn"></a>[Detección de objetos con Faster R-CNN](#tab/object-detect-cnn)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
            (No. of boxes, 4) (No. of boxes,) (No. of boxes,)
    :rtype: tuple
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores

boxes, labels, scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-yolo"></a>[Detección de objetos con YOLO](#tab/object-detect-yolo)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
    :rtype: list
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    pred = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return pred[0]

result = get_predictions_from_ONNX(session, img_data)

```

# <a name="instance-segmentation"></a>[Segmentación de instancias](#tab/instance-segmentation)

El modelo de la segmentación de instancias predice los rectángulos, las etiquetas, las puntuaciones y las máscaras. ONNX genera una máscara de predicción por instancia, junto con los rectángulos delimitadores correspondientes y la puntuación de confianza de la clase. Es posible que tenga que convertir de máscara binaria a polígono si es necesario.

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores , masks with shapes
            (No. of instances, 4) (No. of instances,) (No. of instances,)
            (No. of instances, 1, HEIGHT, WIDTH))  
    :rtype: tuple
    """
    
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores, masks = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores, masks


boxes, labels, scores, masks = get_predictions_from_ONNX(session, img_data)
```

---

## <a name="postprocessing"></a>Posprocesamiento

# <a name="multi-class-image-classification"></a>[Clasificación de imágenes de varias clases](#tab/multi-class)

Aplique `softmax()` en los valores de la predicción para obtener las puntuaciones (probabilidades) de confianza de la clasificación para cada clase. A continuación, la predicción será la clase con la probabilidad más alta. 

### <a name="without-pytorch"></a>Sin PyTorch

```python

def softmax(x):
    x = x.reshape(-1)
    e_x = np.exp(x - np.max(x))
    return e_x / e_x.sum(axis=0)
conf_scores = softmax(scores).tolist()
class_idx = np.argmax(conf_scores)
print("predicted class:",(class_idx, classes[class_idx]))

```

### <a name="with-pytorch"></a>Con PyTorch

```python

conf_scores = torch.nn.functional.softmax(torch.from_numpy(scores), dim=1).tolist()[0]
class_idx = np.argmax(conf_scores)
print("predicted class:", (class_idx, classes[class_idx]))
```

# <a name="multi-label-image-classification"></a>[Clasificación de imágenes con varias etiquetas](#tab/multi-label)

Este paso difiere de la clasificación de varias clases. Debe aplicar `sigmoid` a los logits (salida de ONNX) para obtener las puntuaciones de confianza para la clasificación de imágenes de varias etiquetas.

### <a name="without-pytorch"></a>Sin PyTorch

```python

import math

def sigmoid(x):
    return 1 / (1 + math.exp(-x))

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
for class_idx, prob in enumerate([sigmoid(logit) for logit in scores[0]]):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

### <a name="with-pytorch"></a>Con PyTorch

```python

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
conf_scores = torch.sigmoid(torch.from_numpy(scores[0])).tolist()
for class_idx, prob in enumerate(conf_scores):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

Para la clasificación de varias clases y varias etiquetas, puede seguir los mismos pasos mencionados anteriormente para todos los algoritmos admitidos en AutoML.


# <a name="object-detection-with-faster-r-cnn"></a>[Detección de objetos con Faster R-CNN](#tab/object-detect-cnn)

```python
def _get_box_dims(image_shape, box):
    box_keys = ['topX', 'topY', 'bottomX', 'bottomY']
    height, width = image_shape[0], image_shape[1]

    box_dims = dict(zip(box_keys, [coordinate.item() for coordinate in box]))

    box_dims['topX'] = box_dims['topX'] * 1.0 / width
    box_dims['bottomX'] = box_dims['bottomX'] * 1.0 / width
    box_dims['topY'] = box_dims['topY'] * 1.0 / height
    box_dims['bottomY'] = box_dims['bottomY'] * 1.0 / height

    return box_dims

def _get_prediction(boxes, labels, scores, image_shape, classes):
    bounding_boxes = []
    for box, label_index, score in zip(boxes, labels, scores):
        box_dims = _get_box_dims(image_shape, box)

        box_record = {'box': box_dims,
                      'label': classes[label_index],
                      'score': score.item()}

        bounding_boxes.append(box_record)

    return bounding_boxes

bounding_boxes = _get_prediction(boxes, labels, scores, (height_onnx,width_onnx), classes)
print(json.dumps(bounding_boxes, indent=1))

# Filter the results with a threshold.
# Replace the threshold for your test scenario.
score_threshold = 0.8
filtered_bounding_boxes = []
for box in bounding_boxes:
    if box['score'] >= score_threshold:
        filtered_bounding_boxes.append(box)
```

### <a name="visualize-boxes"></a>Visualización de rectángulos

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw a box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```

# <a name="object-detection-with-yolo"></a>[Detección de objetos con YOLO](#tab/object-detect-yolo)

El código siguiente crea rectángulos, etiquetas y puntuaciones. Use estos detalles del rectángulo delimitador para seguir los mismos pasos de posprocesamiento que siguió para el modelo de Faster R-CNN. 

```python
from yolo_onnx_preprocessing_utils import non_max_suppression, _convert_to_rcnn_output
import torch

result = non_max_suppression(
    torch.from_numpy(result),
    conf_thres=.1,
    iou_thres=.5)
label, image_shape = _convert_to_rcnn_output(result[0], height_onnx, width_onnx, pad)
boxes = np.array(label["boxes"])
labels = np.array(label["labels"])
labels = [label[0] for label in labels]
scores = np.array(label["scores"])
scores = [score[0] for score in scores]
boxes, labels, scores
```

### <a name="visualize-boxes"></a>Visualización de rectángulos

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw a box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```


# <a name="instance-segmentation"></a>[Segmentación de instancias](#tab/instance-segmentation)

#### <a name="visualize-the-masks-and-bounding-boxes"></a>Visualización de las máscaras y los rectángulos delimitadores

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.patches as patches
import cv2
%matplotlib inline

def display_detections(image, boxes, labels, scores, masks, resize_height, 
                       resize_width, classes, score_threshold=0.3):
    """visualize boxes and masks
    
    :param image: raw image
    :type image: PIL image
    :param boxes: box with shape (No. of instances, 4) 
    :type boxes: ndarray 
    :param labels: classes with shape (No. of instances,) 
    :type labels: ndarray
    :param scores: scores with shape (No. of instances,)
    :type scores: ndarray
    :param masks: masks with shape (No. of instances, 1, HEIGHT, WIDTH) 
    :type masks:  ndarray
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :param classes: classes with shape (No. of classes) 
    :type classes:  list
    :param score_threshold: threshold on scores in the range of 0-1
    :type score_threshold: float
    :return: None     
   
    """

    _, ax = plt.subplots(1, figsize=(12,9))

    image = np.array(image)
    original_height = image.shape[0]
    original_width = image.shape[1]

    for mask, box, label, score in zip(masks, boxes, labels, scores):        
        if score <= score_threshold:
            continue
        mask = mask[0, :, :, None]        
        # resize boxes to original raw input size
        box = [box[0]*original_width/resize_width, 
               box[1]*original_height/resize_height, 
               box[2]*original_width/resize_width, 
               box[3]*original_height/resize_height]
        
        mask = cv2.resize(mask, (image.shape[1],image.shape[0]), 0, 0, interpolation = cv2.INTER_NEAREST)
        # mask is a matrix with values in the range of [0,1]
        # higher values indicate the presence of an object and vice versa
        # select the threshold or cutoff value to get objects present               
        mask = mask > 0.5 
        image_masked = image.copy()
        image_masked[mask] = (0, 255, 255)
        alpha = .5 # alpha blending with range 0 to 1
        cv2.addWeighted(image_masked, alpha, image, 1 - alpha,0, image)        
        rect = patches.Rectangle((box[0], box[1]), box[2] - box[0], box[3] - box[1],\
                                 linewidth=1, edgecolor='b', facecolor='none')
        ax.annotate(classes[label] + ':' + str(np.round(score, 2)), (box[0], box[1]),\
                    color='w', fontsize=12)
        ax.add_patch(rect)
        

    ax.imshow(image)
    plt.show()

display_detections(img, boxes.copy(), labels, scores, masks.copy(), 
                   resize_height, resize_width, classes, score_threshold=.5)
```

---


## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre las tareas de Computer Vision en AutoML](how-to-auto-train-image-models.md)
* [Solución de problemas de los experimentos de AutoML](how-to-troubleshoot-auto-ml.md)

