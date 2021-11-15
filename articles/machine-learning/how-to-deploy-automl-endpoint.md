---
title: Implementación de un modelo de AutoML con un punto de conexión en línea administrado (versión preliminar)
titleSuffix: Azure Machine Learning
description: Aprenda a implementar el modelo de AutoML como un servicio web administrado automáticamente por Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
ms.reviewer: laobri
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 5bc5208de912a6f11271681b754736bfafe17e8b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565974"
---
# <a name="how-to-deploy-an-automl-model-to-an-online-endpoint-preview"></a>Implementación de un modelo de AutoML en un punto de conexión en línea (versión preliminar)

En este artículo, aprenderá a implementar un modelo de Machine Learning entrenado por AutoML en un punto de conexión en línea. El aprendizaje automático automatizado, también denominado ML automatizado o AutoML, es el proceso de automatizar las tareas lentas e iterativas del desarrollo de modelos de Machine Learning. Para más información, consulte [¿Qué es el aprendizaje automático automatizado (AutoML)?](concept-automated-ml.md).

En este artículo, aprenderá a implementar un modelo de Machine Learning entrenado por AutoML en puntos de conexión en línea mediante: 

- Azure Machine Learning Studio
- CLI 2.0 de Azure Machine Learning

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prerrequisitos

Un modelo de Machine Learning entrenado por AutoML. Para más información, consulte [Tutorial: Entrenamiento de un modelo de clasificación con aprendizaje automático automatizado sin código en el Estudio de Azure Machine Learning](tutorial-first-experiment-automated-ml.md) o [Tutorial: Previsión de la demanda con aprendizaje automático automatizado](tutorial-automated-ml-forecast.md).

## <a name="deploy-from-azure-machine-learning-studio-and-no-code"></a>Implementación desde el Estudio de Azure Machine Learning y sin código

La implementación de un modelo entrenado por AutoML desde la página de ML automatizado es una experiencia sin código. Es decir, no es necesario preparar un script y un entorno de puntuación, ya que ambos se generan automáticamente. 

1. Vaya a la página de ML automatizado en Studio.
1. Seleccione el tipo de experimento y ejecútelo.
1. Elija la pestaña Modelos.
1. Seleccione el modelo que quiere implementar. 
1. Una vez que seleccione un modelo, el botón Implementar se encenderá con un menú desplegable.
1. Seleccione la opción *Implementación de un punto de conexión en tiempo real (versión preliminar)* .

   :::image type="content" source="media/how-to-deploy-automl-endpoint/deploy-button.png" lightbox="media/how-to-deploy-automl-endpoint/deploy-button.png" alt-text="Captura de pantalla que muestra el menú desplegable del botón Implementar":::

   El sistema generará el modelo y el entorno necesarios para la implementación. 

   :::image type="content" source="media/how-to-deploy-automl-endpoint/model.png" lightbox="media/how-to-deploy-automl-endpoint/model.png" alt-text="Captura de pantalla que muestra el modelo generado":::

   :::image type="content" source="media/how-to-deploy-automl-endpoint/environment.png" lightbox="media/how-to-deploy-automl-endpoint/environment.png" alt-text="Captura de pantalla que muestra el entorno generado":::

5. Complete el asistente para implementar el modelo en un punto de conexión en tiempo real.

 :::image type="content" source="media/how-to-deploy-automl-endpoint/complete-wizard.png" lightbox="media/how-to-deploy-automl-endpoint/complete-wizard.png"  alt-text="Captura de pantalla que muestra la página Revisar y crear":::


## <a name="deploy-manually-from-the-studio-or-command-line"></a>Implementación manual desde Studio o la línea de comandos

Si desea tener más control sobre la implementación, puede descargar los artefactos de entrenamiento e implementarlos. 

Para descargar los componentes que necesitará para la implementación:

1. Vaya al experimento de ML automatizado y ejecútelo en el área de trabajo de aprendizaje automático.
1. Elija la pestaña Modelos.
1. Seleccione el modelo que desea usar. Una vez que seleccione un modelo, se habilitará el botón *Descargar*.
1. Elija *Descargar*.

:::image type="content" source="media/how-to-deploy-automl-endpoint/download-model.png" lightbox="media/how-to-deploy-automl-endpoint/download-model.png" alt-text="Captura de pantalla que muestra la selección del modelo y el botón de descarga":::

Recibirá un archivo ZIP que contiene:
* Un archivo de especificación de entorno de Conda denominado `conda_env_<VERSION>.yml`
* Un archivo de puntuación de Python denominado `scoring_file_<VERSION>.py`
* El propio modelo, en un archivo `.pkl` de Python denominado `model.pkl`

Para realizar la implementación con estos archivos, puede usar Studio o la interfaz de la línea de comandos de Azure.

# <a name="studio"></a>[Estudio](#tab/Studio)

1. Vaya a la página Modelos en el Estudio de Azure Machine Learning.

1. Haga clic en la opción + Registrar modelo.

1. Registre el modelo que descargó de la ejecución de ML automatizado.

1. Vaya a la página Entornos, seleccione Entorno personalizado y seleccione la opción + Crear para crear un entorno para la implementación. Use el YAML de Conda descargado para crear un entorno personalizado.

1. Seleccione el modelo y, en la opción desplegable Implementar, seleccione Implementar en el punto de conexión en tiempo real.

1. Complete todos los pasos del asistente para crear un punto de conexión en línea y una implementación.

 
# <a name="cli"></a>[CLI](#tab/CLI)

## <a name="configure-the-cli"></a>Configuración de la CLI 

Para crear una implementación desde la CLI, necesitará la CLI de Azure con la extensión de ML v2. Ejecute el siguiente comando para confirmar que tiene ambos:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

Si recibe un mensaje de error o `Extensions: ml` no aparece en la respuesta, siga los pasos descritos en [Instalación y configuración de la CLI (v2)](how-to-configure-cli.md).

Inicio de sesión:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

Si tiene acceso a varias suscripciones de Azure, puede establecer la suscripción activa:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

Establezca el grupo de recursos y el área de trabajo predeterminados en donde desea crear la implementación:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

## <a name="put-the-scoring-file-in-its-own-directory"></a>Disposición del archivo de puntuación en su propio directorio

Cree un directorio llamado `src/` y coloque en él el archivo de puntuación que descargó. Este directorio se carga en Azure y contiene todo el código fuente necesario para realizar la inferencia. Para un modelo de AutoML, solo hay un archivo de puntuación. 

## <a name="create-the-endpoint-and-deployment-yaml-file"></a>Creación del punto de conexión y el archivo YAML de implementación

Para crear un punto de conexión en línea administrado desde la línea de comandos, deberá crear un archivo *endpoint.yml* y un archivo *deployment.yml*. El código siguiente, tomado del [repositorio de ejemplos de Azure Machine Learning](https://github.com/Azure/azureml-examples) muestra _endpoints/online/managed/sample/_ , que captura todas las entradas necesarias:

__automl_endpoint.yml__

::: code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/endpoint.yml" :::

__automl_deployment.yml__

::: code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml" :::

Deberá modificar este archivo para usar los archivos que descargó de la página Modelos de AutoML.

1. Cree un archivo `automl_endpoint.yml` y `automl_deployment.yml` y pegue el contenido del ejemplo anterior.

1. Cambie el valor del `name` del punto de conexión. El nombre del punto de conexión debe ser único dentro de la región de Azure. El nombre de un punto de conexión debe empezar con una letra mayúscula o minúscula y estar formado solo por caracteres "-" y alfanuméricos.

1. En el archivo `automl_deployment`, cambie el valor de las claves en las rutas de acceso siguientes:

    | Ruta de acceso | Cambiar a |
    | --- | --- |
    | `model:local_path` | La ruta de acceso al archivo `model.pkl` que descargó. |
    | `code_configuration:code:local_path` | El directorio en el que ha colocado el archivo de puntuación. | 
    | `code_configuration:scoring_script` | El nombre del archivo de puntuación de Python (`scoring_file_<VERSION>.py`). |
    | `environment:conda_file` | Una dirección URL de archivo para el archivo de entorno de Conda descargado (`conda_env_<VERSION>.yml`). |

    > [!NOTE]
    > Para obtener una descripción completa de YAML, consulte la [referencia de YAML sobre puntos de conexión en línea administrados (versión preliminar)](reference-yaml-endpoint-managed-online.md).

3. En la línea de comandos, ejecute: 

    ```azurecli
    az ml online-endpoint create -f automl_endpoint.yml
    az ml online-deployment create -f automl_deployment.yml
    ```
    
---

Después de crear una implementación, puede puntuar como se describe en [Invocación del punto de conexión para puntuar datos mediante el modelo](how-to-deploy-managed-online-endpoints.md#invoke-the-endpoint-to-score-data-by-using-your-model).

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de implementación de puntos de conexión en línea administrados](how-to-troubleshoot-managed-online-endpoints.md)
- [Implementación segura para puntos de conexión en línea](how-to-safely-rollout-managed-endpoints.md)
