---
title: Formato JSONL para tareas de Computer Vision
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo dar formato a los archivos JSONL para el consumo de datos en experimentos de ML automatizado para tareas de Computer Vision.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: reference
ms.reviewer: nibaccam
ms.author: rvadthyavath
author: vadthyavath
ms.date: 10/13/2021
ms.custom: ''
ms.openlocfilehash: 376ba24800b3d547a302d83eef960c58afd73054
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007062"
---
# <a name="data-schemas-to-train-computer-vision-models-with-automated-machine-learning"></a>Esquemas de datos para entrenar modelos de Computer Vision con aprendizaje automático automatizado

Obtenga información sobre cómo dar formato a los archivos JSONL para el consumo de datos en experimentos de ML automatizado para tareas de Computer Vision durante el entrenamiento y la inferencia.


## <a name="data-schema-for-training"></a>Esquema de datos para el entrenamiento 

El aprendizaje automático automatizado para imágenes de Azure Machine Learning requiere que los datos de la imagen de entrada se preparen en formato [JSONL](https://jsonlines.org/) (JSON Lines). En esta sección, se describen los formatos de datos de entrada o el esquema para la clasificación de imágenes de varias clases, la clasificación de imágenes con varias etiquetas, la detección de objetos y la segmentación de instancias. También se proporcionará un ejemplo del archivo JSON Lines de entrenamiento o validación final.

### <a name="image-classification-binarymulti-class"></a>Clasificación de imágenes (binaria/multiclase)

**Esquema o formato de datos de entrada en cada línea JSON:**
```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":"class_name",
}
```

| Clave       | Descripción  | Ejemplo |
| -------- |----------|-----|
| image_url | Ubicación de la imagen en el almacén de datos de AML<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| image_details | Detalles de la imagen<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| format  | Tipo de imagen (se admiten todos los formatos de imagen disponibles en la biblioteca [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html))<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif","bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| width | Ancho de la imagen<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| height | Altura de la imagen<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| etiqueta | Clase o etiqueta de la imagen<br>`Required, String` | `"cat"` |


Ejemplo de un archivo JSONL para la clasificación de imágenes de varias clases:
```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": "can"}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": "milk_bottle"}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": "water_bottle"}
  ```

### <a name="image-classification-multi-label"></a>Clasificación de imágenes con varias etiquetas

A continuación, se muestra un ejemplo de esquema o formato de datos de entrada de cada línea JSON para la clasificación de imágenes.


```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      "class_name_1",
      "class_name_2",
      "class_name_3",
      "...",
      "class_name_n"
        
   ]
}
```

| Clave       | Descripción  | Ejemplo |
| -------- |----------|-----|
| image_url | Ubicación de la imagen en el almacén de datos de AML<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| image_details | Detalles de la imagen<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| format  | Tipo de imagen (se admiten todos los formatos de imagen disponibles en la biblioteca [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html))<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| width | Ancho de la imagen<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| height | Altura de la imagen<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| etiqueta | Lista de clases o etiquetas de la imagen<br>`Required, List of Strings` | `["cat","dog"]` |


Ejemplo de un archivo JSONL para la clasificación de imágenes con varias etiquetas:

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": ["can"]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": ["can","milk_bottle"]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": ["carton","milk_bottle","water_bottle"]}
  ```

### <a name="object-detection"></a>Detección de objetos

A continuación, se muestra un archivo JSONL de ejemplo para la detección de objetos.

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name_1",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      {
         "label":"class_name_2",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      "..."
   ]
}
```

Aquí, 
- xmin = coordenada X de la esquina superior izquierda del rectángulo delimitador
- ymin = coordenada Y de la esquina superior izquierda del rectángulo delimitador
- xmax = coordenada X de la esquina inferior derecha del rectángulo delimitador
- ymax = coordenada Y de la esquina inferior derecha del rectángulo delimitador



| Clave       | Descripción  | Ejemplo |
| -------- |----------|-----|
| image_url | Ubicación de la imagen en el almacén de datos de AML<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| image_details | Detalles de la imagen<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| format  | Tipo de imagen (se admiten todos los formatos de imagen disponibles en la biblioteca [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html). Sin embargo, para YOLO solo se admiten los formatos de imagen permitidos por [opencv](https://pypi.org/project/opencv-python/4.3.0.36/)).<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| width | Ancho de la imagen<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| height | Altura de la imagen<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| label (clave externa) | Lista de rectángulos delimitadores, donde cada uno es un diccionario de `label, topX, topY, bottomX, bottomY, isCrowd`, sus coordenadas superior izquierda e inferior derecha<br>`Required, List of dictionaries` | `[{"label": "cat", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]` |
| label (clave interna)| Clase o etiqueta del objeto del rectángulo delimitador<br>`Required, String` | `"cat"` |
| topX | Proporción de la coordenada X de la esquina superior izquierda del rectángulo delimitador y el ancho de la imagen<br>`Required, Float in the range [0,1]` | `0.260` |
| topY | Proporción de la coordenada Y de la esquina superior izquierda del rectángulo delimitador y la altura de la imagen<br>`Required, Float in the range [0,1]` | `0.406` |
| bottomX | Proporción de la coordenada X de la esquina inferior derecha del rectángulo delimitador y el ancho de la imagen<br>`Required, Float in the range [0,1]` | `0.735` |
| bottomY | Proporción de la coordenada Y de la esquina inferior derecha del rectángulo delimitador y la altura de la imagen<br>`Required, Float in the range [0,1]` | `0.701` |
| isCrowd | Indica si el rectángulo delimitador está alrededor de la multitud de objetos. Si se establece esta marca especial, se omite este rectángulo delimitador específico al calcular la métrica.<br>`Optional, Bool` | `0` |


Ejemplo de un archivo JSONL para la detección de objetos:

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.172, "topY": 0.153, "bottomX": 0.432, "bottomY": 0.659, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.300, "topY": 0.566, "bottomX": 0.891, "bottomY": 0.735, "isCrowd": 0}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.0180, "topY": 0.297, "bottomX": 0.380, "bottomY": 0.836, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.454, "topY": 0.348, "bottomX": 0.613, "bottomY": 0.683, "isCrowd": 0}, {"label": "water_bottle", "topX": 0.667, "topY": 0.279, "bottomX": 0.841, "bottomY": 0.615, "isCrowd": 0}]}
  ```

### <a name="instance-segmentation"></a>Segmentación de instancias

Para la segmentación de instancias, ML automatizado solo admite polígono como entrada y salida, sin máscaras.

A continuación, se muestra un archivo JSONL de ejemplo para la segmentación de instancias.

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name",
         "isCrowd":"isCrowd",
         "polygon":[["x1", "y1", "x2", "y2", "x3", "y3", "...", "xn", "yn"]]
      }
   ]
}
```

| Clave       | Descripción  | Ejemplo |
| -------- |----------|-----|
| image_url | Ubicación de la imagen en el almacén de datos de AML<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| image_details | Detalles de la imagen<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| format  | Tipo de imagen<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff" }`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| width | Ancho de la imagen<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| height | Altura de la imagen<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| label (clave externa) | Lista de máscaras, donde cada máscara es un diccionario de `label, isCrowd, polygon coordinates` <br>`Required, List of dictionaries` | ` [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689,`<br> ` 0.562, 0.681,`<br> `0.559, 0.686]]}]` |
| label (clave interna)| Clase o etiqueta del objeto de la máscara<br>`Required, String` | `"cat"` |
| isCrowd | Indica si la máscara está alrededor de la multitud de objetos.<br>`Optional, Bool` | `0` |
| polygon | Coordenadas del polígono para el objeto<br>`Required,  List of list for multiple segments of the same instance. Float values in the range [0,1]` | ` [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686]]` |


Ejemplo de un archivo JSONL para la segmentación de instancias:

```python
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686, 0.380, 0.593, 0.304, 0.555, 0.294, 0.545, 0.290, 0.534, 0.274, 0.512, 0.2705, 0.496, 0.270, 0.478, 0.284, 0.453, 0.308, 0.432, 0.326, 0.423, 0.356, 0.415, 0.418, 0.417, 0.635, 0.493, 0.683, 0.507, 0.701, 0.518, 0.709, 0.528, 0.713, 0.545, 0.719, 0.554, 0.719, 0.579, 0.713, 0.597, 0.697, 0.621, 0.695, 0.629, 0.631, 0.678, 0.619, 0.683, 0.595, 0.683, 0.577, 0.689]]}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "isCrowd": 0, "polygon": [[0.240, 0.65, 0.234, 0.654, 0.230, 0.647, 0.210, 0.512, 0.202, 0.403, 0.182, 0.267, 0.184, 0.243, 0.180, 0.166, 0.186, 0.159, 0.198, 0.156, 0.396, 0.162, 0.408, 0.169, 0.406, 0.217, 0.414, 0.249, 0.422, 0.262, 0.422, 0.569, 0.342, 0.569, 0.334, 0.572, 0.320, 0.585, 0.308, 0.624, 0.306, 0.648, 0.240, 0.657]]}, {"label": "milk_bottle",  "isCrowd": 0, "polygon": [[0.675, 0.732, 0.635, 0.731, 0.621, 0.725, 0.573, 0.717, 0.516, 0.717, 0.505, 0.720, 0.462, 0.722, 0.438, 0.719, 0.396, 0.719, 0.358, 0.714, 0.334, 0.714, 0.322, 0.711, 0.312, 0.701, 0.306, 0.687, 0.304, 0.663, 0.308, 0.630, 0.320, 0.596, 0.32, 0.588, 0.326, 0.579]]}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "water_bottle", "isCrowd": 0, "polygon": [[0.334, 0.626, 0.304, 0.621, 0.254, 0.603, 0.164, 0.605, 0.158, 0.602, 0.146, 0.602, 0.142, 0.608, 0.094, 0.612, 0.084, 0.599, 0.080, 0.585, 0.080, 0.539, 0.082, 0.536, 0.092, 0.533, 0.126, 0.530, 0.132, 0.533, 0.144, 0.533, 0.162, 0.525, 0.172, 0.525, 0.186, 0.521, 0.196, 0.521 ]]}, {"label": "milk_bottle", "isCrowd": 0, "polygon": [[0.392, 0.773, 0.380, 0.732, 0.379, 0.767, 0.367, 0.755, 0.362, 0.735, 0.362, 0.714, 0.352, 0.644, 0.352, 0.611, 0.362, 0.597, 0.40, 0.593, 0.444,  0.494, 0.588, 0.515, 0.585, 0.621, 0.588, 0.671, 0.582, 0.713, 0.572, 0.753 ]]}]}
```

## <a name="data-format-for-inference"></a>Formato de datos para la inferencia

En esta sección, se documenta el formato de datos de entrada necesario para realizar predicciones cuando se usa un modelo implementado. Cualquier formato de imagen mencionado anteriormente se acepta con el tipo de contenido `application/octet-stream`.

### <a name="input-format"></a>Formato de entrada

A continuación, se muestra el formato de entrada necesario para generar predicciones en cualquier tarea mediante el punto de conexión del modelo específico de la tarea. Después de [implementar el modelo](how-to-auto-train-image-models.md#register-and-deploy-model), podemos usar el siguiente fragmento de código para obtener predicciones para todas las tareas.

```python
# input image for inference
sample_image = './test_image.jpg'
# load image data
data = open(sample_image, 'rb').read()
# set the content type
headers = {'Content-Type': 'application/octet-stream'}
# if authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'
# make the request and display the response
response = requests.post(scoring_uri, data, headers=headers)
```
### <a name="output-format"></a>Formato de salida

Las predicciones realizadas en los puntos de conexión del modelo siguen una estructura diferente en función del tipo de tarea. En esta sección, se exploran los formatos de datos de salida para las tareas de clasificación de imágenes de varias clases y varias etiquetas, la detección de objetos y la segmentación de instancias.  

#### <a name="image-classification"></a>Clasificación de imágenes

El punto de conexión para la clasificación de imágenes devuelve todas las etiquetas del conjunto de datos y sus puntuaciones de probabilidad para la imagen de entrada en el formato siguiente.

```json
{
   "filename":"/tmp/tmppjr4et28",
   "probs":[
      2.098e-06,
      4.783e-08,
      0.999,
      8.637e-06
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="image-classification-multi-label"></a>Clasificación de imágenes con varias etiquetas

Para la clasificación de imágenes con varias etiquetas, el punto de conexión del modelo devuelve las etiquetas y sus probabilidades.

```json
{
   "filename":"/tmp/tmpsdzxlmlm",
   "probs":[
      0.997,
      0.960,
      0.982,
      0.025
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="object-detection"></a>Detección de objetos

El modelo de detección de objetos devuelve varios rectángulos con sus coordenadas superior izquierda e inferior derecha a escala junto con la etiqueta del rectángulo y la puntuación de confianza.

```json
{
   "filename":"/tmp/tmpdkg2wkdy",
   "boxes":[
      {
         "box":{
            "topX":0.224,
            "topY":0.285,
            "bottomX":0.399,
            "bottomY":0.620
         },
         "label":"milk_bottle",
         "score":0.937
      },
      {
         "box":{
            "topX":0.664,
            "topY":0.484,
            "bottomX":0.959,
            "bottomY":0.812
         },
         "label":"can",
         "score":0.891
      },
      {
         "box":{
            "topX":0.423,
            "topY":0.253,
            "bottomX":0.632,
            "bottomY":0.725
         },
         "label":"water_bottle",
         "score":0.876
      }
   ]
}
```
#### <a name="instance-segmentation"></a>Segmentación de instancias

En la segmentación de instancias, la salida consta de varios rectángulos con sus coordenadas superior izquierda e inferior derecha a escala, las etiquetas, las puntuaciones de confianza y los polígonos (no máscaras). En este caso, los valores del polígono están en el mismo formato que se ha descrito en la sección de esquema.

```json
{
   "filename":"/tmp/tmpi8604s0h",
   "boxes":[
      {
         "box":{
            "topX":0.679,
            "topY":0.491,
            "bottomX":0.926,
            "bottomY":0.810
         },
         "label":"can",
         "score":0.992,
         "polygon":[
            [
               0.82, 0.811, 0.771, 0.810, 0.758, 0.805, 0.741, 0.797, 0.735, 0.791, 0.718, 0.785, 0.715, 0.778, 0.706, 0.775, 0.696, 0.758, 0.695, 0.717, 0.698, 0.567, 0.705, 0.552, 0.706, 0.540, 0.725, 0.520, 0.735, 0.505, 0.745, 0.502, 0.755, 0.493
            ]
         ]
      },
      {
         "box":{
            "topX":0.220,
            "topY":0.298,
            "bottomX":0.397,
            "bottomY":0.601
         },
         "label":"milk_bottle",
         "score":0.989,
         "polygon":[
            [
               0.365, 0.602, 0.273, 0.602, 0.26, 0.595, 0.263, 0.588, 0.251, 0.546, 0.248, 0.501, 0.25, 0.485, 0.246, 0.478, 0.245, 0.463, 0.233, 0.442, 0.231, 0.43, 0.226, 0.423, 0.226, 0.408, 0.234, 0.385, 0.241, 0.371, 0.238, 0.345, 0.234, 0.335, 0.233, 0.325, 0.24, 0.305, 0.586, 0.38, 0.592, 0.375, 0.598, 0.365
            ]
         ]
      },
      {
         "box":{
            "topX":0.433,
            "topY":0.280,
            "bottomX":0.621,
            "bottomY":0.679
         },
         "label":"water_bottle",
         "score":0.988,
         "polygon":[
            [
               0.576, 0.680, 0.501, 0.680, 0.475, 0.675, 0.460, 0.625, 0.445, 0.630, 0.443, 0.572, 0.440, 0.560, 0.435, 0.515, 0.431, 0.501, 0.431, 0.433, 0.433, 0.426, 0.445, 0.417, 0.456, 0.407, 0.465, 0.381, 0.468, 0.327, 0.471, 0.318
            ]
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre la [Preparación de los datos para tareas de Computer Vision con aprendizaje automático automatizado](how-to-prepare-datasets-for-automl-images.md).
