---
title: 'Tutorial: AutoML: entrenamiento del modelo de detección de objetos'
titleSuffix: Azure Machine Learning
description: Entrene un modelo de detección de objetos para predecir tarifas de taxi de Nueva York con el SDK de Python para Azure Machine Learning mediante aprendizaje automático automatizado de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: swatig007
ms.author: swatig
ms.reviewer: nibaccam
ms.date: 10/06/2021
ms.custom: devx-track-python, automl
ms.openlocfilehash: a8b299a57b5ba3d31d34447ceacded9933c61b55
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007686"
---
# <a name="tutorial-train-an-object-detection-model-preview-with-automl-and-python"></a>Tutorial: Entrenamiento de un modelo de detección de objetos (versión preliminar) con AutoML y Python

>[!IMPORTANT]
> Las características que se presentan en este artículo están en versión preliminar. Por tanto, deben considerarse características [experimentales](/python/api/overview/azure/ml/#stable-vs-experimental) que pueden cambiar en cualquier momento.

En este tutorial, aprenderá a entrenar un modelo de detección de objetos mediante aprendizaje automático automatizado de Azure Machine Learning con el SDK de Python para Azure Machine Learning. Este modelo de detección de objetos identifica si la imagen contiene objetos, como una lata, una caja, una botella de leche o una botella de agua.

El aprendizaje automático automatizado acepta tanto valores de configuración como datos de entrenamiento y, después, realiza una iteración automática por las combinaciones de diferentes métodos de estandarización o normalización de características, modelos y valores de hiperparámetros para llegar al mejor modelo.


En este tutorial escribirá código con el SDK de Python y aprenderá las siguientes tareas:

> [!div class="checklist"]
> * Descargar y transformar datos
> * Entrenar un modelo de detección de objetos de aprendizaje automático automatizado
> * Especificar valores de hiperparámetros para el modelo
> * Realizar un barrido de hiperparámetros
> * Implementación del modelo
> * Visualizar detecciones

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago](https://azure.microsoft.com/free/) de Azure Machine Learning.

* Python 3.6 o 3.7 son compatibles con esta característica

* Complete los pasos que se describen en [Inicio rápido: Introducción a Azure Machine Learning](quickstart-create-resources.md#create-the-workspace) si aún no tiene un área de trabajo de Azure Machine Learning.

* Descargue el archivo de datos [**odFridgeObjects.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

Este tutorial también está disponible en [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) si quiere ejecutarlo en su propio [entorno local](how-to-configure-environment.md#local). Para obtener los paquetes obligatorios,
* Ejecute `pip install azureml`:
* [Instale el cliente `automl` completo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment).

## <a name="compute-target-setup"></a>Configuración de destino de proceso

En primer lugar, debe configurar un destino de proceso para usarlo para el entrenamiento del modelo de aprendizaje automático automatizado. Los modelos de aprendizaje automático automatizado para tareas de imagen requieren SKU de GPU.

En este tutorial se usa la serie NCsv3 (con GPU V100), ya que este tipo de destino de proceso aprovecha varias GPU para acelerar el entrenamiento. Además, puede configurar varios nodos para aprovechar el paralelismo al ajustar hiperparámetros para el modelo.

El código siguiente crea un proceso de GPU de tamaño estándar _NC24s_v3 con cuatro nodos que están asociados al área de trabajo `ws`.

> [!WARNING]
> Asegúrese de que la suscripción tiene una cuota suficiente para el destino de proceso que desea usar.

```python
from azureml.core.compute import AmlCompute, ComputeTarget

cluster_name = "gpu-cluster-nc24s_v3"

try:
    compute_target = ws.compute_targets[cluster_name]
    print('Found existing compute target.')
except KeyError:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='Standard_NC24s_v3',
                                                           idle_seconds_before_scaledown=1800,
                                                           min_nodes=0,
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

#If no min_node_count is provided, the scale settings are used for the cluster.
compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="experiment-setup"></a>Configuración del experimento
A continuación, cree un elemento `Experiment` en el área de trabajo para realizar un seguimiento de las ejecuciones de entrenamiento del modelo.

```python

from azureml.core import Experiment

experiment_name = 'automl-image-object-detection'
experiment = Experiment(ws, name=experiment_name)
```

## <a name="visualize-input-data"></a>Visualización de datos de entrada

Una vez que tenga los datos de la imagen de entrada preparados en [JSONL](https://jsonlines.org/) (líneas JSON), puede visualizar los rectángulos delimitadores de datos ciertos para una imagen. Para ello, asegúrese de que ha instalado `matplotlib`.

```
%pip install --upgrade matplotlib
```
```python

%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image as pil_image
import numpy as np
import json
import os

def plot_ground_truth_boxes(image_file, ground_truth_boxes):
    # Display the image
    plt.figure()
    img_np = mpimg.imread(image_file)
    img = pil_image.fromarray(img_np.astype("uint8"), "RGB")
    img_w, img_h = img.size

    fig,ax = plt.subplots(figsize=(12, 16))
    ax.imshow(img_np)
    ax.axis("off")

    label_to_color_mapping = {}

    for gt in ground_truth_boxes:
        label = gt["label"]

        xmin, ymin, xmax, ymax =  gt["topX"], gt["topY"], gt["bottomX"], gt["bottomY"]
        topleft_x, topleft_y = img_w * xmin, img_h * ymin
        width, height = img_w * (xmax - xmin), img_h * (ymax - ymin)

        if label in label_to_color_mapping:
            color = label_to_color_mapping[label]
        else:
            # Generate a random color. If you want to use a specific color, you can use something like "red".
            color = np.random.rand(3)
            label_to_color_mapping[label] = color

        # Display bounding box
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=2, edgecolor=color, facecolor="none")
        ax.add_patch(rect)

        # Display label
        ax.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

    plt.show()

def plot_ground_truth_boxes_jsonl(image_file, jsonl_file):
    image_base_name = os.path.basename(image_file)
    ground_truth_data_found = False
    with open(jsonl_file) as fp:
        for line in fp.readlines():
            line_json = json.loads(line)
            filename = line_json["image_url"]
            if image_base_name in filename:
                ground_truth_data_found = True
                plot_ground_truth_boxes(image_file, line_json["label"])
                break
    if not ground_truth_data_found:
        print("Unable to find ground truth information for image: {}".format(image_file))

def plot_ground_truth_boxes_dataset(image_file, dataset_pd):
    image_base_name = os.path.basename(image_file)
    image_pd = dataset_pd[dataset_pd['portable_path'].str.contains(image_base_name)]
    if not image_pd.empty:
        ground_truth_boxes = image_pd.iloc[0]["label"]
        plot_ground_truth_boxes(image_file, ground_truth_boxes)
    else:
        print("Unable to find ground truth information for image: {}".format(image_file))
```

Con las funciones auxiliares anteriores, para cualquier imagen determinada puede ejecutar el código siguiente para mostrar los rectángulos delimitadores.

```python
image_file = "./odFridgeObjects/images/31.jpg"
jsonl_file = "./odFridgeObjects/train_annotations.jsonl"

plot_ground_truth_boxes_jsonl(image_file, jsonl_file)
```

## <a name="upload-data-and-create-dataset"></a>Carga de datos y creación de un conjunto de datos

Para usar los datos para el entrenamiento, cárguelos en el área de trabajo mediante un almacén de datos. El almacén de datos proporciona un mecanismo para cargar o descargar datos e interactuar con ellos desde los destinos de proceso remotos.

```python
ds = ws.get_default_datastore()
ds.upload(src_dir='./odFridgeObjects', target_path='odFridgeObjects')
```

Una vez cargados en el almacén de datos, puede crear un conjunto de datos de Azure Machine Learning a partir de los datos. Los conjuntos de datos empaquetan los datos en un objeto personalizable para el entrenamiento.

El siguiente código crea un conjunto de datos para el entrenamiento. Puesto que no se especifica ningún conjunto de datos de validación, de forma predeterminada se usa el 20 % de los datos de entrenamiento para la validación.

``` python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'odFridgeObjectsTrainingDataset'
if training_dataset_name in ws.datasets:
    training_dataset = ws.datasets.get(training_dataset_name)
    print('Found the training dataset', training_dataset_name)
else:
    # create training dataset
        # create training dataset
    training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/train_annotations.jsonl'),
        set_column_types={"image_url": DataType.to_stream(ds.workspace)},
    )
    training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

### <a name="visualize-dataset"></a>Visualización del conjunto de datos

También puede visualizar los rectángulos delimitadores de datos ciertos para una imagen de este conjunto de datos.

Cargue el conjunto de datos en un dataframe de Pandas.

```python
import azureml.dataprep as dprep

from azureml.dataprep.api.functions import get_portable_path

# Get pandas dataframe from the dataset
dflow = training_dataset._dataflow.add_column(get_portable_path(dprep.col("image_url")),
                                              "portable_path", "image_url")
dataset_pd = dflow.to_pandas_dataframe(extended_types=True)
```

Para cualquier imagen determinada, puede ejecutar el código siguiente para mostrar los rectángulos delimitadores.

```python
image_file = "./odFridgeObjects/images/31.jpg"
plot_ground_truth_boxes_dataset(image_file, dataset_pd)
```

## <a name="configure-your-object-detection-experiment"></a>Configuración del experimento de detección de objetos

Para configurar ejecuciones de aprendizaje automático automatizado para tareas relacionadas con imágenes, use el objeto `AutoMLImageConfig`. En `AutoMLImageConfig`, puede especificar los algoritmos de modelo con el parámetro `model_name` y configurar los valores para realizar un barrido de hiperparámetros en un espacio de parámetros definido para encontrar el modelo óptimo.

En este ejemplo, se usa `AutoMLImageConfig` para entrenar un modelo de detección de objetos con `yolov5` y `fasterrcnn_resnet50_fpn`, que se han entrenado previamente COCO, un conjunto de datos de detección, segmentación y descripción de objetos a gran escala que contiene miles de imágenes etiquetadas con más de 80 categorías de etiquetas.

### <a name="hyperparameter-sweeping-for-image-tasks"></a>Barrido de hiperparámetros para tareas de imagen

Puede realizar un barrido de hiperparámetros en un espacio de parámetros definido para encontrar el modelo óptimo.

El código siguiente define el espacio de parámetros para preparar el barrido de hiperparámetros para cada algoritmo definido, `yolov5` y `fasterrcnn_resnet50_fpn`.  En el espacio de parámetros, especifique el intervalo de valores de `learning_rate`, `optimizer`, `lr_scheduler`, etc. para que AutoML elija mientras intenta generar un modelo con la métrica principal óptima. Si no se especifican valores de hiperparámetros, se usan valores predeterminados para cada algoritmo.

Para la configuración de ajuste, use el muestreo aleatorio para seleccionar muestras de este espacio de parámetros mediante la importación de las clases `GridParameterSampling, RandomParameterSampling` y `BayesianParameterSampling`.  Al hacerlo, indica al aprendizaje automático automatizado que pruebe un total de 20 iteraciones con estas muestras distintas, ejecutando cuatro iteraciones a la vez en el destino de proceso, que se ha configurado con cuatro nodos. Cuantos más parámetros tenga el espacio, más iteraciones necesitará para encontrar modelos óptimos.

También se usa la directiva de bandidos de terminación anticipada. Esta directiva finaliza configuraciones de bajo rendimiento; es decir, aquellas configuraciones que no están dentro del margen del 20 % de la configuración de mejor rendimiento, que ahorra recursos de proceso de forma considerable.

```python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import BanditPolicy, HyperDriveConfig
from azureml.train.hyperdrive import choice, uniform

parameter_space = {
    'model': choice(
        {
            'model_name': choice('yolov5'),
            'learning_rate': uniform(0.0001, 0.01),
            #'model_size': choice('small', 'medium'), # model-specific
            'img_size': choice(640, 704, 768), # model-specific
        },
        {
            'model_name': choice('fasterrcnn_resnet50_fpn'),
            'learning_rate': uniform(0.0001, 0.001),
            #'warmup_cosine_lr_warmup_epochs': choice(0, 3),
            'optimizer': choice('sgd', 'adam', 'adamw'),
            'min_size': choice(600, 800), # model-specific
        }
    )
}

tuning_settings = {
    'iterations': 20,
    'max_concurrent_iterations': 4,
    'hyperparameter_sampling': RandomParameterSampling(parameter_space),
    'policy': BanditPolicy(evaluation_interval=2, slack_factor=0.2, delay_evaluation=6)
}
```

Una vez definidos el espacio de parámetros y la configuración de ajuste, puede pasarlos al objeto `AutoMLImageConfig` y, a continuación, enviar el experimento para entrenar un modelo de imagen mediante el conjunto de datos de entrenamiento.

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(task='image-object-detection',
                                        compute_target=compute_target,
                                        training_data=training_dataset,
                                        validation_data=validation_dataset,
                                        primary_metric='mean_average_precision',
                                        **tuning_settings)

automl_image_run = experiment.submit(automl_image_config)
automl_image_run.wait_for_completion(wait_post_processing=True)
```

Al realizar un barrido de hiperparámetros, puede ser útil visualizar las distintas configuraciones que se probaron con la interfaz de usuario de HyperDrive. Para navegar a esta interfaz de usuario, vaya a la pestaña "Ejecuciones secundarias" de la interfaz de usuario de la instancia principal de automl_image_run anterior, que es la ejecución primaria de HyperDrive. A continuación, puede ir a la pestaña "Ejecuciones secundarias" de esta. Como alternativa, aquí puede ver directamente la ejecución primaria de HyperDrive e ir a su pestaña "Ejecuciones secundarias":

```python
from azureml.core import Run
hyperdrive_run = Run(experiment=experiment, run_id=automl_image_run.id + '_HD')
hyperdrive_run
```

## <a name="register-the-best-model"></a>Registro del mejor modelo

Una vez completada la ejecución, podemos registrar el modelo que se creó a partir de la mejor ejecución.

```python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

## <a name="deploy-model-as-a-web-service"></a>Implementación de un modelo como un servicio web

Cuando tenga el modelo entrenado, podrá implementarlo en Azure. Puede implementar el modelo entrenado como un servicio web en Azure Container Instances (ACI) o Azure Kubernetes Service (AKS). ACI es la opción ideal para probar las implementaciones, mientras que AKS es más adecuado para el uso de producción a gran escala.

En este tutorial, implementaremos el modelo como un servicio web en AKS.

1. Cree un clúster de proceso de AKS. En este ejemplo, se usa una SKU de máquina virtual de GPU para el clúster de implementación.

    ```python
    from azureml.core.compute import ComputeTarget, AksCompute
    from azureml.exceptions import ComputeTargetException

    # Choose a name for your cluster
    aks_name = "cluster-aks-gpu"

    # Check to see if the cluster already exists
    try:
        aks_target = ComputeTarget(workspace=ws, name=aks_name)
        print('Found existing compute target')
    except ComputeTargetException:
        print('Creating a new compute target...')
        # Provision AKS cluster with GPU machine
        prov_config = AksCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                            location="eastus2")
        # Create the cluster
        aks_target = ComputeTarget.create(workspace=ws,
                                          name=aks_name,
                                          provisioning_configuration=prov_config)
        aks_target.wait_for_completion(show_output=True)
    ```

1. Defina la configuración de inferencia que describe cómo establecer el servicio web que contiene el modelo. Puede usar el script de puntuación y el entorno de la ejecución de entrenamiento en la configuración de inferencia.

    > [!NOTE]
    > Para cambiar la configuración del modelo, abra el script de puntuación descargado y modifique la variable model_settings antes de implementar el modelo.

    ```python
    from azureml.core.model import InferenceConfig

    best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
    environment = best_child_run.get_environment()
    inference_config = InferenceConfig(entry_script='score.py', environment=environment)
    ```

1. A continuación, puede implementar el modelo como un servicio web de AKS.

    ```python

    from azureml.core.webservice import AksWebservice
    from azureml.core.webservice import Webservice
    from azureml.core.model import Model
    from azureml.core.environment import Environment

    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                    cpu_cores=1,
                                                    memory_gb=50,
                                                    enable_app_insights=True)

    aks_service = Model.deploy(ws,
                               models=[model],
                               inference_config=inference_config,
                               deployment_config=aks_config,
                               deployment_target=aks_target,
                               name='automl-image-test',
                               overwrite=True)
    aks_service.wait_for_deployment(show_output=True)
    print(aks_service.state)
    ```

## <a name="test-the-web-service"></a>Prueba del servicio web

Puede probar el servicio web implementado para predecir nuevas imágenes. En este tutorial, pase una imagen aleatoria del conjunto de datos al URI de puntuación.

```python
import requests

# URL for the web service
scoring_uri = aks_service.scoring_uri

# If the service is authenticated, set the key or token
key, _ = aks_service.get_keys()

sample_image = './test_image.jpg'

# Load image data
data = open(sample_image, 'rb').read()

# Set the content type
headers = {'Content-Type': 'application/octet-stream'}

# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, data, headers=headers)
print(resp.text)
```
## <a name="visualize-detections"></a>Visualización de detecciones
Ahora que ha puntuado una imagen de prueba, puede visualizar los rectángulos delimitadores de esta imagen. Para ello, asegúrese de que tiene matplotlib instalado.

```
%pip install --upgrade matplotlib
```

```python
%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
import numpy as np
import json

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np=mpimg.imread(sample_image)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size

fig,ax = plt.subplots(1, figsize=(15,15))
# Display the image
ax.imshow(img_np)

# draw box and label for each detection
detections = json.loads(resp.text)
for detect in detections['boxes']:
    label = detect['label']
    box = detect['box']
    conf_score = detect['score']
    if conf_score > 0.6:
        ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
        topleft_x, topleft_y = x * xmin, y * ymin
        width, height = x * (xmax - xmin), y * (ymax - ymin)
        print('{}: [{}, {}, {}, {}], {}'.format(detect['label'], round(topleft_x, 3),
                                                round(topleft_y, 3), round(width, 3),
                                                round(height, 3), round(conf_score, 3)))

        color = np.random.rand(3) #'red'
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=3, edgecolor=color,facecolor='none')

        ax.add_patch(rect)
        plt.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

plt.show()
```

## <a name="clean-up-resources"></a>Limpieza de recursos

No complete esta sección si planea ejecutar otros tutoriales de Azure Machine Learning.

Si no va a usar los recursos creados, elimínelos para no incurrir en cargos.

1. En Azure Portal, seleccione **Grupos de recursos** a la izquierda del todo.
1. En la lista, seleccione el grupo de recursos que creó.
1. Seleccione **Eliminar grupo de recursos**.
1. Escriba el nombre del grupo de recursos. A continuación, seleccione **Eliminar**.

También puede mantener el grupo de recursos pero eliminar una sola área de trabajo. Muestre las propiedades del área de trabajo y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial de aprendizaje automático, ha realizado las tareas siguientes:

> [!div class="checklist"]
> * Ha configurado un área de trabajo y ha preparado datos para un experimento.
> * Ha entrenado un modelo de detección de objetos automatizado.
> * Ha especificado valores de hiperparámetros para el modelo.
> * Ha realizado un barrido de hiperparámetros.
> * Ha implementado el modelo.
> * Ha visualizado detecciones.

* [Más información sobre Computer Vision en aprendizaje automático automatizado (versión preliminar)](concept-automated-ml.md#computer-vision-preview).
* [Aprenda a configurar AutoML para entrenar modelos de Computer Vision con Python (versión preliminar)](how-to-auto-train-image-models.md).
* Revise los ejemplos de código y los casos de uso detallados en el repositorio del [cuaderno de GitHub para obtener muestras de aprendizaje automático automatizado](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml). Consulte las carpetas con el prefijo "image-" para ver ejemplos específicos de la creación de modelos de Computer Vision.
