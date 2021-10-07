---
title: Limpieza y transformación de datos con grupos de Apache Spark (versión preliminar)
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo conectar e iniciar grupos de Apache Spark para la limpieza y transformación de datos con Azure Synapse Analytics y Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: 927b672aa4bd17efcfa491b0ec0092db9a2118c0
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429801"
---
# <a name="data-wrangling-with-apache-spark-pools-preview"></a>Limpieza y transformación de datos con grupos de Apache Spark (versión preliminar) 

En este artículo, aprenderá a realizar tareas de limpieza y transformación de datos de forma interactiva dentro de una sesión de Synapse dedicada, con tecnología de [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md), en un cuaderno de Jupyter mediante el [SDK de Python de Azure Machine Learning](/python/api/overview/azure/ml/). 

Si prefiere usar canalizaciones de Azure Machine Learning, consulte [Uso de Apache Spark (con tecnología de Azure Synapse Analytics) en la canalización de aprendizaje automático (versión preliminar)](how-to-use-synapsesparkstep.md).

Para instrucciones sobre cómo usar Azure Synapse Analytics con un área de trabajo de Synapse, consulte la documentación de [introducción Azure Synapse Analytics](../synapse-analytics/get-started.md).

>[!IMPORTANT]
> La integración de Azure Machine Learning y Azure Synapse Analytics se encuentra en versión preliminar. Las funciones que se presentan en este artículo emplean el paquete `azureml-synapse`, que contiene características en versión preliminar [experimentales](/python/api/overview/azure/ml/#stable-vs-experimental) que pueden cambiar en cualquier momento.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration"></a>Integración de Azure Machine Learning y Azure Synapse Analytics

La integración de Azure Synapse Analytics en Azure Machine Learning (versión preliminar) le permite conectar un grupo de Apache Spark respaldado por Azure Synapse para la exploración y preparación interactivas de datos. Con esta integración, puede contar con una instancia de proceso dedicada para la limpieza y transformación de datos a gran escala, todo dentro del mismo cuaderno de Python que usa para entrenar los modelos de Machine Learning.

## <a name="prerequisites"></a>Requisitos previos

* El [SDK de Azure Machine Learning para Python instalado](/python/api/overview/azure/ml/install). 

* [Cree un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Cree un área de trabajo de Azure Synapse Analytics en Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Cree un grupo de Apache Spark mediante Azure Portal, herramientas web o Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

* [Configure un entorno de desarrollo](how-to-configure-environment.md) para instalar el SDK de Azure Machine Learning o use una [instancia de proceso de Azure Machine Learning](concept-compute-instance.md#create) con el SDK ya instalado. 

* Instale el paquete `azureml-synapse` (versión preliminar) con el siguiente código:

  ```python
  pip install azureml-synapse
  ```

* Vincule el área de trabajo de Azure Machine Learning y el de Azure Synapse Analytics mediante el [SDK de Python de Azure Machine Learning](how-to-link-synapse-ml-workspaces.md#link-sdk) o a través de [Estudio de Azure Machine Learning](how-to-link-synapse-ml-workspaces.md#link-studio).

* [Asocie un grupo de Spark de Synapse](how-to-link-synapse-ml-workspaces.md#attach-synapse-spark-pool-as-a-compute) como destino de proceso.

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>Inicio del grupo de Spark de Synapse para tareas de limpieza y transformación de datos

Para comenzar la preparación de datos con el grupo de Apache Spark, especifique el nombre del proceso de Apache Spark asociado. Este nombre se puede encontrar a través de Estudio de Azure Machine Learning en la pestaña **Attached computes** (Procesos asociados). 

![obtener el nombre de proceso asociado](media/how-to-data-prep-synapse-spark-pool/attached-compute.png)

> [!IMPORTANT]
> Para seguir usando el grupo de Apache Spark, debe indicar qué recurso de proceso se va a utilizar en las tareas de limpieza y transformación de datos, con `%synapse` para líneas de código únicas y `%%synapse` para varias líneas. [Más información sobre el comando magic %synapse](/python/api/azureml-synapse/azureml.synapse.magics.remotesynapsemagics(class)).

```python
%synapse start -c SynapseSparkPoolAlias
```

Una vez iniciada la sesión, puede comprobar sus metadatos.

```python
%synapse meta
```

Puede especificar un [entorno de Azure Machine Learning](concept-environments.md) para usarlo durante la sesión de Apache Spark. Solo se aplicarán las dependencias de Conda especificadas en el entorno. No se admite la imagen de Docker.

>[!WARNING]
>  Los grupos de Apache Spark no admiten las dependencias de Python especificadas en las dependencias de Conda del entorno. Actualmente, solo se admiten las versiones fijas de Python. Compruebe la versión de Python incluyendo `sys.version_info` en el script.

En el código siguiente se crea el entorno, `myenv`, que instala `azureml-core` versión 1.20.0 y `numpy` versión 1.17.0 antes de que se inicie la sesión. Después puede incluir este entorno en la instrucción `start` de la sesión de Apache Spark.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Para comenzar la preparación de datos con el grupo de Apache Spark y su entorno personalizado, especifique el nombre del grupo de Apache Spark y qué entorno utilizar durante la sesión de Apache Spark. Además, puede proporcionar el identificador de la suscripción, el grupo de recursos del área de trabajo de aprendizaje automático y el nombre del área de trabajo de aprendizaje automático.

>[!IMPORTANT]
> Asegúrese de que la opción [Permitir paquetes de nivel de sesión](../synapse-analytics/spark/apache-spark-manage-python-packages.md#session-scoped-packages-preview) esté habilitada en el área de trabajo de Synapse vinculada.
>
>![Habilitación de paquetes de nivel de sesión](media/how-to-data-prep-synapse-spark-pool/enable-session-level-package.png)

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```

## <a name="load-data-from-storage"></a>Carga de datos desde el almacenamiento

Una vez que se inicia la sesión de Apache Spark, lea los datos que quiera preparar. La carga de datos es compatible con Azure Blob Storage y Azure Data Lake Storage Generation 1 y Generation 2.

Los datos de estos servicios de almacenamiento se pueden cargar de dos maneras: 

* Carga directa de los datos desde el almacenamiento mediante su ruta de acceso del sistema de archivos distribuido de Hadoop (HDFS).

* Lectura de los datos de un [conjunto de datos de Azure Machine Learning](how-to-create-register-datasets.md) existente.

Para acceder a estos servicios de almacenamiento, necesita permisos de **lector de datos de Storage Blob**. Si tiene previsto escribir datos de nuevo en estos servicios de almacenamiento, necesitará permisos de **colaborador de datos de Storage Blob**. [Obtenga más información sobre los permisos y roles de almacenamiento](../storage/blobs/assign-azure-role-data-access.md).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Carga de datos con la ruta de acceso del sistema de archivos distribuido de Hadoop (HDFS)

Para cargar y leer datos de almacenamiento con la ruta de acceso de HDFS correspondiente, debe tener preparadas las credenciales de autenticación de acceso a datos. Estas credenciales varían según el tipo de almacenamiento.  

En el código siguiente se muestra cómo se leen datos de **Azure Blob Storage** en una trama de datos de Spark con la clave de acceso o el token de firma de acceso compartido (SAS). 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

En el código siguiente se muestra cómo se leen datos de **Azure Data Lake Storage Generation 1 (ADLS Gen1)** con las credenciales de la entidad de servicio. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

En el código siguiente se muestra cómo se leen datos de **Azure Data Lake Storage Generation 2 (ADLS Gen2)** con las credenciales de la entidad de servicio. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Lectura de datos de conjuntos de datos registrados

También puede obtener un conjunto de datos registrado existente en el área de trabajo y realizar la preparación de datos convirtiéndolo en una trama de datos de Spark.

En el ejemplo siguiente autentifica el área de trabajo, se obtiene un objeto TabularDataset registrado, `blob_dset`, que hace referencia a los archivos del almacenamiento de blobs y lo convierte en una trama de datos de Spark. Al convertir los conjuntos de datos en una trama de datos de Spark, puede aprovechar las bibliotecas de preparación y exploración de datos `pyspark`.  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Realización de tareas de limpieza y transformación de datos

Después de recuperar y explorar los datos, puede llevar a cabo las tareas de limpieza y transformación de datos.

En el código siguiente, se amplía el ejemplo de HDFS de la sección anterior y se filtran los datos de la trama de datos de Spark, `df`, en función de la columna **Survivor** y se agrupa la lista según el valor de **Age**.

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Almacenamiento de datos y detención de la sesión de Spark

Una vez completada la exploración y la preparación de los datos, almacene los datos preparados para poder usarlos más adelante en su cuenta de almacenamiento de Azure.

En el ejemplo siguiente, los datos preparados se escriben en Azure Blob Storage y sobrescriben el archivo `Titanic.csv` original del directorio `training_data`. Para volver a escribir en el almacenamiento, necesita permisos de **colaborador de datos de Blob Storage**. [Obtenga más información sobre los permisos y roles de almacenamiento](../storage/blobs/assign-azure-role-data-access.md).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Cuando haya terminado de preparar los datos y haya guardado los datos preparados en el almacenamiento, detenga el uso del grupo de Apache Spark con el comando siguiente.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Creación de conjuntos de datos para representar los datos preparados

Cuando esté listo para usar los datos preparados para el entrenamiento de modelos, conéctese al almacenamiento con un [almacén de datos de Azure Machine Learning](how-to-access-data.md) y especifique los archivos que desea usar con un [conjunto de datos de Azure Machine Learning](how-to-create-register-datasets.md).

Consulte el ejemplo de código siguiente:

* En él se supone que ya ha creado un almacén de datos que se conecta al servicio de almacenamiento en el que guardó los datos preparados.  
* Obtiene el almacén de datos existente, `mydatastore`, del área de trabajo, `ws`, con el método get().
* Crea un objeto [FileDataset](how-to-create-register-datasets.md#filedataset), `train_ds`, que hace referencia a los archivos de datos preparados ubicados en el directorio `training_data` de `mydatastore`.  
* Crea la variable `input1`, que se puede usar más adelante para que los archivos de datos del conjunto de datos `train_ds` estén disponibles para un destino de proceso correspondiente a las tareas de entrenamiento.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>Uso de `ScriptRunConfig` para enviar una ejecución de experimento a un grupo de Synapse Spark

Si está listo para automatizar y enviar a producción las tareas de limpieza y transformación de datos, puede enviar una ejecución de experimento a [un grupo de Spark de Synapse asociado](how-to-link-synapse-ml-workspaces.md#attach-a-pool-with-the-python-sdk) con el objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).  

De forma similar, si tiene una canalización de Azure Machine Learning, puede usar [SynapseSparkStep para especificar el grupo de Spark de Synapse como destino de proceso para el paso de preparación de datos de la canalización](how-to-use-synapsesparkstep.md).

La disponibilidad de los datos para el grupo de Spark de Synapse depende del tipo del conjunto de datos. 

* En el caso de FileDataset, puede usar el método [`as_hdfs()`](/python/api/azureml-core/azureml.data.filedataset#as-hdfs--). Cuando se envía la ejecución, el conjunto de datos está disponible para el grupo de Spark de Synapse como un sistema de archivos distribuido de Hadoop (HFDS). 
* Para [TabularDataset](how-to-create-register-datasets.md#tabulardataset), puede usar el método [`as_named_input()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#as-named-input-name-). 

El código siguiente: 

* Crea la variable `input2` a partir del objeto FileDataset `train_ds` que se creó en el ejemplo de código anterior.
* Crea la variable `output` con la clase HDFSOutputDatasetConfiguration. Una vez finalizada la ejecución, esta clase nos permite guardar la salida de la ejecución como el conjunto de datos `test` en el almacén de datos `mydatastore`. En el área de trabajo Azure Machine Learning, el conjunto de datos `test` está registrado con el nombre `registered_dataset`. 
* Configura los valores que debe usar la ejecución para llevarse a cabo en el grupo de Spark de Synapse. 
* Define los parámetros ScriptRunConfig para hacer lo siguiente 
  * Usar `dataprep.py` para la ejecución. 
  * Especificar qué datos debe usar como entrada y cómo hacer que estén disponibles para el grupo de Spark de Synapse.
  * Especificar dónde se almacenarán los datos de salida `output`.  

```Python
from azureml.core import Dataset, HDFSOutputDatasetConfig
from azureml.core.environment import CondaDependencies
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

input2 = train_ds.as_hdfs()
output = HDFSOutputDatasetConfig(destination=(datastore, "test").register_on_complete(name="registered_dataset")

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

conda_dep = CondaDependencies()
conda_dep.add_pip_package("azureml-core==1.20.0")

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

Una vez configurado el objeto `ScriptRunConfig`, puede enviar la ejecución.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```

Para obtener más detalles, como el script `dataprep.py` usado en este ejemplo, vea el cuaderno de [ejemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb).

Una vez preparados los datos, puede usarlos como entrada para los trabajos de entrenamiento. En el ejemplo de código mencionado anteriormente, `registered_dataset` es lo que se especificaría como datos de entrada para los trabajos de entrenamiento. 

## <a name="example-notebooks"></a>Cuadernos de ejemplo

Consulte los cuadernos de ejemplo para ver más conceptos y demostraciones de las funcionalidades de integración de Azure Synapse Analytics y Azure Machine Learning.
* [Ejecute una sesión interactiva de Spark desde un cuaderno en el área de trabajo de Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb).
* [Envíe una ejecución de experimento de Azure Machine Learning con un grupo de Spark de Synapse como destino de proceso](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb).

## <a name="next-steps"></a>Pasos siguientes

* [Entrenamiento de un modelo](how-to-set-up-training-targets.md).
* [Entrenamiento de modelos con conjuntos de datos de Azure Machine Learning](how-to-train-with-datasets.md).
