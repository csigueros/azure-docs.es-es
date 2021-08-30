---
title: Implementación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)
titleSuffix: Azure Machine Learning
description: Aprenda a implementar el modelo de aprendizaje automático como un servicio web administrado automáticamente por Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 0a4117e17ddddbb1fcb083a927fc35abae8b2eb6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751960"
---
# <a name="deploy-and-score-a-machine-learning-model-by-using-a-managed-online-endpoint-preview"></a>Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)

Aprenda a usar un punto de conexión en línea administrado (versión preliminar) para implementar el modelo, de modo que no tenga que crear ni administrar la infraestructura subyacente. Comenzará por implementar un modelo en la máquina local para depurar los errores y, después, lo implementará y probará en Azure. 

También aprenderá a ver los registros y a supervisar el Acuerdo de Nivel de Servicio (SLA). Empezará con un modelo y terminará con un punto de conexión HTTPS/REST escalable que se puede usar para la puntuación en línea o en tiempo real. 

Para más información, consulte [¿Qué son los puntos de conexión de Azure Machine Learning (versión preliminar)?](concept-endpoints.md).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos

* Para usar Azure Machine Learning, es preciso tener una suscripción a Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* Instale y configure la CLI de Azure y la extensión `ml` a la CLI de Azure. Para más información, consulte [Instalación, configuración y uso de la CLI 2.0 (versión preliminar)](how-to-configure-cli.md). 

* Debe tener un grupo de recursos de Azure, en el que usted (o la entidad de servicio que use) tenga acceso de colaborador. Se crea un grupo de recursos en [Instalación, configuración y uso de la CLI 2.0 (versión preliminar)](how-to-configure-cli.md). 

* Debe tener un área de trabajo de Azure Machine Learning. Se crea un área de trabajo en [Instalación, configuración y uso de la CLI 2.0 (versión preliminar)](how-to-configure-cli.md).

* Si aún no ha establecido los valores predeterminados de la CLI de Azure, guarde la configuración predeterminada. Para evitar pasar los valores de la suscripción, el área de trabajo y el grupo de recursos varias veces, ejecute este código:

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* (Opcional) Para implementar localmente, debe [instalar el motor de Docker](https://docs.docker.com/engine/install/) en el equipo local. *Se recomienda* esta opción, para que sea más sencillo depurar los problemas.

## <a name="prepare-your-system"></a>Preparación del sistema

Para seguir este artículo, primero clone el repositorio de ejemplos (azureml-examples). A continuación, ejecute el código siguiente para ir al directorio de ejemplos:

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

Para establecer el nombre del punto de conexión, elija uno de los siguientes comandos, en función del sistema operativo (reemplace `YOUR_ENDPOINT_NAME` por un nombre único).

Para Unix, ejecute este comando:

```azurecli
export ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

Para Windows, ejecute este comando:

```azurecli
set ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

> [!NOTE]
> Los nombres del punto de conexión deben ser únicos dentro de una región de Azure. Por ejemplo, en la región westus2 de Azure, solo puede haber un punto de conexión con el nombre `my-endpoint`. 

## <a name="define-the-endpoint-configuration"></a>Definición de la configuración del punto de conexión

Las entradas necesarias para implementar un modelo en un punto de conexión en línea son las siguientes:

- Archivos de modelo (o el nombre y la versión de un modelo ya registrado en el área de trabajo). En el ejemplo, tenemos un modelo scikit-learn que realiza la regresión.
- Código necesario para puntuar el modelo. En este caso, tenemos un archivo *score-py*.
- Entorno en el que se ejecuta el modelo. Como verá, el entorno podría ser una imagen de Docker con dependencias de Conda o podría ser un Dockerfile.
- Configuración para especificar el tipo de instancia y la capacidad de escalado.

El fragmento de código siguiente muestra el archivo *endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml*, que captura todas las entradas necesarias: 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml":::

> [!NOTE]
> Para obtener una descripción completa de YAML, consulte la [referencia de YAML sobre puntos de conexión en línea administrados (versión preliminar)](reference-yaml-endpoint-managed-online.md).

La referencia para el formato YAML del punto de conexión se describe en la tabla siguiente. Para aprender a especificar estos atributos, consulte el ejemplo de YAML en [Preparación del sistema](#prepare-your-system) o la [referencia de YAML de puntos de conexión en línea](reference-yaml-endpoint-managed-online.md). Para más información sobre los límites relacionados con los puntos de conexión administrados, consulte [Administración y aumento de las cuotas de recursos con Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).

| Clave | Descripción |
| --- | --- |
| `$schema`    | (Opcional) El esquema de YAML. Para ver todas las opciones disponibles en el archivo YAML, puede ver el esquema del ejemplo anterior en un explorador.|
| `name`       | Nombre del punto de conexión. El valor debe ser único dentro de la región de Azure.|
| `traffic` | El porcentaje de tráfico desde el punto de conexión que se desviará a cada implementación. La suma de los valores de tráfico debe ser 100. |
| `auth_mode` | Use `key` para la autenticación basada en claves o `aml_token` para la autenticación basada en tokens de Azure Machine Learning. `key` no expira, pero `aml_token` sí lo hace. (Obtenga el token más reciente con el comando `az ml endpoint get-credentials`). |
| `deployments` | La lista de implementaciones que se crearán en el punto de conexión. En este caso, solo tenemos una implementación, denominada `blue`. Para más información sobre varias implementaciones, consulte [Implementación segura para puntos de conexión en línea (versión preliminar)](how-to-safely-rollout-managed-endpoints.md).|

En la tabla siguiente se describen los atributos de `deployments`:

| Clave | Descripción |
| --- | --- |
| `name`  | Nombre de la implementación. |
| `model` | En este ejemplo, se especifican las propiedades del modelo en línea: `name`, `version` y `local_path`. Los archivos de modelo se cargan y registran automáticamente. Una desventaja de la especificación en línea es que debe incrementar la versión manualmente si quiere actualizar los archivos del modelo. Para ver los procedimientos recomendados relacionados, consulte la sugerencia de la sección siguiente. |
| `code_configuration.code.local_path` | Directorio que contiene todo el código fuente de Python para puntuar el modelo. Puede usar directorios y paquetes anidados. |
| `code_configuration.scoring_script` | El archivo de Python que está en el directorio de puntuación anterior `code_configuration.code.local_path`. Este código de Python debe tener una función `init()` y una función `run()`. Se llamará a la función `init()` después de crear o actualizar el modelo (puede usarla para almacenar en caché el modelo en memoria, por ejemplo). Se llama a la función `run()` en cada invocación del punto de conexión para realizar la puntuación o predicción reales. |
| `environment` | Contiene los detalles del entorno para hospedar el modelo y el código. En este ejemplo, tenemos definiciones en línea que incluyen `name` `version` y `path`. Usaremos `environment.docker.image` para la imagen. Las dependencias `conda_file` se instalarán encima de la imagen. Para más información, consulte la sugerencia de la sección siguiente. |
| `instance_type` | La SKU de máquina virtual que hospedará las instancias de implementación. Para más información, consulte las [SKU de máquina virtual compatibles con puntos de conexión en línea administrados](reference-managed-online-endpoints-vm-sku-list.md). |
| `scale_settings.scale_type` | De momento, este valor debe ser `manual`. Para realizar el escalado o la reducción vertical después de crear el punto de conexión y la implementación, actualice `instance_count` en el archivo YAML y ejecute el comando `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>`.|
| `scale_settings.instance_count` | Número de instancias de la implementación. Base el valor en la carga de trabajo esperada. Para alta disponibilidad, se recomienda establecer `scale_settings.instance_count` en al menos `3`. |

Para más información sobre el esquema de YAML, consulte la [referencia de YAML del punto de conexión en línea](reference-yaml-endpoint-managed-online.md).

> [!NOTE]
> Para usar Azure Kubernetes Service (AKS) en lugar de puntos de conexión administrados como destino de proceso:
> 1. Cree el clúster de AKS y asócielo como destino de proceso al área de Azure Machine Learning mediante [Azure ML Studio](how-to-create-attach-compute-studio.md#whats-a-compute-target).
> 1. Use el archivo [YAML de punto de conexión](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) para utilizar AKS como destino en lugar del archivo YAML del punto de conexión administrado. Tendrá que editar el archivo YAML para cambiar el valor de `target` por el nombre del destino de proceso registrado.
>
> Todos los comandos que se usan en este artículo (excepto la supervisión opcional del Acuerdo de Nivel de Servicio y la integración de Azure Log Analytics) se pueden usar con puntos de conexión administrados o con puntos de conexión de AKS.

### <a name="register-your-model-and-environment-separately"></a>Registro del modelo y el entorno por separado

En este ejemplo, se especifican las propiedades del modelo y del entorno en línea: `name`, `version` y `local_path` (desde donde cargar los archivos). La CLI carga automáticamente los archivos y registra el modelo y el entorno. Como procedimiento recomendado en producción, debe registrar el modelo y el entorno y especificar el nombre y la versión registrados por separado en el archivo YAML. Utilice el formulario `model: azureml:my-model:1` o `environment: azureml:my-env:1`.

Para el registro, puede extraer las definiciones de YAML de `model` y `environment` en archivos YAML diferentes y usar los comandos `az ml model create` y `az ml environment create`. Para más información sobre estos comandos, ejecute `az ml model create -h` y `az ml environment create -h`.

### <a name="use-different-cpu-and-gpu-instance-types"></a>Uso de diferentes tipos de instancia de CPU y GPU

El archivo YAML anterior usa un tipo de uso general (`Standard_F2s_v2`) y una imagen de Docker que no es GPU (en el archivo YAML, consulte el atributo `image`). Para el proceso de GPU, elija una SKU de tipo de proceso de GPU y una imagen de Docker de GPU.

Para ver los tipos de instancia de GPU y de uso general admitidos, consulte [Las SKU de máquina virtual compatibles con los puntos de conexión en línea administrados](reference-managed-online-endpoints-vm-sku-list.md). Para obtener una lista de las imágenes base de GPU y CPU de Azure Machine Learning, consulte las [imágenes base de Azure Machine Learning](https://github.com/Azure/AzureML-Containers).

### <a name="use-more-than-one-model"></a>Uso de más de un modelo

Actualmente, solo puede especificar un modelo por implementación en el archivo YAML. Si tiene más de un modelo, al registrar el modelo, copie todos los modelos (como archivos o subdirectorios) en la carpeta que use para el registro. En el script de puntuación, use la variable de entorno `AZUREML_MODEL_DIR` para obtener la ruta de acceso a la carpeta raíz del modelo. Se conserva la estructura de directorios subyacente.

## <a name="understand-the-scoring-script"></a>Descripción del script de puntuación

> [!TIP]
> El formato del script de puntuación para los puntos de conexión en línea administrados es el mismo que se usaba en la versión anterior de la CLI y en el SDK de Python.

Como se ha indicado antes, `run()` debe tener una función `code_configuration.scoring_script` y una función `init()`. En este ejemplo se usa el [archivo score.py](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py). Se llama a la función `init()` cuando se inicializa o se inicia el contenedor. La inicialización suele producirse poco después de crear o actualizar la implementación. Escriba aquí la lógica para realizar operaciones de inicialización globales, como almacenar en caché el modelo en memoria (como se hace en este ejemplo). Se llama a la función `run()` en cada invocación del punto de conexión y debe realizar la puntuación y predicción reales. En el ejemplo, extraemos los datos de la entrada JSON, llamamos al método `predict()` del modelo scikit-learn y devolvemos el resultado.

## <a name="deploy-and-debug-locally-by-using-local-endpoints"></a>Implementación y depuración locales mediante puntos de conexión locales

Para ahorrar tiempo en la depuración, se *recomienda* que lleve a cabo una serie de pruebas del punto de conexión localmente.

> [!NOTE]
> * Para realizar la implementación localmente, debe tener instalado el [motor de Docker](https://docs.docker.com/engine/install/).
> * El motor de Docker debe estar en ejecución. Este motor normalmente se inicia cuando se inicia el equipo. Si no lo hace, puede [solucionar el problema aquí](https://docs.docker.com/config/daemon/#start-the-daemon-manually).

> [!IMPORTANT]
> El objetivo de la implementación de un punto de conexión local es validar y depurar el código y la configuración antes de implementarlo en Azure. La implementación local tiene las siguientes limitaciones:
> - Los puntos de conexión locales *no* admiten reglas de tráfico, autenticación, configuración de escalado o configuración de sondeo. 
> - Los puntos de conexión locales solo admiten una implementación por punto de conexión. Es decir, en una implementación local no puede usar una referencia a un modelo o a un entorno registrados en el área de trabajo de Azure Machine Learning. 

### <a name="deploy-the-model-locally"></a>Implementación del modelo localmente

Para implementar el modelo localmente:

```azurecli
az ml endpoint create --local -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

> [!NOTE]
> Si usa un sistema operativo Windows, utilice `%ENDPOINT_NAME%` en lugar de `$ENDPOINT_NAME` aquí y en los sucesivos comandos.

La marca `--local` indica a la CLI que implemente el punto de conexión en el entorno de Docker.

### <a name="verify-the-local-deployment-succeeded"></a>Comprobación de que la implementación local se ha realizado correctamente

Compruebe los registros para ver si el modelo se implementó sin errores:

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

### <a name="invoke-the-local-endpoint-to-score-data-by-using-your-model"></a>Invocación del punto de conexión local para puntuar los datos con el modelo

Invoque el punto de conexión para puntuar el modelo usando el práctico comando `invoke` y pasando los parámetros de consulta almacenados en un archivo JSON:

```azurecli
az ml endpoint invoke --local -n $ENDPOINT_NAME --request-file endpoints/online/model-1/sample-request.json
```

Si desea usar un cliente REST (como curl), debe tener el URI de puntuación. Para obtener el identificador URI de puntuación, ejecute `az ml endpoint show --local -n $ENDPOINT_NAME`. En los datos devueltos, busque el atributo `scoring_uri`. 

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>Revisión de los registros para obtener la salida de la operación de invocación

En el archivo *score.py* de ejemplo, el método `run()` registra alguna salida en la consola. Puede ver esta salida usando de nuevo el comando `get-logs`:

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>Implementación del punto de conexión en línea administrado en Azure

A continuación, implemente el punto de conexión en línea administrado en Azure.

### <a name="deploy-to-azure"></a>Implementar en Azure

Para implementar la configuración de YAML en la nube, ejecute el siguiente código:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="deploy" :::

Esta implementación puede tardar un máximo de 15 minutos, dependiendo de si es la primera vez que se crean el entorno o la imagen subyacentes. Las implementaciones posteriores que usan el mismo entorno terminarán de procesarse más rápidamente.

> [!TIP]
> * Si prefiere no bloquear la consola de la CLI, puede agregar la marca `--no-wait` al comando. Sin embargo, se detendrá la presentación interactiva del estado de implementación.
>
> * Use [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](how-to-troubleshoot-managed-online-endpoints.md) para depurar los errores.

### <a name="check-the-status-of-the-deployment"></a>Comprobación del estado de la implementación

El comando `show` contiene información de `provisioning_status` tanto para el punto de conexión como para la implementación:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

Puede enumerar todos los puntos de conexión del área de trabajo en un formato de tabla con el comando `list`:

```azurecli
az ml endpoint list --output table
```

### <a name="check-the-status-of-the-cloud-deployment"></a>Comprobación del estado de la implementación en la nube

Compruebe los registros para ver si el modelo se implementó sin errores:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

De forma predeterminada, los registros se extraen del servidor de inferencia. Para ver los registros del inicializador de almacenamiento (monta recursos como el modelo y el código en el contenedor), agregue la marca `--container storage-initializer`.

### <a name="invoke-the-endpoint-to-score-data-by-using-your-model"></a>Invocación del punto de conexión para puntuar los datos con el modelo

Puede usar el comando `invoke` o un cliente REST de su elección para invocar el punto de conexión y puntuar algunos datos: 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

Para ver los registros de invocación, vuelva a ejecutar `get-logs`.

Para usar un cliente REST, debe tener el valor de `scoring_uri` y la clave o token de autenticación. El valor de `scoring_uri` está disponible en la salida del comando `show`:
 
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_scoring_uri" :::

Se está usando la marca `--query` para filtrar los atributos solo a lo que se necesita. Puede encontrar más información sobre `--query` en [Consulta de la salida del comando de la CLI de Azure](/cli/azure/query-azure-cli).

Recupere las credenciales necesarias mediante el comando `get-credentials`:

```azurecli
az ml endpoint get-credentials -n $ENDPOINT_NAME
```

### <a name="optional-update-the-deployment"></a>(Opcional) Actualización de la implementación

Si quiere actualizar el código, el modelo, el entorno o la configuración de escalado, actualice el archivo YAML y ejecute el comando `az ml endpoint update`. 

> [!IMPORTANT]
> Solo puede modificar *un* aspecto (el tráfico, la configuración de escalado, el código, el modelo o el entorno) en un solo comando `update`. 

Para comprender cómo funciona `update`:

1. Abra el archivo *online/model-1/onlinescoring/score.py*.
1. Cambie la última línea de la función `init()`: después de `logging.info("Init complete")`, agregue `logging.info("Updated successfully")`. 
1. Guarde el archivo.
1. Ejecute este comando:

    ```azurecli
    az ml endpoint update -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
    ```

    > [!IMPORTANT]
    > La actualización mediante YAML es declarativa. Es decir, los cambios en el archivo YAML se reflejan en los recursos de Azure Resource Manager subyacentes (puntos de conexión e implementaciones). El enfoque declarativo facilita [GitOps](https://www.atlassian.com/git/tutorials/gitops): *Todos* los cambios en los puntos de conexión o implementaciones (incluso `instance_count`) pasan por YAML. Como resultado, si quita una implementación de YAML y ejecuta `az ml endpoint update` con el archivo, esa implementación se eliminará. Puede realizar actualizaciones sin usar YAML mediante la marca `--set`.
    
1. Dado que ha modificado la función `init()` (`init()`se ejecuta cuando se crea o actualiza el punto de conexión), el mensaje `Updated successfully` estará en los registros. Recupere los registros ejecutando:

    ```azurecli
    az ml endpoint get-logs -n $ENDPOINT_NAME --deployment blue
    ```

En el caso poco frecuente de que quiera eliminar y volver a crear la implementación debido a un problema irresoluble, ejecute:

```azurecli
az ml endpoint delete -n $ENDPOINT_NAME --deployment blue
```

El comando `update` también funciona con puntos de conexión locales. Use el mismo comando `az ml endpoint update` con la marca `--local`.

> [!TIP]
> Con el comando `az ml endpoint update`, puede usar el parámetro [`--set` de la CLI de Azure](/cli/azure/use-cli-effectively#generic-update-arguments) para invalidar los atributos del archivo YAML *o* para establecer atributos específicos sin pasar este archivo. El uso de `--set` para atributos únicos es especialmente valioso en escenarios de implementación y pruebas. Por ejemplo, para escalar verticalmente el valor de `instance_count` de la primera implementación, podría usar la marca `--set deployments[0].scale_settings.instance_count=2`. Sin embargo, como YAML no está actualizado, esta técnica no facilita [GitOps](https://www.atlassian.com/git/tutorials/gitops).

### <a name="optional-monitor-sla-by-using-azure-monitor"></a>(Opcional) Supervisión del Acuerdo de Nivel de Servicio mediante Azure Monitor

Para ver las métricas y establecer alertas en función del Acuerdo de Nivel de Servicio, complete los pasos que se describen en [Supervisión de puntos de conexión en línea administrados](how-to-monitor-online-endpoints.md).

### <a name="optional-integrate-with-log-analytics"></a>(Opcional) Integración con Log Analytics

El comando `get-logs` solo proporciona los últimos cientos de líneas de registros de una instancia seleccionada automáticamente. Sin embargo, Log Analytics proporciona una manera de almacenar y analizar los registros de forma duradera. 

En primer lugar, cree un área de trabajo de Log Analytics mediante los pasos que se indican en [Creación de un área de trabajo de Log Analytics en Azure Portal](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace).

Luego, en Azure Portal:

1. Vaya al grupo de recursos.
1. Seleccione el punto de conexión.
1. Seleccione la **página de recursos de ARM**.
1. Seleccione **Configuración de diagnóstico**.
1. Seleccione **Add settings** (Agregar configuración).
1. Seleccione esta opción para habilitar el envío de registros de la consola al área de trabajo de Log Analytics.

Los registros pueden tardar hasta una hora en conectarse. Después de este tiempo, envíe algunas solicitudes de puntuación y, después, compruebe los registros mediante los pasos siguientes:

1. Abra el área de trabajo de Log Analytics. 
1. En el menú de la izquierda, seleccione **Registros**.
1. Cierre el menú emergente **Consultas** que se abre automáticamente.
1. Haga doble clic en **AmlOnlineEndpointConsoleLog**.
1. Seleccione **Run** (Ejecutar).

## <a name="delete-the-endpoint-and-the-deployment"></a>Eliminación del punto de conexión y la implementación

Si no va a usar la implementación, debe eliminarla mediante la ejecución del código siguiente (se elimina el punto de conexión y todas las implementaciones subyacentes):

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte estos artículos:

- [Implementación de modelos con REST (versión preliminar)](how-to-deploy-with-rest.md)
- [Creación y uso de puntos de conexión en línea administrados (versión preliminar) en Estudio](how-to-use-managed-online-endpoint-studio.md)
- [Implementación segura para puntos de conexión en línea (versión preliminar)](how-to-safely-rollout-managed-endpoints.md)
- [Uso de puntos de conexión por lotes (versión preliminar) para la puntuación por lotes](how-to-use-batch-endpoint.md)
- [Visualización de los costos de un punto de conexión en línea administrado de Azure Machine Learning (versión preliminar)](how-to-view-online-endpoints-costs.md)
- [Tutorial: Acceso a recursos de Azure con un punto de conexión en línea administrado e identidad administrada por el sistema (versión preliminar)](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
- [Solución de problemas de implementación de puntos de conexión en línea administrados](how-to-troubleshoot-managed-online-endpoints.md)
