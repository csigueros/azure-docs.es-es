---
title: 'Tutorial: Creación del modelo predictivo con un cuaderno (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a crear e implementar un modelo de aprendizaje automático mediante código en un cuaderno de Jupyter Notebook. Cree también un script de puntuación que defina la entrada y la salida para facilitar la integración en Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6e71116d79496340c306628a29504aa140e9d5f5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429915"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Tutorial: Integración de Power BI: creación del modelo predictivo con un cuaderno de Jupyter Notebook (parte 1 de 2)

En la parte 1 de este tutorial, va a entrenar e implementar un modelo de aprendizaje automático predictivo mediante código en un cuaderno de Jupyter Notebook. También creará un script de puntuación para definir el esquema de entrada y salida del modelo para la integración en Power BI.  En la parte 2, usará el modelo para predecir los resultados en Microsoft Power BI.

En este tutorial ha:

> [!div class="checklist"]
> * Crear un cuaderno de Jupyter Notebook.
> * Creará una instancia de proceso de Azure Machine Learning.
> * Entrenará un modelo de regresión mediante scikit-learn.
> * Escriba un script de puntuación que defina la entrada y la salida para facilitar la integración en Microsoft Power BI.
> * Implementará el modelo en un punto de conexión de puntuación en tiempo real.

Existen tres formas de crear e implementar el modelo que se va a usar en Power BI.  En este artículo se describe la "Opción A: Entrenamiento e implementación de modelos mediante cuadernos".  Esta opción es una experiencia de creación de tipo "código primero". En ella se usan cuadernos de Jupyter Notebook que se hospedan en Azure Machine Learning Studio. 

Sin embargo, podría usar una de las otras opciones:

* [Opción B: Entrenamiento e implementación de modelos con el diseñador de Azure Machine Learning](tutorial-power-bi-designer-model.md). En esta experiencia de creación con poco código se usa una interfaz de usuario basada en el método de arrastrar y colocar.
* [Opción C: Entrenamiento e implementación de modelos mediante el aprendizaje automático automatizado](tutorial-power-bi-automated-model.md). En esta experiencia de creación sin código se automatiza totalmente la preparación de los datos y el entrenamiento del modelo.


## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. Si aún no tiene una suscripción, puede usar una [evaluación gratuita](https://azure.microsoft.com/free/). 
- Un área de trabajo de Azure Machine Learning. Si aún no tiene un área de trabajo, consulte [Creación y administración de áreas de trabajo de Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conocimiento introductorio del lenguaje Python y los flujos de trabajo de aprendizaje automático.

## <a name="create-a-notebook-and-compute"></a>Creación de un cuaderno y un recurso de proceso

En la página principal de [**Azure Machine Learning Studio**](https://ml.azure.com), seleccione **Crear nuevo** > **Cuaderno**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Captura de pantalla que muestra cómo crear un cuaderno.":::
 
En la página **Create a new file** (Crear un archivo nuevo):

1. Asigne al cuaderno un nombre (por ejemplo, *my_model_notebook*).
1. Cambie **Tipo de archivo** a **Cuaderno**.
1. Seleccione **Crear**. 
 
A continuación, para ejecutar las celdas de código, cree una instancia de proceso y asóciela al cuaderno. Para comenzar, seleccione el icono de signo más en la parte superior del cuaderno:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Captura de pantalla que muestra cómo crear una instancia de proceso":::

En la página **Create compute instance** (Crear instancia de proceso):

1. Elija un tamaño de máquina virtual de CPU. En este tutorial, puede elegir un tamaño **Standard_D11_v2**, con 2 núcleos y 14 GB de RAM.
1. Seleccione **Next** (Siguiente). 
1. En la página **Parámetros de configuración**, proporcione un **nombre de proceso** válido. Los caracteres válidos son mayúsculas y minúsculas, dígitos y guiones (-).
1. Seleccione **Crear**.

En el cuaderno, es posible que observe que el círculo situado junto a **Proceso** se vuelve de color cian. Este cambio de color indica que se está creando la instancia de proceso:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Captura de pantalla que muestra la creación de un proceso.":::

> [!NOTE]
> La instancia de proceso puede tardar entre 2 y 4 minutos en aprovisionarse.

Finalizada esta operación, puede usar el cuaderno para ejecutar las celdas de código. Por ejemplo, en la celda puede escribir el código siguiente:

```python
import numpy as np

np.sin(3)
```

Luego, seleccione Mayús + Entrar (o seleccione Control + Entrar o el botón **Reproducir** situado junto a la celda). Debería ver la siguiente salida:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Captura de pantalla que muestra la salida de una celda.":::

Ya está listo para crear un modelo de aprendizaje automático.

## <a name="build-a-model-by-using-scikit-learn"></a>Creación de un modelo con scikit-learn

En este tutorial, usará el [conjunto de datos Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Este conjunto de datos está disponible en [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Importar datos

Para importar los datos, copie el código siguiente y péguelo en una nueva *celda de código* del cuaderno.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

La trama de datos de pandas `X_df` contiene 10 variables de entrada de base de referencia. Estas variables incluyen la edad, el sexo, el índice de masa corporal, la presión arterial media y seis mediciones del suero sanguíneo. La trama de datos de pandas `y_df` es la variable de destino. Contiene una medida cuantitativa de la progresión de la enfermedad un año después de la base de referencia. La trama de datos contiene 442 registros.

### <a name="train-the-model"></a>Entrenamiento del modelo

Cree una *celda de código* en el cuaderno. A continuación, copie el siguiente código y péguelo en la celda. Este fragmento de código crea un modelo de regresión contraída y serializa el modelo con el formato pickle de Python.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registro del modelo

Además del contenido del propio archivo de modelo, el modelo registrado almacenará los metadatos. Los metadatos incluyen la descripción, las etiquetas y la información del marco del modelo. 

Los metadatos son útiles cuando se administran e implementan modelos en el área de trabajo. Mediante etiquetas, por ejemplo, puede clasificar los modelos y aplicar filtros al enumerar los modelos del área de trabajo. Además, si marca este modelo con el marco scikit-learn, simplificará su implementación como un servicio web.

Copie el código siguiente y péguelo en una nueva *celda de código* del cuaderno.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

También puede ver el modelo en Azure Machine Learning Studio. En el menú de la izquierda, seleccione **Modelos**:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Captura de pantalla que muestra cómo ver un modelo.":::

## <a name="define-the-scoring-script"></a>Definición del script de puntuación

Al implementar un modelo que se va a integrar en Power BI, debe definir un *script de puntuación* de Python y un entorno personalizado. El script de puntuación contiene dos funciones:

- La función `init()` se ejecuta cuando se inicia el servicio. Carga el modelo (que se descarga automáticamente del registro de modelos) y lo deserializa.
- La función `run(data)` se ejecuta cuando una llamada al servicio incluye datos de entrada que se deben puntuar. 

>[!NOTE]
> Los decoradores de Python del siguiente código definen el esquema de los datos de entrada y salida, que es importante para la integración en Power BI.

Copie el código siguiente y péguelo en una nueva *celda de código* del cuaderno. El siguiente fragmento de código tiene una instrucción mágica de celda que escribe el código para un archivo denominado *score.py*.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definición del entorno personalizado

A continuación, defina el entorno para puntuar el modelo. En el entorno, defina los paquetes de Python, como pandas y scikit-learn, que requiere el script de puntuación (*score.py*).

Para definir el entorno, copie el código siguiente y péguelo en una nueva *celda de código* del cuaderno.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Implementación del modelo

Para implementar el modelo, copie el código siguiente y péguelo en una nueva *celda de código* del cuaderno:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> El servicio puede tardar entre 2 y 4 minutos en implementarse.

Si el servicio se implementa correctamente, verá la siguiente salida:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

También puede ver el servicio en Azure Machine Learning Studio. En el menú de la izquierda, seleccione **Puntos de conexión**:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Captura de pantalla que muestra cómo ver el servicio.":::

Se recomienda probar el servicio web para asegurarse de que funciona según lo previsto. Para devolver el cuaderno, en Azure Machine Learning Studio, en el menú de la izquierda, seleccione **Cuadernos**. A continuación, copie el siguiente código y péguelo en una nueva *celda de código*  del cuaderno para probar el servicio.

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

La salida debe parecerse a esta estructura JSON: `{'predict': [[205.59], [68.84]]}`.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha visto cómo crear e implementar un modelo para que pueda usarse con Power BI. En la siguiente parte, aprenderá a consumir este modelo en un informe de Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumo de un modelo en Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
