---
title: Creación y ejecución de canalizaciones de ML basadas en componentes (CLI)
titleSuffix: Azure Machine Learning
description: Cree y ejecute canalizaciones de aprendizaje automático con la CLI de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 71e611855023becf474337b309f510244507bc96
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848465"
---
# <a name="create-and-run-machine-learning-pipelines-using-components-with-the-azure-machine-learning-cli-preview"></a>Creación y ejecución de canalizaciones de aprendizaje automático mediante componentes con la CLI de Azure Machine Learning (versión preliminar)

En este artículo, aprenderá a crear y ejecutar [canalizaciones de aprendizaje automático](concept-ml-pipelines.md) mediante componentes y la CLI de Azure (para más información, vea [Definición de componente de Azure Machine Learning](concept-component.md)). Puede crear [canalizaciones sin usar componentes](how-to-train-cli.md#build-a-training-pipeline), pero los componentes ofrecen el máximo nivel de flexibilidad y reutilización. AzureML Pipelines se puede definir en YAML y ejecutarse desde la CLI, crearse en Python o componerse en el Diseñador de AzureML Studio con una interfaz de usuario de arrastrar y colocar. Este documento se centra en la CLI.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prerrequisitos

* Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* Necesitará un [área de trabajo de Azure Machine Learning](how-to-manage-workspace.md) para las canalizaciones y los recursos asociados.

* [Instalación y configuración de la extensión de la CLI de Azure para Machine Learning](how-to-configure-cli.md)

* Clone el repositorio de ejemplos:

    ```azurecli-interactive
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli/jobs/pipelines-with-components/
    ```

## <a name="introducing-machine-learning-pipelines"></a>Introducción a las canalizaciones de aprendizaje automático

Las canalizaciones en AzureML le permiten secuenciar una colección de tareas de aprendizaje automático en un flujo de trabajo. Los científicos de datos suelen iterar con scripts que se centran en tareas individuales, como la preparación de datos, el entrenamiento, la puntuación, etc. Cuando todos estos scripts están listos, las canalizaciones ayudan a conectar una colección de estos scripts a procesos de calidad de producción que son:

| Prestación | Descripción |
| --- | --- |
| Independiente | Las canalizaciones pueden ejecutarse de forma independiente durante horas, o incluso días, subiendo datos, procesándolos y pasándolos a los scripts posteriores sin ninguna intervención manual. |
| Eficaz | Las canalizaciones pueden ejecutarse en clústeres de proceso grandes hospedados en la nube que tienen la capacidad de procesamiento para analizar grandes conjuntos de datos o para realizar miles de barridos para encontrar los mejores modelos. | 
| Repetible y automatizable | Las canalizaciones se pueden programar para ejecutar y procesar nuevos datos y actualizar modelos de ML, lo que permite que los flujos de trabajo de ML sean repetibles. | 
| Reproducible | Las canalizaciones pueden generar resultados reproducibles registrando toda la actividad y conservando todas las salidas, incluidos los datos intermedios en la nube, lo que permite cumplir los requisitos de cumplimiento y auditoría. |

Azure tiene otros tipos de canalizaciones: las canalizaciones de Azure Data Factory tienen una gran compatibilidad con las canalizaciones de datos a datos, mientras que Azure Pipelines es la mejor opción para la automatización de CI/CD. [Compare las canalizaciones de Machine Learning con estas canalizaciones diferentes](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

## <a name="create-your-first-pipeline"></a>Cree su primera canalización

Desde el directorio `cli/jobs/pipelines-with-components/basics` del [repositorio `azureml-examples`](https://github.com/Azure/azureml-examples), vaya al subdirectorio `3a_basic_pipeline` (los ejemplos anteriores de dicho directorio muestran canalizaciones sin componentes). Enumere los recursos de proceso disponibles con el siguiente comando: 

```azurecli
az ml compute list
```

Si no lo tiene, cree un clúster llamado `cpu-cluster` mediante la ejecución de:

```azurecli
az ml compute create -n cpu-cluster --type amlcompute --min-instances 0 --max-instances 10
```

Ahora, cree un trabajo de canalización con el siguiente comando:

```azurecli
az ml job create --file pipeline.yml
```

Debe recibir un diccionario JSON con información sobre el trabajo de canalización, con la información siguiente:

| Clave | Descripción | 
| --- | --- | 
| `name` | Nombre basado en GUID del trabajo. | 
| `experiment_name` | Nombre con el que se organizarán los trabajos en Studio. | 
| `services.Studio.endpoint` | Dirección URL para supervisar y revisar el trabajo de canalización. | 
| `status` | El estado del trabajo. Es probable que sea `Preparing` en este momento. |

### <a name="review-a-component"></a>Revisión de un componente 

 Eche un vistazo rápido al código fuente de Python en `componentA_src`, `componentB_src` y `componentC_src`. Como puede ver, cada uno de estos directorios contiene un programa "Hola mundo" ligeramente diferente en Python. 

Abra `ComponentA.yaml` para ver cómo se define el primer componente: 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/componentA.yml":::

En la versión preliminar actual, solo se admiten componentes del tipo `command`. `name` es el identificador único y se usa en Studio para describir el componente, y `display_name` se usa para proporcionar un nombre descriptivo. El par clave-valor `version` permite desarrollar los componentes de canalización mientras se mantiene la reproducibilidad con versiones anteriores. 

Todos los archivos del valor `code.local_path` se cargarán en Azure para su procesamiento. 

La sección `environment` permite especificar el entorno de software en el que se ejecuta el componente. En este caso, el componente usa una imagen base de Docker, como se especifica en `environment.image`. Para más información, vea [Creación y uso de entornos de software en Azure Machine Learning](how-to-use-environments.md). 

Por último, la clave `command` se usa para especificar el comando que se va a ejecutar.

> [!NOTE]
> El valor de `command` empieza con `>-` que es YAML "folding style with block-chomping". Esto le permite escribir el comando en varias líneas de texto para mayor claridad.

Para más información sobre los componentes y su especificación, vea [Definición de componente de Azure Machine Learning](concept-component.md).

### <a name="review-the-pipeline-specification"></a>Revisión de la especificación de canalización

En el directorio de ejemplo, el archivo `pipeline.yaml` es similar al código siguiente:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/pipeline.yml":::

Si abre la dirección URL del trabajo en Studio (el valor de `services.Studio.endpoint` a partir del comando `job create` al crear un trabajo o `job show` una vez creado el trabajo), verá una representación gráfica de la canalización:

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph.png" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph.png" alt-text="Representación gráfica de la canalización en Studio":::

No hay dependencias entre los componentes de esta canalización. Por lo general, las canalizaciones tendrán dependencias y se mostrarán visualmente en esta página. Dado que estos componentes no dependen entre sí y, puesto que `cpu-cluster` tenía suficientes nodos, se ejecutaron simultáneamente. 

Si hace doble clic en un componente en el gráfico de la canalización, puede ver los detalles de la ejecución secundaria del componente. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/component-details.png" alt-text="Captura de pantalla que muestra los detalles y las salidas de la ejecución secundaria de un componente" lightbox="media/how-to-create-component-pipelines-cli/component-details.png":::. 

## <a name="upload-and-use-data"></a>Carga y uso de datos

En el ejemplo `3b_pipeline_with_data` se muestra cómo definir el flujo de datos de entrada y salida y el almacenamiento en canalizaciones. 

Defina directorios de datos de entrada para la canalización en el archivo YAML de la canalización mediante la ruta de acceso `inputs`. Los directorios de datos intermedios y de salida se definen mediante la ruta de acceso `outputs`. Estas definiciones se usan en las rutas de acceso `jobs.<JOB_NAME>.inputs` y `jobs.<JOB_NAME>.outputs`, como se muestra en la siguiente imagen:

:::image type="content" source="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png" alt-text="Imagen que muestra cómo se asignan las rutas de acceso de entrada y salida a las rutas de acceso de entrada y salida de los trabajos" lightbox="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png":::.

1. La ruta de acceso `inputs.pipeline_sample_input_data` crea un identificador de clave y carga los datos de entrada desde el directorio `local_path`. A continuación, este identificador `${{inputs.pipeline_sample_input_data}}` se usa como el valor de la clave `jobs.componentA_job.inputs.componentA_input`. 
1. La ruta de acceso `jobs.componentA_job.outputs.componentA_output` como identificador (`${{jobs.componentA_job.outputs.componentA_output`}}) que se usa como el valor de la clave `jobs.componentB_job.inputs.componentB_input` en el paso siguiente. 
1. Al igual que con el componente A, la salida del componente B se usa como entrada para el componente C.
1. La clave `outputs.final_pipeline_output` de la canalización es el origen del identificador utilizado como el valor de la clave `jobs.componentC_job.outputs.componentC_output`. En otras palabras, la salida del componente C es la salida final de la canalización.

La visualización de la canalización en Studio tiene el aspecto siguiente: 

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png" alt-text="Captura de pantalla que muestra la vista gráfica en Studio de una canalización con dependencias de datos" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png":::

Puede ver que `inputs.pipeline_sample_input_data` se representa como `Dataset`. Las claves de las rutas de acceso `jobs.<COMPONENT_NAME>.inputs` y `outputs` se muestran como flujos de datos entre los componentes de la canalización.

Para ejecutar este ejemplo, cambie al subdirectorio `3b_pipeline_with_data` del repositorio de ejemplos y ejecute:

`az ml job create --file pipeline.yaml`

### <a name="access-data-in-your-script"></a>Acceso a datos en el script

Las rutas de acceso del directorio de entrada y salida de un componente se pasan al script como argumentos. El nombre del argumento será la clave que especificó en el archivo YAML en la ruta de acceso `inputs` o `outputs`. Por ejemplo:

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--componentA_input", type=str)
parser.add_argument("--componentA_output", type=str)

args = parser.parse_args()

print("componentA_input path: %s" % args.componentA_input)
print("componentA_output path: %s" % args.componentA_output)

```

En el caso de las entradas, el orquestador de canalizaciones descarga o monta los datos del almacén en la nube y los habilita como una carpeta local desde la que leer el script que se ejecuta en cada trabajo. Este comportamiento indica que el script no necesita ninguna modificación entre la ejecución local y la ejecución en el proceso de la nube. Del mismo modo, para las salidas, el script escribe en una carpeta local que se monta en el almacén de la nube o que se sincroniza con él, o bien se carga una vez completado el script. Puede usar la palabra clave `mode` para especificar la descarga en lugar del montaje para las entradas y la carga en lugar del montaje para las salidas. 

## <a name="create-a-preparation-train-evaluate-pipeline"></a>Creación de una canalización de preparación, entrenamiento y evaluación

Uno de los escenarios comunes para las canalizaciones de aprendizaje automático tiene tres fases principales:

1. Preparación de los datos
1. Cursos
1. Evaluación del modelo

Cada una de estas fases puede tener varios componentes. Por ejemplo, el paso de preparación de datos puede tener pasos independientes para cargar y transformar los datos de entrenamiento. El repositorio de ejemplos contiene una canalización de ejemplo de un extremo a otro en el directorio `cli/jobs/pipelines-with-components/nyc_taxi_data_regression`. 

`job.yml` comienza con el par clave-valor `type: pipeline` obligatorio. A continuación, define las entradas y salidas de la siguiente manera:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="5-22":::

Como se ha descrito anteriormente, estas entradas especifican los datos de entrada para la canalización, en este caso el conjunto de datos de `./data`, y las salidas intermedias y finales de la canalización, que se almacenan en rutas de acceso independientes. Los nombres de estas entradas y salidas se convierten en valores en las entradas `inputs` y `outputs` de los trabajos individuales: 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="26-72":::

Observe cómo se usa `jobs.train_job.outputs.model_output` como entrada para el trabajo de predicción y el trabajo de puntuación, como se muestra en el diagrama siguiente: 

:::image type="content" source="media/how-to-create-component-pipelines-cli/regression-graph.png" alt-text="Gráfico de canalizaciones de la tarea de predicción de tarifas de taxi en Nueva York" lightbox="media/how-to-create-component-pipelines-cli/regression-graph.png":::

## <a name="caching--reuse"></a>Almacenamiento en caché y reutilización  

De forma predeterminada, solo se vuelven a ejecutar los componentes cuyas entradas han cambiado. Puede cambiar este comportamiento; para ello, establezca la clave `is_deterministic` del archivo YAML de especificación de componentes en `False`. Para ello, suele ser necesario un componente que carga datos que se pueden haber actualizado desde una ubicación o dirección URL fijas. 

## <a name="faq"></a>Preguntas más frecuentes

### <a name="how-do-i-change-the-location-of-the-outputs-generated-by-the-pipeline"></a>¿Cómo se puede cambiar la ubicación de las salidas generadas por la canalización?
Puede usar la sección `settings` del trabajo de canalización para especificar un almacén de datos diferente para todos los trabajos de la canalización (vea las líneas 25 y 26 de [este ejemplo](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)). Actualmente no se puede especificar un almacén de datos diferente para un trabajo específico o una salida específica. Tampoco se permite actualmente especificar rutas de acceso donde se guardan las salidas en el almacén de datos.

### <a name="how-do-i-specify-a-compute-that-can-be-used-by-all-jobs"></a>¿Cómo puedo especificar un proceso que todos los trabajos puedan usar?
Puede especificar un proceso en el nivel de trabajo de la canalización, que usarán los trabajos que no mencionen explícitamente un proceso. (Vea la línea 28 de [este ejemplo](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)).

### <a name="what-job-types-are-supported-in-the-pipeline-job"></a>¿Qué tipos de trabajos se admiten en el trabajo de canalización?
La versión actual admite tipos de trabajos de comandos y componentes. Para el tipo de trabajo de componente, solo se admite el componente de comando. En futuras versiones, se admitirán más tipos de trabajos, como el barrido.

### <a name="what-are-the-different-modes-that-i-use-with-inputs-or-outputs"></a>¿Cuáles son los diferentes modos que puedo usar con las entradas o salidas?
| Category | Modos permitidos | Valor predeterminado |
| --- | --- | --- |
| Entradas del conjunto de datos | `ro_mount` y `download` | `ro_mount` |
| Entradas de URI | `ro_mount`, `rw_mount` y `download` | `ro_mount` | 
| Salidas | `rw_mount`, `upload` | `rw_mount` | 

### <a name="when-do-i-use-command-jobs-vs-component-jobs"></a>¿Cuándo debo usar trabajos de comandos en lugar de trabajos de componentes?
Puede iterar rápidamente con trabajos de comandos y conectarlos conjuntamente a una canalización. Sin embargo, esto hace que la canalización sea monolítica. Si alguien necesita usar uno de los pasos de la canalización en una canalización diferente, debe copiar la definición del trabajo, los scripts, el entorno, etc. Si desea que los pasos individuales se puedan reutilizar entre canalizaciones y que sean fáciles de entender y usar para otros usuarios del equipo, es conveniente aplicar los pasos adicionales de creación y registro. La otra razón por la que debería considerar el uso de componentes es por el uso de la interfaz de usuario del diseñador de arrastrar y colocar para crear canalizaciones. Como los trabajos no están registrados en el área de trabajo, no puede arrastrarlos y colocarlos en el lienzo del diseñador.

### <a name="im-doing-distributed-training-in-my-component-the-component-which-is-registered-specifies-distributed-training-settings-including-node-count-how-can-i-change-the-number-of-nodes-used-during-runtime-the-optimal-number-of-nodes-is-best-determined-at-runtime-so-i-dont-want-to-update-the-component-and-register-a-new-version"></a>Estoy realizando el entrenamiento distribuido en mi componente. El componente, que está registrado, especifica la configuración del entrenamiento distribuido, incluido el número de nodos. ¿Cómo puedo cambiar el número de nodos usados durante el tiempo de ejecución? El número óptimo de nodos se determina mejor en tiempo de ejecución, por lo que no quiero actualizar el componente ni registrar una nueva versión.

Puede usar la sección de invalidaciones del trabajo del componente para cambiar la configuración de los recursos y de la distribución. Vea [este ejemplo con TensorFlow](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/6a_tf_hello_world/) o [este ejemplo con PyTorch](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/6y_pytorch_hello_world).  

### <a name="how-can-i-define-an-environment-with-conda-dependencies-inside-a-component"></a>¿Cómo puedo definir un entorno con dependencias de Conda dentro de un componente?
Consulte [este ejemplo](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/jobs/pipelines-with-components/basics/5c_env_conda_file).
 

## <a name="next-steps"></a>Pasos siguientes

- Para compartir su canalización con compañeros o clientes, consulte [Publicación de canalizaciones de aprendizaje automático](how-to-deploy-pipelines.md).
- Use [estos cuadernos de Jupyter Notebook en GitHub](https://aka.ms/aml-pipeline-readme) para explorar en profundidad las canalizaciones de aprendizaje automático.
- Consulte la referencia del SDK para obtener ayuda con el paquete [azureml-pipelines-core](/python/api/azureml-pipeline-core/) y el paquete [azureml-pipelines-steps](/python/api/azureml-pipeline-steps/).
- Vea [Solución de problemas de canalizaciones de aprendizaje automático](how-to-debug-pipelines.md) para obtener sugerencias sobre las canalizaciones de depuración y solución de problemas.
- Siga las instrucciones del artículo [Exploración de Azure Machine Learning con cuadernos de Jupyter](samples-notebooks.md) para aprender a ejecutar cuadernos.
