---
title: Uso de la GPU o VPU de aceleración de proceso en dispositivos de Azure Stack Edge para implementaciones de Kubernetes| Microsoft Docs
description: Se describe cómo usar la GPU o VPU de aceleración de proceso en dispositivos de Azure Stack Edge Pro con GPU, Azure Stack Edge Pro R o Azure Stack Edge Mini R para implementaciones de Kubernetes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.openlocfilehash: ad071118eabafdfeaddcf1e2a4738c646f62986d
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720001"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Uso de la aceleración de proceso en Azure Stack Edge Pro con GPU para la implementación de Kubernetes

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo usar la aceleración de proceso en dispositivos de Azure Stack Edge al usar implementaciones de Kubernetes. 


## <a name="about-compute-acceleration"></a>Acerca de la aceleración de proceso 

La unidad de procesamiento central (CPU) es el proceso predeterminado de uso general en la mayor parte de los procesos que se ejecutan en un equipo. A menudo, se usa un hardware de equipo especializado para realizar algunas funciones de forma más eficaz que las que se ejecutan en el software en una CPU. Por ejemplo, se puede usar una unidad de procesamiento gráfico (GPU) para acelerar el procesamiento de datos de píxeles.  

La aceleración de proceso es un término que se utiliza específicamente para dispositivos de Azure Stack Edge en los que se usa una unidad de procesamiento gráfico (GPU), una unidad de procesamiento de visión (VPU) o una matriz de puertas programables (FPGA) para la aceleración de hardware. La mayoría de las cargas de trabajo implementadas en el dispositivo de Azure Stack Edge implican un control de tiempo crítico, varias transmisiones de cámara o velocidades de fotogramas elevadas, lo que requiere una aceleración de hardware más específica.

En este artículo se describe la aceleración de proceso solo con GPU o VPU para los siguientes dispositivos:

- **Azure Stack Edge Pro con GPU**: estos dispositivos pueden tener 1 o 2 GPU NVIDIA T4 con núcleo Tensor. Para obtener más información, consulte [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge Pro R**: estos dispositivos tienen 1 GPU NVIDIA T4 con núcleo Tensor. Para obtener más información, consulte [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge Mini R**: estos dispositivos tienen 1 VPU Intel Movidius Myriad X. Para obtener más información, consulte [VPU Intel Movidius Myriad X](https://www.movidius.com/MyriadX).


## <a name="use-gpu-for-kubernetes-deployment"></a>Uso de la GPU para la implementación de Kubernetes

El siguiente ejemplo `yaml` se puede usar para un dispositivo de Azure Stack Edge Pro con GPU o un dispositivo Azure Stack Edge Pro R con una GPU.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Uso de VPU para la implementación de Kubernetes

El siguiente ejemplo `yaml` se puede usar para un dispositivo de Azure Stack Edge Mini R que tiene una VPU.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre el [Uso de kubectl para ejecutar una aplicación con estado de Kubernetes con PersistentVolume en el dispositivo de Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
