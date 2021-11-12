---
title: Administración de entornos de Azure Machine Learning con la CLI (v2)
titleSuffix: Azure Machine Learning
description: Aprenda a administrar entornos de Azure ML mediante la extensión CLI de Azure para Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 2c88c8954e334b5c6ffc80a60b2bd85cd98bda7a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055538"
---
# <a name="manage-azure-machine-learning-environments-with-the-cli-v2-preview"></a>Administración de entornos de Azure Machine Learning con la CLI (v2) (versión preliminar)

Los entornos de Azure Machine Learning definen los entornos de ejecución de los trabajos o las implementaciones y encapsulan las dependencias del código. Azure ML usa la especificación del entorno para crear el contenedor de Docker en el que se ejecuta el código de entrenamiento o puntuación en el destino de proceso especificado. Puede definir un entorno a partir de una especificación de Conda, una imagen de Docker o un contexto de compilación de Docker.

En este artículo va a aprender a crear y administrar entornos de Azure ML mediante la CLI (v2).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos

- Para usar la CLI, debe tener una suscripción de Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).
- [Instalación y configuración de la extensión de la CLI de Azure para Machine Learning](how-to-configure-cli.md)

> [!TIP]
> Si desea un entorno de desarrollo completo, use Visual Studio Code y la [extensión de Azure Machine Learning](how-to-setup-vs-code.md) para [administrar los recursos de Azure Machine Learning](how-to-manage-resources-vscode.md) y [entrenar modelos de Machine Learning](tutorial-train-deploy-image-classification-model-vscode.md).

### <a name="clone-examples-repository"></a>Clonación del repositorio de ejemplos

Para ejecutar los ejemplos de entrenamiento, primero clone el repositorio de ejemplos y cambie al directorio `cli`:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

Tenga en cuenta que `--depth 1` solo clona la confirmación más reciente en el repositorio, lo cual reduce el tiempo para completar la operación.

## <a name="curated-environments"></a>Entornos mantenidos

Hay dos tipos de entornos en Azure ML: entornos mantenidos y personalizados. Los entornos mantenidos son entornos predefinidos que contienen marcos y herramientas populares de ML. Los entornos personalizados están definidos por el usuario y se pueden crear mediante `az ml environment create`.

Azure ML proporciona los entornos mantenidos, que están disponibles en el área de trabajo de manera predeterminada. Azure ML actualiza estos entornos de forma rutinaria con las versiones más recientes del marco y los mantiene con correcciones de errores y revisiones de seguridad. Están respaldados por imágenes de Docker almacenadas en caché, lo que reduce el costo de preparación del trabajo y el tiempo de implementación del modelo.

Puede usar estos entornos mantenidos de serie para el entrenamiento o la implementación si hace referencia a un entorno específico mediante la sintaxis `azureml:<curated-environment-name>:<version>`. También puede usarlos como referencia para sus propios entornos personalizados si modifica los documentos Dockerfile que respaldan estos entornos mantenidos.

Puede ver el conjunto de entornos mantenidos disponibles en la interfaz de usuario de Estudio de Azure ML o mediante la CLI (v2) por medio de `az ml environments list`.

## <a name="create-an-environment"></a>Creación de un entorno

Puede definir un entorno a partir de una especificación de Conda, una imagen de Docker o un contexto de compilación de Docker. Configure el entorno mediante un archivo de especificaciones de YAML y cree el entorno mediante el siguiente comando de la CLI:

```cli
az ml environment create --file my_environment.yml
```

Para obtener la documentación de referencia de YAML para entornos de Azure ML, vea [Esquema YAML del entorno de la CLI (v2)](reference-yaml-environment.md).

### <a name="create-an-environment-from-a-docker-image"></a>Creación de un entorno a partir de una imagen de Docker

Para definir un entorno a partir de una imagen de Docker, proporcione el URI de la imagen hospedada en un registro como Docker Hub o Azure Container Registry. 

El ejemplo siguiente es un archivo de especificaciones YAML para un entorno definido a partir de una imagen de Docker. Se especifica una imagen del repositorio oficial de PyTorch en Docker Hub mediante la propiedad `image` del archivo YAML.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image.yml":::

Para crear el entorno:

```cli
az ml environment create --file assets/environment/docker-image.yml
```

> [!TIP]
> Azure ML mantiene un conjunto de imágenes base basadas en Ubuntu Linux CPU y GPU con dependencias comunes del sistema. Por ejemplo, las imágenes de GPU contienen Miniconda, OpenMPI, CUDA, cuDNN y NCCL. Puede usar estas imágenes para sus entornos o usar sus documentos Dockerfile correspondientes como referencia a la hora de compilar sus propias imágenes personalizadas.
>  
> Para obtener el conjunto de imágenes base y sus documentos Dockerfile correspondientes, vea el [repositorio AzureML-Containers](https://github.com/Azure/AzureML-Containers).

### <a name="create-an-environment-from-a-docker-build-context"></a>Creación de un entorno a partir de un contexto de compilación de Docker

En lugar de definir un entorno a partir de una imagen precompilada, puede hacerlo a partir de un [contexto de compilación](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#understand-build-context) de Docker. Para ello, especifique el directorio que va a actuar como contexto de compilación. Este directorio debe contener un documento Dockerfile y cualquier otro archivo necesario para compilar la imagen.

El ejemplo siguiente es un archivo de especificaciones YAML para un entorno definido a partir de un contexto de compilación. La ruta de acceso local a la carpeta del contexto de compilación se especifica en el campo `build.local_path` y la ruta de acceso relativa al documento Dockerfile dentro de esa carpeta del contexto de compilación se especifica en el campo `build.dockerfile_path`. Si se omite `build.dockerfile_path` en el archivo YAML, Azure ML busca un documento Dockerfile de nombre `Dockerfile` en la raíz del contexto de compilación.

En este ejemplo el contexto de compilación contiene un documento Dockerfile de nombre `Dockerfile` y un archivo `requirements.txt` al que se hace referencia dentro del documento Dockerfile para instalar paquetes de Python.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-context.yml":::

Para crear el entorno:

```cli
az ml environment create --file assets/environment/docker-context.yml
```

Azure ML comienza a compilar la imagen a partir del contexto de compilación cuando se cree el entorno. Puede supervisar el estado de la compilación y ver los registros de compilación en la interfaz de usuario de Studio.

### <a name="create-an-environment-from-a-conda-specification"></a>Creación de un entorno a partir de una especificación de Conda

Puede definir un entorno mediante un archivo de configuración YAML de Conda estándar que incluya las dependencias del entorno de Conda. Vea [Creación manual de un entorno](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually) para obtener información sobre este formato estándar.

También debe especificar una imagen base de Docker para este entorno. Azure ML compila el entorno de Conda sobre la imagen de Docker proporcionada.

El ejemplo siguiente es un archivo de especificaciones YAML para un entorno definido a partir de una especificación de Conda. Aquí se especifica la ruta de acceso relativa al archivo de Conda desde el archivo YAML del entorno de Azure ML mediante la propiedad `conda_file`. También puede definir la especificación de Conda insertada mediante la propiedad `conda_file`, en lugar de definirla en un archivo independiente.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image-plus-conda.yml":::

Para crear el entorno:

```cli
az ml environment create --file assets/environment/docker-image-plus-conda.yml
```

Azure ML compila la imagen final de Docker a partir de esta especificación de entorno cuando el entorno se usa en un trabajo o una implementación. También puede desencadenar manualmente una compilación del entorno en la interfaz de usuario de Studio.

## <a name="manage-environments"></a>Administración de entornos

La CLI (v2) proporciona un conjunto de comandos en `az ml environment` para administrar el ciclo de vida de los recursos del entorno de Azure ML.

### <a name="list"></a>List

Enumera todos los entornos del área de trabajo:

```cli
az ml environment list
```

Enumera todas las versiones del entorno con un nombre determinado:

```cli
az ml environment list --name docker-image-example
```

### <a name="show"></a>Mostrar

Obtiene los detalles de un entorno específico:

```cli
az ml environment list --name docker-image-example --version 1
```

### <a name="update"></a>Actualizar

Actualiza propiedades mutables de un entorno específico:

```cli
az ml environment update --name docker-image-example --version 1 --set description="This is an updated description."
```

> [!IMPORTANT]
> En los entornos solo se pueden actualizar `description` y `tags`. Todas las demás propiedades son inmutables; si necesita cambiar alguna de esas propiedades, debe crear una nueva versión del entorno.

### <a name="delete"></a>Eliminar

Elimina un entorno específico:

```cli
az ml environment delete --name docker-image-example --version 1
```

## <a name="use-environments-for-training"></a>Uso de entornos para el entrenamiento

Para usar un entorno en un trabajo de entrenamiento, especifique el campo `environment` de la configuración de YAML del trabajo. Puede hacer referencia a un entorno de Azure ML registrado existente mediante `environment: azureml:<environment-name>:<environment-version>` o definir una especificación de entorno insertada. Si define un entorno insertado, no especifique los campos `name` y `version`, ya que estos entornos se tratan como entornos "anónimos" y no se realiza su seguimiento en el registro de recursos del entorno.

Cuando se envía un trabajo de entrenamiento, la compilación de un nuevo entorno puede tardar varios minutos. La duración depende del tamaño de las dependencias necesarias. Los entornos se almacenan en caché en el servicio. Por tanto, siempre y cuando la definición del entorno permanezca inalterada, incurrirá en el tiempo de configuración completo una sola vez.

Para obtener más información sobre cómo usar entornos en trabajos, vea [Entrenamiento de modelos con la CLI (v2)](how-to-train-cli.md).

## <a name="use-environments-for-model-deployments"></a>Uso de entornos para implementaciones de modelos

También puede usar entornos para las implementaciones de modelos para la puntuación en línea y por lotes. Para ello, especifique el campo `environment` en la configuración de YAML de implementación.

Para obtener más información sobre cómo usar entornos en implementaciones, vea [Implementación y puntuación de un modelo de Machine Learning con un punto de conexión en línea](how-to-deploy-managed-online-endpoints.md).

## <a name="next-steps"></a>Pasos siguientes

- [Entrenamiento de modelos (crear trabajos) con la CLI (v2)](how-to-train-cli.md)
- [Implementación y puntuación de un modelo de Machine Learning con un punto de conexión en línea](how-to-deploy-managed-online-endpoints.md)
- [Referencia de esquema de YAML de un entorno](reference-yaml-environment.md)
