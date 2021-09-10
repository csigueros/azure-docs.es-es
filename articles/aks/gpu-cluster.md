---
title: Uso de las GPU en Azure Kubernetes Service (AKS)
description: Obtenga información acerca de cómo usar GPU para cargas de trabajo de cálculo de alto rendimiento o de uso intensivo de gráficos en Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: fa7415f015ad17cc2e8a5ff4822c8ff53578f054
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751528"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Uso de GPU para cargas de trabajo de cálculo intensivo en Azure Kubernetes Service (AKS)

Por lo general, las unidades de procesamiento gráfico (GPU) se usan para cargas de trabajo de cálculo intensivo, como cargas de trabajo de visualización y gráficos. AKS admite la creación de grupos de nodos compatibles con GPU para ejecutar estas cargas de trabajo de cálculo intensivo en Kubernetes. Para obtener más información sobre las máquinas virtuales habilitadas para GPU disponibles, consulte [Tamaños de máquinas virtuales optimizadas para GPU en Azure][gpu-skus]. Para grupos de nodos de AKS, se recomienda un tamaño mínimo de *Standard_NC6*.

> [!NOTE]
> Las máquinas virtuales habilitadas para GPU contienen hardware especializado que está sujeto a una mayor disponibilidad de precios y región. Para obtener más información, consulte la herramienta de [precios][azure-pricing] y la [disponibilidad de regiones][azure-availability].

Actualmente, el uso de grupos de nodos habilitados para GPU solo está disponible para grupos de nodos de Linux.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, consulte [Inicio rápido: Implementación de un clúster de Azure Kubernetes Service mediante la CLI de Azure][aks-quickstart].

También es preciso tener instalada y configurada la versión 2.0.64 de la CLI de Azure u otra versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][install-azure-cli].

## <a name="get-the-credentials-for-your-cluster"></a>Obtención de las credenciales del clúster

Para obtener las credenciales de su clúster de AKS, use el comando [az aks get-credentials][az-aks-get-credentials]. Con el siguiente comando de ejemplo se obtienen las credenciales del clúster *myAKSCluster* en el grupo de recursos *myResourceGroup*.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-the-nvidia-device-plugin"></a>Incorporación del complemento de dispositivo NVIDIA

Hay dos opciones para agregar el complemento de dispositivo NVIDIA:

* Usar la imagen de GPU de AKS.
* Instalar manualmente el complemento.

> [!WARNING]
> Aunque puede usar cualquiera de las opciones anteriores, no instale manualmente el conjunto de demonios del complemento de dispositivo NVIDIA con clústeres que usen la imagen de GPU de AKS.

### <a name="update-your-cluster-to-use-the-aks-gpu-image-preview"></a>Actualización del clúster para usar la imagen de GPU de AKS (versión preliminar)

AKS proporciona una imagen de AKS totalmente configurada que ya contiene el [complemento de dispositivo NVIDIA para Kubernetes][nvidia-github].

Registre la característica `GPUDedicatedVHDPreview`:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Pueden pasar unos minutos hasta que el estado aparezca como **Registrado**. Puede comprobar el estado del registro con el comando [az feature list](/cli/azure/feature#az_feature_list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Cuando el estado se muestre como Registrado, actualice el registro del proveedor de recursos `Microsoft.ContainerService` mediante el comando [az provider register](/cli/azure/provider#az_provider_register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Para instalar la extensión aks-preview de la CLI, use los siguientes comandos de la CLI de Azure:

```azurecli
az extension add --name aks-preview
```

Para actualizar la extensión aks-preview de la CLI, use los siguientes comandos de la CLI de Azure:

```azurecli
az extension update --name aks-preview
```

## <a name="add-a-node-pool-for-gpu-nodes"></a>Incorporación de un grupo de nodos para nodos de GPU

Para agregar un grupo de nodos a su clúster, use [az aks nodepool add][az-aks-nodepool-add].

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --aks-custom-headers UseGPUDedicatedVHD=true \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

El comando anterior agrega un grupo de nodos denominado *gpunp* al clúster *myAKSCluster* en el grupo *myResourceGroup*. También establece el tamaño de máquina virtual de los nodos del grupo en *Standard_NC6*, habilita el escalador automático de clústeres, lo configura para mantener en el grupo un mínimo de un nodo y un máximo de tres y especifica un nodo de imagen de GPU de AKS especializado en el nuevo grupo y un valor taint *sku=gpu:NoSchedule* para este.

> [!NOTE]
> Aunque solo es posible establecer un valor taint y un tamaño de máquina virtual para los grupos de nodos al crearlos, la configuración del escalador automático se puede actualizar en cualquier momento.

> [!NOTE]
> Si la SKU de GPU requiere máquinas virtuales de generación 2, use *--aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true*. Por ejemplo:
> 
> ```azurecli
> az aks nodepool add \
>    --resource-group myResourceGroup \
>    --cluster-name myAKSCluster \
>    --name gpunp \
>    --node-count 1 \
>    --node-vm-size Standard_NC6 \
>    --node-taints sku=gpu:NoSchedule \
>    --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true \
>    --enable-cluster-autoscaler \
>    --min-count 1 \
>    --max-count 3
> ```

### <a name="manually-install-the-nvidia-device-plugin"></a>Instalación manual del complemento

También puede implementar un objeto DaemonSet para el complemento de dispositivo NVIDIA. Este objeto DaemonSet ejecuta un pod en cada nodo para proporcionar los controladores requeridos para las GPU.

Para agregar un grupo de nodos a su clúster, use [az aks nodepool add][az-aks-nodepool-add].

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

El comando anterior agrega un grupo de nodos denominado *gpunp* al clúster *myAKSCluster* en el grupo *myResourceGroup*. También establece el tamaño de máquina virtual de los nodos del grupo en *Standard_NC6*, habilita el escalador automático de clústeres, lo configura para mantener en el grupo un mínimo de un nodo y un máximo de tres y especifica un valor taint *sku=gpu:NoSchedule* para el grupo.

> [!NOTE]
> Aunque solo es posible establecer un valor taint y un tamaño de máquina virtual para los grupos de nodos al crearlos, la configuración del escalador automático se puede actualizar en cualquier momento.

Cree un espacio de nombres mediante el comando [kubectl create namespace][kubectl-create], como *gpu-resources*:

```console
kubectl create namespace gpu-resources
```

Cree un archivo denominado *nvidia-device-plugin-ds.yaml* y pegue el siguiente manifiesto YAML. Este manifiesto se proporciona como parte del [complemento de dispositivo NVIDIA para el proyecto de Kubernetes][nvidia-github].

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Use [kubectl apply][kubectl-apply] para crear el objeto DaemonSet y confirme que el complemento de dispositivo NVIDIA se cree correctamente, tal como se muestra en la siguiente salida de ejemplo:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Confirmación de que las GPU son programables

Con el clúster de AKS creado, confirme que las GPU son programables en Kubernetes. Primero, enumere los nodos del clúster con el comando [kubectl get nodes][kubectl-get]:

```console
$ kubectl get nodes

NAME                   STATUS   ROLES   AGE   VERSION
aks-gpunp-28993262-0   Ready    agent   13m   v1.20.7
```

Ahora, use el comando [kubectl describe node][kubectl-describe] para confirmar que las GPU son programables. En la sección *Capacidad*, la GPU debe aparecer como `nvidia.com/gpu:  1`.

En el siguiente ejemplo reducido se muestra que una GPU está disponible en el nodo denominado *aks-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-gpunp-28993262-0

Name:               aks-gpunp-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
[...]
 nvidia.com/gpu:                 1
[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Ejecución de una carga de trabajo habilitada para GPU

Para ver la GPU en acción, programe una carga de trabajo compatible con la GPU con la solicitud de recursos adecuada. En este ejemplo se va a ejecutar un trabajo [Tensorflow](https://www.tensorflow.org/) en el [conjunto de datos MNIST](http://yann.lecun.com/exdb/mnist/).

Cree un archivo denominado *samples-tf-mnist-demo.yaml* y pegue el siguiente manifiesto YAML. El siguiente manifiesto de trabajo incluye un límite de recursos de `nvidia.com/gpu: 1`:

> [!NOTE]
> Si recibe un error de coincidencia de versión al llamar a los controladores, como, por ejemplo, que la versión del controlador CUDA no es suficiente para la versión en tiempo de ejecución de CUDA, revise el gráfico de compatibilidad de la matriz de controladores de NVIDIA: [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html).

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
      tolerations:
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
```

Use el comando [kubectl apply][kubectl-apply] para ejecutar el trabajo. Este comando analiza el archivo de manifiesto y crea los objetos de Kubernetes definidos:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Ver el estado y la salida de la carga de trabajo habilitada para GPU

Supervise el progreso del trabajo mediante el comando [kubectl get jobs][kubectl-get] con el argumento `--watch`. Puede tardar unos minutos en extraer primero la imagen y procesar el conjunto de datos. Cuando la columna *COMPLETIONS* (FINALIZACIONES) muestra *1/1*, significa que el trabajo ha finalizado correctamente. Salga del comando `kubetctl --watch` con *Ctrl-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Para ver la salida de la carga de trabajo habilitada para GPU, obtenga primero el nombre del pod con el comando [kubectl get pods][kubectl-get]:

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Ahora, use el comando [kubectl logs][kubectl-logs] para ver los registros del pod. En el siguiente ejemplo, los registros de pod confirman que se ha descubierto el dispositivo GPU adecuado, `Tesla K80`. Proporcione el nombre para su pod:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="use-container-insights-to-monitor-gpu-usage"></a>Uso de información de contenedores para supervisar el uso de GPU

Dispone de las siguientes métricas de [información de contenedores con AKS][aks-container-insights] para supervisar el uso de GPU.

| Nombre de métrica | Dimensión de métrica (etiquetas) | Descripción |
|-------------|-------------------------|-------------|
| containerGpuDutyCycle | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor`   | Porcentaje de tiempo del período de muestra anterior (60 segundos) durante el cual la GPU estuvo ocupada o realizando un procesamiento activo para un contenedor. El ciclo de servicio es un número comprendido entre 1 y 100. |
| containerGpuLimits | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | Cada contenedor puede especificar límites para una o varias GPU. No es posible solicitar ni limitar una fracción de GPU. |
| containerGpuRequests | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | Cada contenedor puede solicitar una o varias GPU. No es posible solicitar ni limitar una fracción de GPU. |
| containerGpumemoryTotalBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | Cantidad de memoria de GPU en bytes disponible para su uso en un contenedor específico. |
| containerGpumemoryUsedBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | Cantidad de memoria de GPU en bytes que usa un contenedor específico. |
| nodeGpuAllocatable | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | Número de GPU en un nodo que puede usar Kubernetes. |
| nodeGpuCapacity | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | Número total de GPU en un nodo. |

## <a name="clean-up-resources"></a>Limpieza de recursos

Para eliminar los objetos de Kubernetes asociados que creó en este artículo, use el comando [kubectl delete job][kubectl delete] como se muestra a continuación:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Pasos siguientes

Para ejecutar trabajos de Apache Spark, consulte [Ejecución de trabajos de Apache Spark en AKS][aks-spark].

Para obtener más información acerca de la ejecución de cargas de trabajo de aprendizaje automático (ML) en Kubernetes, vea [Kubeflow Labs][kubeflow-labs].

Para más información sobre el uso de Azure Kubernetes Service con Azure Machine Learning, consulte los artículos siguientes:

* [Implementación de un modelo en Azure Kubernetes Service][azureml-aks].
* [Implementación de un modelo de aprendizaje profundo para la inferencia con GPU][azureml-gpu].
* [Servicio de alto rendimiento con Triton Inference Server][azureml-triton].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
[azureml-aks]: ../machine-learning/how-to-deploy-azure-kubernetes-service.md
[azureml-gpu]: ../machine-learning/how-to-deploy-inferencing-gpus.md
[azureml-triton]: ../machine-learning/how-to-deploy-with-triton.md
[aks-container-insights]: monitor-aks.md#container-insights