---
title: Implementación de un modelo de ML con un punto de conexión en línea (versión preliminar)
titleSuffix: Azure Machine Learning
description: Aprenda a implementar el modelo de Machine Learning como un servicio web para Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2, ignite-fall-2021
ms.openlocfilehash: dba2e849fb28dfb0f6667b496c65bdef8cbdf046
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557554"
---
# <a name="deploy-and-score-a-machine-learning-model-by-using-an-online-endpoint-preview"></a>Implementación y puntuación de un modelo de Machine Learning con un punto de conexión en línea (versión preliminar)

Aprenda a usar un punto de conexión en línea (versión preliminar) para implementar el modelo, de modo que no tenga que crear ni administrar la infraestructura subyacente. Comenzará por implementar un modelo en la máquina local para depurar los errores y, después, lo implementará y probará en Azure.

También aprenderá a ver los registros y a supervisar el Acuerdo de Nivel de Servicio (SLA). Empezará con un modelo y terminará con un punto de conexión HTTPS/REST escalable que se puede usar para la puntuación en línea o en tiempo real. 

Para más información, consulte [¿Qué son los puntos de conexión de Azure Machine Learning (versión preliminar)?](concept-endpoints.md).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos

* Para usar Azure Machine Learning, es preciso tener una suscripción a Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* Instale y configure la CLI de Azure y la extensión `ml` a la CLI de Azure. Para más información, consulte [Instalación, configuración y uso de la CLI v2 (versión preliminar)](how-to-configure-cli.md). 

* Debe tener un grupo de recursos de Azure, en el que usted (o la entidad de servicio que use) tenga acceso de colaborador. Se crea un grupo de recursos en [Instalación, configuración y uso de la CLI v2 (versión preliminar)](how-to-configure-cli.md). 

* Debe tener un área de trabajo de Azure Machine Learning. Se crea un área de trabajo en [Instalación, configuración y uso de la CLI v2 (versión preliminar)](how-to-configure-cli.md).

* Si aún no ha establecido los valores predeterminados de la CLI de Azure, guarde la configuración predeterminada. Para evitar pasar los valores de la suscripción, el área de trabajo y el grupo de recursos varias veces, ejecute este código:

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* (Opcional) Para implementar localmente, debe [instalar el motor de Docker](https://docs.docker.com/engine/install/) en el equipo local. *Se recomienda* esta opción, para que sea más sencillo depurar los problemas.

> [!IMPORTANT]
> En los ejemplos de este documento se da por hecho que se está usando el shell de Bash. Por ejemplo, en un sistema Linux o [Subsistema de Windows para Linux](/windows/wsl/about). 

## <a name="prepare-your-system"></a>Preparación del sistema

Para seguir este artículo, primero clone el repositorio de ejemplos (azureml-examples). A continuación, ejecute el código siguiente para ir al directorio de ejemplos:

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

Para establecer el nombre del punto de conexión, elija uno de los siguientes comandos, en función del sistema operativo (reemplace `YOUR_ENDPOINT_NAME` por un nombre único).

Para Unix, ejecute este comando:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="set_endpoint_name":::

> [!NOTE]
> Hace poco que se ha modificado la interfaz de la CLI: anteriormente `endpoint` y `deployment` estaban en `az ml endpoint`, mientras que ahora se han separado en `az ml online-endpoint` y `az ml online-deployment`.  Esto facilita el empleo de puntos de conexión en scripts de CI/CD.

> [!NOTE]
> Los nombres del punto de conexión deben ser únicos dentro de una región de Azure. Por ejemplo, en la región `westus2` de Azure solo puede haber un punto de conexión con el nombre `my-endpoint`. 

## <a name="review-the-endpoint-and-deployment-configurations"></a>Revisión de las configuraciones de punto de conexión e implementación

El siguiente fragmento de código muestra el archivo *endpoints/online/managed/sample/endpoint.yml*: 

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/endpoint.yml":::

> [!NOTE]
> Para obtener una descripción completa de YAML, consulte la [referencia de YAML sobre puntos de conexión en línea administrados (versión preliminar)](reference-yaml-endpoint-managed-online.md).

La referencia para el formato YAML del punto de conexión se describe en la tabla siguiente. Para aprender a especificar estos atributos, consulte el ejemplo de YAML en [Preparación del sistema](#prepare-your-system) o la [referencia de YAML de puntos de conexión en línea](reference-yaml-endpoint-managed-online.md). Para más información sobre los límites relacionados con los puntos de conexión administrados, consulte [Administración y aumento de las cuotas de recursos con Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).

| Clave | Descripción |
| --- | --- |
| `$schema`    | (Opcional) El esquema de YAML. Para ver todas las opciones disponibles en el archivo YAML, puede ver el esquema del ejemplo anterior en un explorador.|
| `name`       | Nombre del punto de conexión. El valor debe ser único dentro de la región de Azure.|
| `traffic` | El porcentaje de tráfico desde el punto de conexión que se desviará a cada implementación. La suma de los valores de tráfico debe ser 100. |
| `auth_mode` | Use `key` para la autenticación basada en claves. Use `aml_token` para la autenticación basada en tokens de Azure Machine Learning. `key` no expira, pero `aml_token` sí lo hace. (Obtenga el token más reciente con el comando `az ml online-endpoint get-credentials`). |

El ejemplo contiene todos los archivos necesarios para implementar un modelo en un punto de conexión en línea. Para implementar un modelo debe tener:

- Archivos de modelo (o el nombre y la versión de un modelo ya registrado en el área de trabajo). En el ejemplo, tenemos un modelo scikit-learn que realiza la regresión.
- Código necesario para puntuar el modelo. En este caso, tenemos un archivo *score-py*.
- Entorno en el que se ejecuta el modelo. Como verá, el entorno podría ser una imagen de Docker con dependencias de Conda o podría ser un Dockerfile.
- Configuración para especificar el tipo de instancia y la capacidad de escalado.

El fragmento de código siguiente muestra el archivo *endpoints/online/managed/sample/blue-deployment.yml* con todas las entradas necesarias: 

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml":::

En la tabla se describen los atributos de `deployment`:

| Clave | Descripción |
| --- | --- |
| `name`  | Nombre de la implementación. |
| `model` | En este ejemplo se especifican las propiedades del modelo insertado: `local_path`. Los archivos de modelo se cargan y se registran de manera automática con un nombre generado automáticamente. Para ver los procedimientos recomendados relacionados, consulte la sugerencia de la sección siguiente. |
| `code_configuration.code.local_path` | Directorio que contiene todo el código fuente de Python para puntuar el modelo. Puede usar directorios y paquetes anidados. |
| `code_configuration.scoring_script` | El archivo de Python que está en el directorio de puntuación anterior `code_configuration.code.local_path`. Este código de Python debe tener una función `init()` y una función `run()`. Se llamará a la función `init()` después de crear o actualizar el modelo (puede usarla para almacenar en caché el modelo en memoria, por ejemplo). Se llama a la función `run()` en cada invocación del punto de conexión para realizar la puntuación o predicción reales. |
| `environment` | Contiene los detalles del entorno para hospedar el modelo y el código. En este ejemplo hay definiciones insertadas que incluyen `path`. Usaremos `environment.docker.image` para la imagen. Las dependencias `conda_file` se instalarán encima de la imagen. Para obtener más información, vea la sugerencia de la sección siguiente. |
| `instance_type` | La SKU de máquina virtual que hospedará las instancias de implementación. Para más información, consulte las [SKU de máquina virtual compatibles con puntos de conexión en línea administrados](reference-managed-online-endpoints-vm-sku-list.md). |
| `instance_count` | Número de instancias de la implementación. Base el valor en la carga de trabajo esperada. Para alta disponibilidad, se recomienda establecer `scale_settings.instance_count` en al menos `3`. |

Para más información sobre el esquema de YAML, consulte la [referencia de YAML del punto de conexión en línea](reference-yaml-endpoint-managed-online.md).

> [!NOTE]
> Para usar Kubernetes en lugar de puntos de conexión administrados como destino de proceso:
> 1. Cree el clúster de Kubernetes y asócielo como destino de proceso al área de Azure Machine Learning mediante [Estudio de Azure Machine Learning](how-to-attach-arc-kubernetes.md?&tabs=studio#attach-arc-cluster).
> 1. Utilice el archivo [YAML de punto de conexión](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) para usar Kubernetes como destino en lugar del archivo YAML del punto de conexión administrado. Tendrá que editar el archivo YAML para cambiar el valor de `target` por el nombre del destino de proceso registrado.
>
> Todos los comandos que se usan en este artículo (excepto la supervisión opcional del Acuerdo de Nivel de Servicio y la integración de Azure Log Analytics) se pueden usar con puntos de conexión administrados o con puntos de conexión de Kubernetes.

### <a name="register-your-model-and-environment-separately"></a>Registro del modelo y el entorno por separado

En este ejemplo se especifica `local_path` (desde donde cargar archivos) insertado. La CLI carga automáticamente los archivos y registra el modelo y el entorno. Como procedimiento recomendado en producción, debe registrar el modelo y el entorno y especificar el nombre y la versión registrados por separado en el archivo YAML. Utilice el formulario `model: azureml:my-model:1` o `environment: azureml:my-env:1`.

Para el registro, puede extraer las definiciones de YAML de `model` y `environment` en archivos YAML diferentes y usar los comandos `az ml model create` y `az ml environment create`. Para más información sobre estos comandos, ejecute `az ml model create -h` y `az ml environment create -h`.

### <a name="use-different-cpu-and-gpu-instance-types"></a>Uso de diferentes tipos de instancia de CPU y GPU

El archivo YAML anterior usa un tipo de uso general (`Standard_F2s_v2`) y una imagen de Docker que no es GPU (en el archivo YAML, consulte el atributo `image`). Para el proceso de GPU, elija una SKU de tipo de proceso de GPU y una imagen de Docker de GPU.

Para ver los tipos de instancia de GPU y de uso general admitidos, consulte [Las SKU de máquina virtual compatibles con los puntos de conexión en línea administrados](reference-managed-online-endpoints-vm-sku-list.md). Para obtener una lista de las imágenes base de GPU y CPU de Azure Machine Learning, consulte las [imágenes base de Azure Machine Learning](https://github.com/Azure/AzureML-Containers).

### <a name="use-more-than-one-model"></a>Uso de más de un modelo

Actualmente, solo puede especificar un modelo por implementación en el archivo YAML. Si tiene más de un modelo, al registrar el modelo, copie todos los modelos (como archivos o subdirectorios) en la carpeta que use para el registro. En el script de puntuación, use la variable de entorno `AZUREML_MODEL_DIR` para obtener la ruta de acceso a la carpeta raíz del modelo. Se conserva la estructura de directorios subyacente.

## <a name="understand-the-scoring-script"></a>Descripción del script de puntuación

> [!TIP]
> El formato del script de puntuación para los puntos de conexión en línea es el mismo que se usaba en la versión anterior de la CLI y en el SDK de Python.

Como se ha indicado antes, `run()` debe tener una función `code_configuration.scoring_script` y una función `init()`. En este ejemplo se usa el [archivo score.py](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py). Se llama a la función `init()` cuando se inicializa o se inicia el contenedor. La inicialización suele producirse poco después de crear o actualizar la implementación. Escriba aquí la lógica para realizar operaciones de inicialización globales, como almacenar en caché el modelo en memoria (como se hace en este ejemplo). Se llama a la función `run()` en cada invocación del punto de conexión y debe realizar la puntuación y predicción reales. En el ejemplo, extraemos los datos de la entrada JSON, llamamos al método `predict()` del modelo scikit-learn y devolvemos el resultado.

## <a name="deploy-and-debug-locally-by-using-local-endpoints"></a>Implementación y depuración locales mediante puntos de conexión locales

Para ahorrar tiempo en la depuración, se *recomienda* que lleve a cabo una serie de pruebas del punto de conexión localmente. Para obtener más información, vea [Depuración local de puntos de conexión en línea administrados con Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

> [!NOTE]
> * Para realizar la implementación localmente, debe tener instalado el [motor de Docker](https://docs.docker.com/engine/install/).
> * El motor de Docker debe estar en ejecución. Este motor normalmente se inicia cuando se inicia el equipo. Si no lo hace, puede [solucionar el problema aquí](https://docs.docker.com/config/daemon/#start-the-daemon-manually).

> [!IMPORTANT]
> El objetivo de la implementación de un punto de conexión local es validar y depurar el código y la configuración antes de implementarlo en Azure. La implementación local tiene las siguientes limitaciones:
> - Los puntos de conexión locales *no* admiten reglas de tráfico, autenticación, configuración de escalado o configuración de sondeo. 
> - Los puntos de conexión locales solo admiten una implementación por punto de conexión. 

### <a name="deploy-the-model-locally"></a>Implementación del modelo localmente

En primer lugar, cree el punto de conexión. Opcionalmente, en el caso de un punto de conexión local, puede omitir este paso y crear directamente la implementación (paso siguiente), lo que, a su vez, crea los metadatos necesarios. Esto es útil para fines de desarrollo y prueba.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="create_endpoint":::

Ahora cree una implementación de nombre `blue` en el punto de conexión.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="create_deployment":::

La marca `--local` indica a la CLI que implemente el punto de conexión en el entorno de Docker.

> [!TIP]
> Use Visual Studio Code para probar y depurar los puntos de conexión en local. Para obtener más información, vea [Depuración local de puntos de conexión en línea administrados con Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

### <a name="verify-the-local-deployment-succeeded"></a>Comprobación de que la implementación local se ha realizado correctamente

Compruebe el estado para ver si el modelo se ha implementado sin errores:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="get_status":::

La salida debe tener un aspecto similar al del siguiente JSON. Tenga en cuenta que `provisioning_state` es `Succeeded`.

```json
{
  "auth_mode": "key",
  "location": "local",
  "name": "docs-endpoint",
  "properties": {},
  "provisioning_state": "Succeeded",
  "scoring_uri": "http://localhost:49158/score",
  "tags": {},
  "traffic": {}
}
```

### <a name="invoke-the-local-endpoint-to-score-data-by-using-your-model"></a>Invocación del punto de conexión local para puntuar los datos con el modelo

Invoque el punto de conexión para puntuar el modelo usando el práctico comando `invoke` y pasando los parámetros de consulta almacenados en un archivo JSON:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="test_endpoint":::

Si desea usar un cliente REST (como curl), debe tener el URI de puntuación. Para obtener el identificador URI de puntuación, ejecute `az ml online-endpoint show --local -n $ENDPOINT_NAME`. En los datos devueltos, busque el atributo `scoring_uri`. Los comandos de ejemplo basados en curl están disponibles más adelante en este documento.

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>Revisión de los registros para obtener la salida de la operación de invocación

En el archivo *score.py* de ejemplo, el método `run()` registra alguna salida en la consola. Puede ver esta salida usando de nuevo el comando `get-logs`:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-local-endpoint.sh" ID="get_logs":::

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>Implementación del punto de conexión en línea administrado en Azure

A continuación, implemente el punto de conexión en línea administrado en Azure.

### <a name="deploy-to-azure"></a>Implementar en Azure

Para crear el punto de conexión en la nube, ejecute el código siguiente:

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="create_endpoint" :::

Para crear la implementación de nombre `blue` en el punto de conexión, ejecute el código siguiente:

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="create_deployment" :::

Esta implementación puede tardar un máximo de 15 minutos, dependiendo de si es la primera vez que se crean el entorno o la imagen subyacentes. Las implementaciones posteriores que usan el mismo entorno terminarán de procesarse más rápidamente.

> [!Important]
> La marca --all-traffic del elemento `az ml online-deployment create` anterior asigna el 100 % del tráfico al punto de conexión para la implementación recién creada. Aunque esto es útil para fines de desarrollo y prueba, en producción es posible que quiera abrir el tráfico a la nueva implementación mediante un comando explícito. Por ejemplo: `az ml online-endpoint update -n $ENDPOINT_NAME --traffic "blue=100"` 

> [!TIP]
> * Si prefiere no bloquear la consola de la CLI, puede agregar la marca `--no-wait` al comando. Sin embargo, se detendrá la presentación interactiva del estado de implementación.
>
> * Use [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](./how-to-troubleshoot-online-endpoints.md) para depurar los errores.

### <a name="check-the-status-of-the-deployment"></a>Comprobación del estado de la implementación

El comando `show` contiene información de `provisioning_status` tanto para el punto de conexión como para la implementación:

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

Puede enumerar todos los puntos de conexión del área de trabajo en un formato de tabla con el comando `list`:

```azurecli
az ml online-endpoint list --output table
```

### <a name="check-the-status-of-the-cloud-deployment"></a>Comprobación del estado de la implementación en la nube

Compruebe los registros para ver si el modelo se implementó sin errores:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

De forma predeterminada, los registros se extraen del servidor de inferencia. Para ver los registros del inicializador de almacenamiento (monta recursos como el modelo y el código en el contenedor), agregue la marca `--container storage-initializer`.

### <a name="invoke-the-endpoint-to-score-data-by-using-your-model"></a>Invocación del punto de conexión para puntuar los datos con el modelo

Puede usar el comando `invoke` o un cliente REST de su elección para invocar el punto de conexión y puntuar algunos datos: 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

En el ejemplo siguiente se muestra cómo obtener la clave usada para autenticarse en el punto de conexión:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint_using_curl_get_key":::

Luego use curl para puntuar los datos.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint_using_curl" :::

Observe que se usan los comandos `show` y `get-credentials` para obtener las credenciales de autenticación. Observe también que se está usando la marca `--query` para filtrar los atributos a fin de obtener solo los necesarios. Puede encontrar más información sobre `--query` en [Consulta de la salida del comando de la CLI de Azure](/cli/azure/query-azure-cli).

Para ver los registros de invocación, vuelva a ejecutar `get-logs`.

### <a name="optional-update-the-deployment"></a>(Opcional) Actualización de la implementación

Si quiere actualizar el código, el modelo, el entorno o la configuración de escalado, actualice el archivo YAML y ejecute el comando `az ml online-endpoint update`. 

> [!Note]
> Si actualiza el recuento de instancias junto con otras configuraciones del modelo (código, modelo o entorno) en un solo comando `update`: primero se realiza la operación de escalado y luego se aplican las demás actualizaciones. En el entorno de producción se recomienda realizar estas operaciones por separado.

Para comprender cómo funciona `update`:

1. Abra el archivo *online/model-1/onlinescoring/score.py*.
1. Cambie la última línea de la función `init()`: después de `logging.info("Init complete")`, agregue `logging.info("Updated successfully")`. 
1. Guarde el archivo.
1. Ejecute este comando:

    ```azurecli
    az ml online-deployment update -n blue --endpoint $ENDPOINT_NAME -f endpoints/online/managed/sample/blue-deployment.yml
    ```

    > [!Note]
    > La actualización mediante YAML es declarativa. Es decir, los cambios en el archivo YAML se reflejan en los recursos de Azure Resource Manager subyacentes (puntos de conexión e implementaciones). El enfoque declarativo facilita [GitOps](https://www.atlassian.com/git/tutorials/gitops): *Todos* los cambios en los puntos de conexión o implementaciones (incluso `instance_count`) pasan por YAML. Puede realizar actualizaciones sin usar YAML mediante la marca `--set`.
    
1. Dado que ha modificado la función `init()` (`init()`se ejecuta cuando se crea o actualiza el punto de conexión), el mensaje `Updated successfully` estará en los registros. Recupere los registros ejecutando:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

El comando `update` también funciona con implementaciones locales. Use el mismo comando `az ml online-deployment update` con la marca `--local`.

> [!TIP]
> Con el comando `update`, puede usar el parámetro [`--set` de la CLI de Azure](/cli/azure/use-cli-effectively#generic-update-arguments) para invalidar los atributos del archivo YAML *o* para establecer atributos específicos sin pasar este archivo. El uso de `--set` para atributos únicos es especialmente valioso en escenarios de implementación y pruebas. Por ejemplo, para escalar verticalmente el valor de `instance_count` de la primera implementación, podría usar la marca `--set instance_count=2`. Sin embargo, como YAML no está actualizado, esta técnica no facilita [GitOps](https://www.atlassian.com/git/tutorials/gitops).
> [!Note]
> Lo anterior es un ejemplo de actualización gradual local: es decir, la misma implementación se actualiza con la nueva configuración, con un 20 % de los nodos cada vez. Si la implementación tiene diez nodos, se actualizan dos nodos cada vez. En el caso del uso en producción, es posible que quiera considerar una [implementación azul-verde](how-to-safely-rollout-managed-endpoints.md), que ofrece una alternativa más segura.
### <a name="optional-configure-autoscaling"></a>(Opcional) Configuración de la escalabilidad automática

La escalabilidad automática ejecuta automáticamente la cantidad adecuada de recursos para controlar la carga en la aplicación. Los puntos de conexión administrados en línea admiten la escalabilidad automática mediante la integración con la característica de escalabilidad automática de Azure Monitor. Para configurar la escalabilidad automática, vea [Escalabilidad automática de puntos de conexión en línea](how-to-autoscale-endpoints.md).

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

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte estos artículos:

- [Implementación de modelos con REST (versión preliminar)](how-to-deploy-with-rest.md)
- [Creación y uso de puntos de conexión en línea administrados (versión preliminar) en Estudio](how-to-use-managed-online-endpoint-studio.md)
- [Implementación segura para puntos de conexión en línea (versión preliminar)](how-to-safely-rollout-managed-endpoints.md)
- [Escalabilidad automática de puntos de conexión administrados en línea](how-to-autoscale-endpoints.md)
- [Uso de puntos de conexión por lotes (versión preliminar) para la puntuación por lotes](how-to-use-batch-endpoint.md)
- [Visualización de los costos de un punto de conexión en línea administrado de Azure Machine Learning (versión preliminar)](how-to-view-online-endpoints-costs.md)
- [Acceso a recursos de Azure con un punto de conexión en línea administrado e identidad administrada (versión preliminar)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Solución de problemas de implementación de puntos de conexión en línea administrados](how-to-troubleshoot-online-endpoints.md)
