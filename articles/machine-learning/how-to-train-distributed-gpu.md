---
title: Guía de entrenamiento distribuido de GPU
titleSuffix: Azure Machine Learning
description: Conozca los procedimientos recomendados para realizar el entrenamiento distribuido con marcos compatibles con Azure Machine Learning, como MPI, Horovod, DeepSpeed, PyTorch, PyTorch Lightning, Hugging Face Transformers, TensorFlow e InfiniBand.
author: fuhuifang
ms.author: fufang
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: ff95cb0004fbde34e70bcb778d93a2f381dc5830
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122661659"
---
# <a name="distributed-gpu-training-guide"></a>Guía de entrenamiento distribuido de GPU

Obtenga más información sobre el empleo de código de entrenamiento distribuido de GPU en Azure Machine Learning (ML). En este artículo no se enseña sobre el entrenamiento distribuido,  sino que se ayuda a ejecutar el código de entrenamiento distribuido existente en Azure Machine Learning. Se ofrecen sugerencias y ejemplos que se pueden seguir para cada marco:

* Interfaz de paso de mensajes (MPI)
    * Horovod
    * DeepSpeed
    * Variables de entorno de Open MPI
* PyTorch
    * Inicialización de grupo de control de proceso
    * Opciones de inicio
    * DistributedDataParallel (per-process-launch)
    * Uso de `torch.distributed.launch` (inicio por nodo)
    * PyTorch Lightning
    * Hugging Face Transformers
* TensorFlow
    * Variables de entorno de TensorFlow (TF_CONFIG)
* Aceleración de entrenamiento de GPU con InfiniBand

## <a name="prerequisites"></a>Requisitos previos

Revise estos [conceptos básicos de entrenamiento distribuido de GPU](concept-distributed-training.md), como el _paralelismo de datos_, el _paralelismo de datos distribuido_ y el _paralelismo de modelos_.

> [!TIP]
> Si no sabe qué tipo de paralelismo usar, más del 90 % de las veces debe tratarse de __Paralelismo de datos distribuido__.

## <a name="mpi"></a>MPI

Azure ML ofrece un [trabajo de MPI](https://www.mcs.anl.gov/research/projects/mpi/) para iniciar un determinado número de procesos en cada nodo. Puede adoptar este enfoque para ejecutar el entrenamiento distribuido mediante el iniciador por proceso o por nodo, en función de si `process_count_per_node` está establecido en 1 (valor predeterminado) en el iniciador por nodo, o es igual al número de dispositivos o GPU en el iniciador por proceso. Azure ML crea el comando de inicio de MPI completo (`mpirun`) en segundo plano.  No puede proporcionar sus propios comandos completos de iniciador de nodo principal, como `mpirun` o `DeepSpeed launcher`.

> [!TIP]
> La imagen base de Docker que usa un trabajo de MPI de Azure Machine Learning debe tener instalada una biblioteca de MPI. [Open MPI](https://www.open-mpi.org/) está incluido en todas las [imágenes base de GPU de Azure ML](https://github.com/Azure/AzureML-Containers). Si usa una imagen de Docker personalizada, es responsable de asegurarse de que esta incluya una biblioteca de MPI. Se recomienda Open MPI, pero también puede usar otra implementación de MPI, como Intel MPI. Azure ML también proporciona [entornos mantenidos](resource-curated-environments.md) de marcos populares. 

Para ejecutar el entrenamiento distribuido mediante MPI, siga estos pasos:

1. Use un entorno de Azure ML con el marco de aprendizaje profundo y MPI preferidos. Azure ML proporciona [entornos mantenidos](resource-curated-environments.md) de marcos populares.
1. Defina `MpiConfiguration` con `process_count_per_node` y `node_count`. `process_count_per_node` debe ser igual al número de GPU por nodo en el inicio por proceso, o establecerse en 1 (valor predeterminado) en el inicio por nodo si el script de usuario es responsable de iniciar los procesos por nodo.
1. Pase el objeto `MpiConfiguration` al parámetro `distributed_job_config` de `ScriptRunConfig`.

```python
from azureml.core import Workspace, ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import MpiConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = MpiConfiguration(process_count_per_node=4, node_count=2)

run_config = ScriptRunConfig(
  source_directory= './src',
  script='train.py',
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

### <a name="horovod"></a>Horovod

Use la configuración del trabajo de MPI si usa [Horovod](https://horovod.readthedocs.io/en/stable/index.html) para el entrenamiento distribuido con el marco de aprendizaje profundo.

Asegúrese de que el código siga estas sugerencias:

* El código de entrenamiento se instrumenta correctamente con Horovod antes de agregar los elementos de Azure ML.
* El entorno de Azure ML contiene Horovod y MPI. Los entornos de GPU mantenidos de PyTorch y TensorFlow vienen preconfigurados con Horovod y sus dependencias.
* Cree un elemento `MpiConfiguration` con la distribución deseada.

### <a name="horovod-example"></a>Ejemplo de Horovod

* [azureml-examples: Entrenamiento distribuido de TensorFlow con Horovod](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed-horovod)

### <a name="deepspeed"></a>DeepSpeed

No use el iniciador personalizado de DeepSpeed para ejecutar el entrenamiento distribuido con la biblioteca [DeepSpeed](https://www.deepspeed.ai/) en Azure ML. En su lugar, configure un trabajo de MPI para iniciar el trabajo de entrenamiento [con MPI](https://www.deepspeed.ai/getting-started/#mpi-and-azureml-compatibility).

Asegúrese de que el código siga estas sugerencias:

* El entorno de Azure ML contiene DeepSpeed y sus dependencias, Open MPI y mpi4py.
* Cree un elemento `MpiConfiguration` con la distribución.

### <a name="deepseed-example"></a>Ejemplo de DeepSeed

* [azureml-examples: Entrenamiento distribuido con DeepSpeed en CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/deepspeed/cifar)

### <a name="environment-variables-from-open-mpi"></a>Variables de entorno de Open MPI

Al ejecutar trabajos de MPI con imágenes de Open MPI, se inician las siguientes variables de entorno de cada proceso:

1. `OMPI_COMM_WORLD_RANK`: clasificación del proceso
2. `OMPI_COMM_WORLD_SIZE`: tamaño del mundo
3. `AZ_BATCH_MASTER_NODE`: dirección principal con puerto, `MASTER_ADDR:MASTER_PORT`
4. `OMPI_COMM_WORLD_LOCAL_RANK`: clasificación local del proceso en el nodo
5. `OMPI_COMM_WORLD_LOCAL_SIZE`: número de procesos en el nodo

> [!TIP]
> A pesar del nombre, la variable de entorno `OMPI_COMM_WORLD_NODE_RANK` no corresponde a `NODE_RANK`. Para usar el iniciador por nodo, establezca `process_count_per_node=1` y use `OMPI_COMM_WORLD_RANK` como `NODE_RANK`.

## <a name="pytorch"></a>PyTorch

Azure ML permite ejecutar trabajos distribuidos mediante las capacidades de entrenamiento distribuido nativas de PyTorch (`torch.distributed`).

> [!TIP]
> En el caso del paralelismo de datos, las [instrucciones oficiales de PyTorch](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html#comparison-between-dataparallel-and-distributeddataparallel) son usar DistributedDataParallel (DDP) en lugar de DataParallel para el entrenamiento distribuido de un solo nodo y de varios nodos. PyTorch además [recomienda usar DistributedDataParallel en lugar del paquete de multiprocesamiento](https://pytorch.org/docs/stable/notes/cuda.html#use-nn-parallel-distributeddataparallel-instead-of-multiprocessing-or-nn-dataparallel). Por tanto, la documentación y los ejemplos de Azure Machine Learning se centrarán en el entrenamiento DistributedDataParallel.

### <a name="process-group-initialization"></a>Inicialización de grupo de control de proceso

La columna vertebral de cualquier entrenamiento distribuido se basa en un grupo de procesos que se conocen entre sí y que pueden comunicarse entre ellos mediante un back-end. En PyTorch, el grupo de control de proceso se crea al llamar a [torch.distributed.init_process_group](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) en __todos los procesos distribuidos__ para formar colectivamente un grupo de control de proceso.

```
torch.distributed.init_process_group(backend='nccl', init_method='env://', ...)
```

Los back-end de comunicación más comunes que se usan son `mpi`, `nccl` y `gloo`. En el caso del entrenamiento basado en GPU, se recomienda `nccl` para lograr el mejor rendimiento, y se debe usar siempre que sea posible. 

`init_method` indica cómo pueden detectarse los procesos entre sí, cómo se inicializan, y comprueba el grupo de control de proceso mediante el back-end de comunicación. De manera predeterminada, si no se especifica `init_method`, PyTorch usa el método de inicialización de la variable de entorno (`env://`). `init_method` es el método de inicialización recomendado que se usa en el código de entrenamiento para ejecutar PyTorch distribuido en Azure ML.  PyTorch busca las siguientes variables de entorno para la inicialización:

- **`MASTER_ADDR`** : dirección IP de la máquina que va a hospedar el proceso con la clasificación 0.
- **`MASTER_PORT`** : puerto libre de la máquina que va a hospedar el proceso con la clasificación 0.
- **`WORLD_SIZE`** : número total de procesos. Debe ser igual al número total de dispositivos (GPU) usados para el entrenamiento distribuido.
- **`RANK`** : clasificación (global) del proceso actual. Los valores posibles son de 0 a (tamaño mundial - 1).

Para obtener más información sobre la inicialización de grupos de control de procesos, vea la [documentación de PyTorch](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group).

Además de estas, muchas aplicaciones también necesitan las siguientes variables de entorno:
- **`LOCAL_RANK`** : clasificación local (relativa) del proceso dentro del nodo. Los valores posibles son de 0 a (n.º de procesos en el nodo - 1). Esta información es útil porque muchas operaciones, como la preparación de datos, solo se deben realizar una vez por nodo, normalmente en local_rank = 0.
- **`NODE_RANK`** : clasificación del nodo para el entrenamiento de varios nodos. Los valores posibles son de 0 a (número total de nodos - 1).

### <a name="pytorch-launch-options"></a>Opciones de inicio de PyTorch

El trabajo de PyTorch de Azure ML admite dos tipos de opciones para iniciar el entrenamiento distribuido:

- __Iniciador por proceso__: el sistema inicia todos los procesos distribuidos automáticamente, con toda la información relevante (como variables de entorno) para configurar el grupo de control de proceso.
- __Iniciador por nodo__: proporcione a Azure ML el iniciador de la utilidad que se vaya a ejecutar en cada nodo. El iniciador de la utilidad controla el inicio de cada uno de los procesos en un nodo determinado. El iniciador configura `RANK` y `LOCAL_RANK` de forma local en cada nodo. La utilidad **torch.distributed.launch** y PyTorch Lightning pertenecen a esta categoría.

No hay diferencias fundamentales entre estas opciones de inicio. La elección depende en gran medida de las preferencias del usuario o de las convenciones de los marcos o las bibliotecas creados a partir de Vanilla PyTorch (como Lightning o Hugging Face).

En las secciones siguientes se detalla cómo configurar trabajos de PyTorch de Azure ML en cada una de las opciones de inicio.

### <a name="distributeddataparallel-per-process-launch"></a><a name="per-process-launch"></a> DistributedDataParallel (per-process-launch)

No es necesario usar una utilidad de iniciador como `torch.distributed.launch`. Para ejecutar un trabajo distribuido de PyTorch:

1. Especificar el script de entrenamiento y los argumentos
1. Cree un elemento `PyTorchConfiguration` y especifique `process_count` y `node_count`. `process_count` corresponde al número total de procesos que desea ejecutar para su trabajo. `process_count` normalmente debe ser igual a `# GPUs per node x # nodes`. Si no se especifica `process_count`, Azure ML inicia de manera predeterminada un proceso por nodo.

Azure ML establece las variables de entorno `MASTER_ADDR`, `MASTER_PORT`, `WORLD_SIZE` y `NODE_RANK` en cada nodo, además de las variables de entorno `RANK` y `LOCAL_RANK` de nivel de proceso.

Para usar esta opción para el entrenamiento de varios procesos por nodo, emplee Azure ML Python SDK `>= 1.22.0`. Process_count se incorporó en la versión 1.22.0.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=8, node_count=2)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 50],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> Si el script de entrenamiento pasa información como la clasificación local o la clasificación como argumentos de script, puede hacer referencia a las variables de entorno en los argumentos:
>
> ```python
> arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]
> ```

### <a name="pytorch-per-process-launch-example"></a>Ejemplo de inicio por proceso de Pytorch

- [azureml-examples: Entrenamiento distribuido con PyTorch en CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="using-torchdistributedlaunch-per-node-launch"></a><a name="per-node-launch"></a> Uso de `torch.distributed.launch` (inicio por nodo)

PyTorch proporciona una utilidad de inicio en [torch.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) que se puede usar para iniciar varios procesos por nodo. El módulo `torch.distributed.launch` genera varios procesos de entrenamiento en cada uno de los nodos.

Los siguientes pasos muestran cómo configurar un trabajo de PyTorch con un iniciador por nodo en Azure ML.  El trabajo logra el equivalente a ejecutar el comando siguiente:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Proporcione el comando `torch.distributed.launch` al parámetro `command` del constructor `ScriptRunConfig`. Azure ML ejecuta este comando en cada nodo del clúster de entrenamiento. `--nproc_per_node` debe ser menor o igual que el número de GPU disponibles en cada nodo. Azure ML establece MASTER_ADDR, MASTER_PORT y NODE_RANK, por lo que solo puede hacer referencia a las variables de entorno en el comando. Azure ML establece MASTER_PORT en `6105`, pero puede pasar otro valor al argumento `--master_port` del comando torch.distributed.launch si quiere. (La utilidad de inicio restablecerá las variables de entorno).
2. Cree un elemento `PyTorchConfiguration` y especifique el valor de `node_count`.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> **Entrenamiento de varias GPU de nodo único:** si usa la utilidad de inicio para ejecutar el entrenamiento de PyTorch de varias GPU de nodo único, no es necesario especificar el parámetro `distributed_job_config` de ScriptRunConfig.
>
>```python
> launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --use_env train.py --epochs 50".split()
>
> run_config = ScriptRunConfig(
>  source_directory='./src',
>  command=launch_cmd,
>  compute_target=compute_target,
>  environment=pytorch_env,
> )
> ```

### <a name="pytorch-per-node-launch-example"></a>Ejemplo de inicio por nodo de PyTorch

- [azureml-examples: Entrenamiento distribuido con PyTorch en CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="pytorch-lightning"></a>PyTorch Lightning

[PyTorch Lightning](https://pytorch-lightning.readthedocs.io/en/stable/) es una biblioteca ligera de código abierto que proporciona una interfaz de alto nivel para PyTorch. Lightning quita muchas de las configuraciones de entrenamiento distribuido de nivel inferior necesarias para Vanilla PyTorch. Lightning permite ejecutar los scripts de entrenamiento en una sola GPU, varias GPU de nodo único y varias GPU de varios nodos. En segundo plano, inicia varios procesos automáticamente de manera similar a `torch.distributed.launch`.

En el caso del entrenamiento de nodo único (lo que incluye varias GPU de nodo único), puede ejecutar el código en Azure ML sin necesidad de especificar `distributed_job_config`. En el caso del entrenamiento de varios nodos, Lightning exige que se establezcan las siguientes variables de entorno en cada nodo del clúster de entrenamiento:

- MASTER_ADDR
- MASTER_PORT
- NODE_RANK

Para ejecutar el entrenamiento de Lightning de varios nodos en Azure ML, puede seguir las [instrucciones de inicio por nodo](#per-node-launch):

- Defina `PyTorchConfiguration` y especifique `node_count`. No especifique `process_count`, ya que Lightning controla internamente el inicio de los procesos de trabajo de cada nodo.
- En los trabajos de PyTorch, Azure ML controla el establecimiento de las variables de entorno MASTER_ADDR, MASTER_PORT y NODE_RANK requeridas por Lightning.
- Lightning controla el cálculo del tamaño del mundo de las marcas de Trainer `--gpus` y `--num_nodes` y administra la clasificación y la clasificación local internamente.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.runconfig import PyTorchConfiguration

nnodes = 2
args = ['--max_epochs', 50, '--gpus', 2, '--accelerator', 'ddp', '--num_nodes', nnodes]
distr_config = PyTorchConfiguration(node_count=nnodes)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=args,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

### <a name="pytorch-lightning-example"></a>Ejemplo de Lightning de PyTorch

* [azureml-examples: Entrenamiento de varios nodos con Lightning de PyTorch](https://github.com/Azure/azureml-examples/blob/main/python-sdk/experimental/using-pytorch-lightning/4.train-multi-node-ddp.ipynb)

### <a name="hugging-face-transformers"></a>Hugging Face Transformers

Hugging Face proporciona muchos [ejemplos](https://github.com/huggingface/transformers/tree/master/examples) para usar su biblioteca Transformers con `torch.distributed.launch` para ejecutar el entrenamiento distribuido. Para ejecutar estos ejemplos y sus propios scripts de entrenamiento personalizados mediante Transformers Trainer API, siga la sección [Uso de `torch.distributed.launch`](#per-node-launch).

Código de configuración de trabajo de ejemplo para perfeccionar el modelo grande de BERT en la tarea MNLI de clasificación de texto mediante el script `run_glue.py` en un nodo con ocho GPU:

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

distr_config = PyTorchConfiguration() # node_count defaults to 1
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 8 text-classification/run_glue.py --model_name_or_path bert-large-uncased-whole-word-masking --task_name mnli --do_train --do_eval --max_seq_length 128 --per_device_train_batch_size 8 --learning_rate 2e-5 --num_train_epochs 3.0 --output_dir /tmp/mnli_output".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)
```

También puede usar la opción de [inicio por proceso](#per-process-launch) para ejecutar el entrenamiento distribuido sin usar `torch.distributed.launch`. Una cosa que hay que tener en cuenta si se usa este método es que los transformadores [TrainingArguments](https://huggingface.co/transformers/main_classes/trainer.html?highlight=launch#trainingarguments) esperan que la clasificación local se pase como un argumento (`--local_rank`). `torch.distributed.launch` se encarga de esto cuando `--use_env=False`, pero si usa el inicio por proceso, tiene que pasar explícitamente la clasificación local como argumento al script de entrenamiento `--local_rank=$LOCAL_RANK`, ya que Azure ML solo establece la variable de entorno `LOCAL_RANK`.

## <a name="tensorflow"></a>TensorFlow

Si usa [TensorFlow distribuido nativo](https://www.tensorflow.org/guide/distributed_training) en el código de entrenamiento, como la API `tf.distribute.Strategy` de TensorFlow 2.x, puede iniciar el trabajo distribuido mediante Azure ML con `TensorflowConfiguration`.

Para ello, especifique un objeto `TensorflowConfiguration` en el parámetro `distributed_job_config` del constructor `ScriptRunConfig`. Si usa `tf.distribute.experimental.MultiWorkerMirroredStrategy`, especifique `worker_count` en el elemento `TensorflowConfiguration` correspondiente al número de nodos del trabajo de entrenamiento.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import TensorflowConfiguration

curated_env_name = 'AzureML-TensorFlow-2.3-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  compute_target=compute_target,
  environment=tf_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

Si el script de entrenamiento usa la estrategia de servidor de parámetros para el entrenamiento distribuido, como en TensorFlow 1.x heredado, también debe especificar el número de servidores de parámetros que se van a usar en el trabajo, por ejemplo, `tf_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)`.

### <a name="tf_config"></a>TF_CONFIG

En TensorFlow, la variable de entorno **TF_CONFIG** es necesaria para el entrenamiento en varias máquinas. En los trabajos de TensorFlow, Azure ML configura y establece la variable TF_CONFIG correctamente en cada trabajo antes de ejecutar el script de entrenamiento.

Si lo necesita, puede acceder a TF_CONFIG desde el script de entrenamiento: `os.environ['TF_CONFIG']`.

Ejemplo de TF_CONFIG establecido en un nodo de trabajo principal:
```json
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

### <a name="tensorflow-example"></a>Ejemplo de TensorFlow

- [azureml-examples: Entrenamiento distribuido de TensorFlow con MultiWorkerMirroredStrategy](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed)

## <a name="accelerating-gpu-training-with-infiniband"></a><a name="infiniband"></a> Aceleración de entrenamiento de GPU con InfiniBand

Algunas series de máquinas virtuales de Azure, en concreto NC, ND y H, ahora tienen máquinas virtuales compatibles con RDMA que admiten SR-IOV e Infiniband. Estas máquinas virtuales se comunican a través de la red InfiniBand de baja latencia y alto ancho de banda, que es mucho más eficaz que la conectividad basada en Ethernet. SR-IOV para InfiniBand permite un rendimiento prácticamente sin sistema operativo en cualquier biblioteca de MPI (MPI se usa en muchos marcos y herramientas de entrenamiento distribuido, incluido el software NCCL de NVIDIA). Estos SKU están diseñados para satisfacer las necesidades de las cargas de trabajo de aprendizaje automático de cálculo intensivo y GPU acelerada. Para obtener más información, vea [Aceleración del entrenamiento distribuido en Azure Machine Learning con SR-IOV](https://techcommunity.microsoft.com/t5/azure-ai/accelerating-distributed-training-in-azure-machine-learning/ba-p/1059050).

Si crea un clúster `AmlCompute` de uno de estos tamaños compatibles con RDMA habilitados para InfiniBand, como `Standard_ND40rs_v2`, la imagen del sistema operativo viene con el controlador Mellanox OFED necesario para habilitar InfiniBand preinstalado y preconfigurado.

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de modelos de aprendizaje automático en Azure](how-to-deploy-and-where.md)
* [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md)
* [Arquitectura de referencia para el entrenamiento del aprendizaje profundo distribuido en Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
