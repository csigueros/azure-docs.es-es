---
title: Definición de componente (versión preliminar)
titleSuffix: Azure Machine Learning
description: Use componentes de Azure Machine Learning para compilar canalizaciones de aprendizaje automático.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: laobri
ms.date: 10/21/2021
ms.topic: conceptual
ms.openlocfilehash: 5846a84df6c11364fed6fa65b852b55c42cd5364
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566038"
---
# <a name="what-is-an-azure-machine-learning-component-preview"></a>Definición de componente de Azure Machine Learning (versión preliminar)

Un componente de Azure Machine Learning (conocido anteriormente como módulo) es un fragmento de código independiente que realiza un paso en una canalización de aprendizaje automático. Los componentes son los bloques de creación de las canalizaciones avanzadas de aprendizaje automático (vea [Creación y ejecución de canalizaciones de aprendizaje automático con la CLI de Azure Machine Learning](how-to-create-component-pipelines-cli.md)). Los componentes pueden realizar tareas como el procesamiento de datos, el entrenamiento de modelos, la puntuación de modelos, etc.

Un componente es análogo a una función: tiene un nombre, parámetros, espera una entrada y devuelve una salida. Para obtener más información sobre la creación de un componente, vea [Creación de un componente](#define-a-component-preview).

## <a name="why-should-i-use-a-component-preview"></a>Motivos por los que usar un componente (versión preliminar)

Los componentes permiten administrar y reutilizar lógica común entre canalizaciones.

- **Admiten composición**: los componentes permiten a los desarrolladores ocultar lógica complicada tras una interfaz sencilla. Los usuarios de componentes no tienen que preocuparse por la lógica subyacente, solo necesitan proporcionar parámetros.

- **Uso compartido y reutilización**: los componentes se comparten automáticamente con los usuarios de la misma área de trabajo. Puede reutilizar componentes entre canalizaciones, entornos, áreas de trabajo y suscripciones. El seguimiento de versiones integrado permite realizar un seguimiento de los cambios y reproducir los resultados.

- **Compatibilidad con la CLI**: use componentes para crear canalizaciones en la CLI (v2).


## <a name="define-a-component-preview"></a>Definición de componente (versión preliminar)

Para definir un componente de Azure Machine Learning, debe proporcionar dos archivos:

- Una especificación de componente con el [formato de especificación de componentes de YAML](reference-yaml-component-command.md) válido. Este archivo especifica la siguiente información:
  - Metadatos: nombre, nombre para mostrar, versión, tipo, etc.
  - Interfaz: entradas y salidas
  - Comando, código y entorno: el comando, el código y el entorno usados para ejecutar el componente
- Un script para proporcionar la lógica de ejecución real.

### <a name="component-specification"></a>Especificación de componentes

El archivo de especificación de componentes define los metadatos y los parámetros de ejecución de un componente. La especificación de componentes indica a Azure Machine Learning cómo ejecutar el script de Python que se proporciona.

El ejemplo siguiente es una especificación de componentes de un componente de entrenamiento.

```yaml
name: Example_Train
display_name: Example Train
version: 20
type: command
description: Example of a torchvision training component
tags: {category: Component Tutorial, contact: user@contoso.com}
inputs:
  training_data: 
    type: path
    description: Training data organized in torchvision structure
  max_epochs:
    type: integer
    description: Maximum epochs for training
  learning_rate: 
    type: number
    description: Learning rate, default is 0.01
    default: 0.01
  learning_rate_schedule: 
    type: string
    default: time-based 
outputs:
  model_output:
    type: path
code:
  local_path: ./train_src
environment: azureml:AzureML-Minimal:1
command: >-
  python train.py 
  --training_data ${{inputs.training_data}} 
  --max_epochs ${{inputs.max_epochs}}   
  --learning_rate ${{inputs.learning_rate}} 
  --learning_rate_schedule ${{inputs.learning_rate_schedule}} 
  --model_output ${{outputs.model_output}}
```

En la tabla siguiente se explican los campos del ejemplo. Para obtener una lista completa de los campos disponibles, vea la [página de referencia de especificación de componentes de YAML](reference-yaml-component-command.md).

| Nombre                | Tipo                                                     | Obligatorio | Descripción                                                  |
| ------------------- | -------------------------------------------------------- | -------- | ------------------------------------------------------------ |
| name                | string                                                   | Sí      | Nombre del componente. Debe ser un identificador único del componente. Debe comenzar con número o una letra, y solo contener letras, números, `_` y `-`. La longitud máxima es de 255 caracteres.|
| version             | string                                                   | Sí      | Versión del componente. Debe ser una cadena. |
| display_name        | string                                                   | No       | Nombre para mostrar del componente. El valor predeterminado es igual a `name`. |
| type                | string                                                   | No       | Tipo del componente. De momento, este valor debe ser `command`.|
| description         | string                                                   | No       | Descripción detallada del componente. |
| etiquetas                | Dictionary&lt;string&gt;                                       | No | Lista de pares clave-valor para describir las distintas perspectivas del componente. La clave y el valor de cada etiqueta deben ser una palabra o una frase corta, por ejemplo, `Product:Office`, `Domain:NLP`, `Scenario:Image Classification`. |
| is_deterministic    | boolean                                                  | No       | Si el componente siempre va a generar el mismo resultado con los mismos datos de entrada. El valor predeterminado es `True`. Debe establecerse en `False` para los componentes que cargan datos de recursos externos, por ejemplo, mediante la importación de datos desde una dirección URL determinada, ya que los datos se pueden actualizar. |
| inputs              | Dictionary&lt;string, Input&gt; | No       | Define los puertos de entrada y los parámetros del componente. La clave de cadena es el nombre de la entrada, que debe ser un nombre de variable de Python válido. |
| outputs             | Dictionary&lt;string, Output&gt;                    | No       | Define los puertos de salida del componente. La clave de cadena es el nombre de la salida, que debe ser un nombre de variable de Python válido. |
| código                | string                                                   | No       | Ruta de acceso al código fuente. |
| Environment         | Entorno                              | No       | Entorno en tiempo de ejecución para que se ejecute el componente. |
| command             | string                                             | No    | Comando para ejecutar el código del componente.         |

### <a name="python-script"></a>Script de Python

El script de Python contiene la lógica ejecutable del componente. El script indica a Azure Machine Learning lo que quiere que haga el componente.

Para ejecutarlo, debe emparejar los argumentos del script de Python con los argumentos definidos en la especificación de YAML. El ejemplo siguiente es un script de entrenamiento de Python que se empareja con la especificación de YAML de la sección anterior.

```python
## Required imports 
import argparse
import os
## Import other dependencies your script needs
from pathlib import Path
from uuid import uuid4
from datetime import datetime

## Define an argument parser that matches the arguments from the components specification file
parser = argparse.ArgumentParser("train")
parser.add_argument("--training_data", type=str, help="Path to training data")
parser.add_argument("--max_epochs", type=int, help="Max # of epochs for the training")
parser.add_argument("--learning_rate", type=float, help="Learning rate")
parser.add_argument("--learning_rate_schedule", type=str, help="Learning rate schedule")
parser.add_argument("--model_output", type=str, help="Path of output model")

args = parser.parse_args()

## Implement your custom logic (in this case a training script)
print ("hello training world...")

lines = [
    f'Training data path: {args.training_data}',
    f'Max epochs: {args.max_epochs}',
    f'Learning rate: {args.learning_rate}',
    f'Learning rate: {args.learning_rate_schedule}',
    f'Model output path: {args.model_output}',
]

for line in lines:
    print(line)

print("mounted_path files: ")
arr = os.listdir(args.training_data)
print(arr)

for filename in arr:
    print ("reading file: %s ..." % filename)
    with open(os.path.join(args.training_data, filename), 'r') as handle:
        print (handle.read())

## Do the train and save the trained model as a file into the output folder.
## Here only output a dummy data for example.
curtime = datetime.now().strftime("%b-%d-%Y %H:%M:%S")
model = f"This is a dummy model with id: {str(uuid4())} generated at: {curtime}\n"
(Path(args.model_output) / 'model.txt').write_text(model)

```

:::image type="content" source="media/concept-component/component-introduction.png" lightbox="media/concept-component/component-introduction.png" alt-text="Documento conceptual que muestra la asignación entre elementos de código fuente y la interfaz de usuario del componente." :::

## <a name="create-a-component"></a>Crear un componente

### <a name="create-a-component-using-cli-v2"></a>Creación de un componente mediante la CLI (v2)

Después de definir la especificación de componentes y los archivos de script de Python e [instalar la CLI (v2) correctamente](how-to-configure-cli.md),puede crear el componente en las áreas de trabajo mediante:

```azurecli
az ml component create --file my_component.yml --version 1 --resource-group my-resource-group --workspace-name my-workspace
```

Use `az ml component create --help` para obtener más información sobre el comando `create`.

### <a name="create-a-component-in-the-studio-ui"></a>Creación de un componente en la interfaz de usuario de Studio

Puede crear un componente en la página **Componentes** de la interfaz de usuario de Studio.

1. Haga clic en **Nuevo componente** en la página de componentes.

    :::image type="content" source="./media/concept-component/ui-create-component.png" lightbox="./media/concept-component/ui-create-component.png" alt-text="Captura de pantalla que muestra el botón Nuevo componente.":::

1. Siga el asistente para finalizar el proceso de creación.

## <a name="use-components-to-build-ml-pipelines"></a>Uso de componentes para compilar canalizaciones de ML

Puede usar la CLI de Azure (v2) para crear un trabajo de canalización. Vea [Creación y ejecución de canalizaciones de ML (CLI)](how-to-create-component-pipelines-cli.md).

## <a name="manage-components"></a>Administración de componentes

Puede comprobar los detalles de un componente y administrarlo mediante la CLI (v2). Use `az ml component -h` para obtener instrucciones detalladas sobre los comandos de componentes.

### <a name="list-components"></a>Enumeración de componentes

Puede usar `az ml component list` para enumerar todos los componentes de un área de trabajo.

Puede ver todos los componentes creados en el área de trabajo en la página **Componentes** de la interfaz de usuario de Studio.

### <a name="show-details-for-a-component"></a>Visualización de detalles de un componente

Puede usar `az ml component show --name <COMPONENT_NAME>` para mostrar los detalles de un componente.

También puede examinar los detalles del componente en la página **Componentes** de la interfaz de usuario de Studio.


### <a name="upgrade-a-component"></a>Actualización de un componente

Puede usar `az ml component create --file <NEW_VERSION.yaml>` para actualizar un componente.

También puede hacer clic en **Actualizar** en la página de detalles del componente para actualizar a una nueva versión del componente.

:::image type="content" source="./media/concept-component/upgrade-component.png" lightbox="./media/concept-component/upgrade-component.png" alt-text="Captura de pantalla que muestra el botón Actualizar.":::

### <a name="delete-a-component"></a>Eliminar un componente

Puede usar `az ml component delete --name <COMPONENT_NAME>` para eliminar un componente. 

También puede seleccionar un componente y archivarlo.

:::image type="content" source="./media/concept-component/archive-component.png" alt-text="Captura de pantalla que muestra la opción de archivo de un componente.":::

## <a name="next-steps"></a>Pasos siguientes

- [Referencia de YAML de componentes](reference-yaml-component-command.md)
- [Creación y ejecución de canalizaciones de ML(CLI)](how-to-create-component-pipelines-cli.md)
- [Compilación de canalizaciones de aprendizaje automático en el diseñador](tutorial-designer-automobile-price-train-score.md)
