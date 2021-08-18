---
title: Uso compartido de GPU en el dispositivo Azure Stack Edge Pro GPU
description: Describe los enfoques para compartir las GPU en el dispositivo Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/01/2021
ms.author: alkohli
ms.openlocfilehash: b1fd1d77a8b2e6f7da3a88bc4d7f57336d18237c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736718"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Uso compartido de GPU en su dispositivo Azure Stack Edge Pro GPU

La unidad de procesamiento de gráficos (GPU) es un procesador especializado diseñado para acelerar la representación de gráficos. Las GPU pueden procesar muchos fragmentos de datos al mismo tiempo, por lo que resultan útiles para las aplicaciones de aprendizaje automático, edición de vídeos y juegos. Además de la CPU para el proceso de uso general, los dispositivos Azure Stack Edge Pro GPU pueden contener una o dos GPU de Nvidia Tesla T4 para cargas de trabajo con muchos procesos, como la inferencia acelerada de hardware. Para obtener más información, consulte [GPU Tesla T4 de Nvidia](https://www.nvidia.com/en-us/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>Acerca del uso compartido de GPU

Es posible que muchas cargas de trabajo de proceso o de aprendizaje automático no necesiten una GPU dedicada. Las GPU se pueden compartir y su uso compartido entre cargas de trabajo de máquinas virtuales o en contenedor ayuda a aumentar el uso de la GPU sin afectar significativamente a las ventajas de rendimiento de la GPU.  

## <a name="using-gpu-with-vms"></a>Uso de GPU con máquinas virtuales

En el dispositivo Azure Stack Edge Pro, no se puede compartir una GPU al implementar cargas de trabajo de máquinas virtuales. Una GPU solo se puede asignar a una máquina virtual. Esto implica que solo puede tener una máquina virtual con GPU en un dispositivo con una GPU y dos máquinas virtuales en un dispositivo equipado con dos GPU. Hay otros factores que también se deben tener en cuenta al usar máquinas virtuales de GPU en un dispositivo que tiene Kubernetes configurado para cargas de trabajo en contenedor. Para obtener más información, consulte [Máquinas virtuales con GPU y Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>Uso de GPU con contenedores

Si va a implementar cargas de trabajo en contenedor, una GPU puede compartirse de varias maneras en la capa de hardware y software. Con la GPU Tesla T4 en el dispositivo Azure Stack Edge Pro, estamos limitados al uso compartido de software. En el dispositivo, se usan los dos enfoques siguientes para el uso compartido de software de GPU: 

- El primer enfoque implica el uso de variables de entorno para especificar el número de GPU que se pueden compartir en el tiempo. Tenga en cuenta las siguientes advertencias al usar este enfoque:

    - Puede especificar una o ambas GPU con este método. No es posible especificar el uso fraccionario.
    - Varios módulos pueden asignarse a una GPU, pero un mismo módulo no se puede asignar a más de una GPU.
    - Con la salida de Nvidia SMI, puede ver el uso general de la GPU, incluido el uso de memoria.
    
    Para obtener más información, consulte como [implementar un módulo de IOT Edge que usa GPU](azure-stack-edge-gpu-configure-gpu-modules.md) en el dispositivo.

- El segundo enfoque requiere que se habilite el servicio de varios procesos en las GPU de Nvidia. MPS es un servicio en tiempo de ejecución que permite que varios procesos que usan CUDA se ejecuten simultáneamente en una sola GPU compartida. MPS permite la superposición de operaciones de kernel y memcopy de distintos procesos en la GPU para lograr el uso máximo. Para más información, consulte [Servicio multiproceso](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

    Tenga en cuenta las siguientes advertencias al usar este enfoque:
    
    - MPS permite especificar más marcas en la implementación de GPU.
    - Puede especificar el uso fraccionario a través de MPS, lo que limita el uso de cada aplicación implementada en el dispositivo. Puede especificar el porcentaje de GPU que se usará para cada aplicación en la sección `env` de `deployment.yaml` agregando el siguiente parámetro: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>Utilización de GPU
 
Cuando se comparte la GPU en las cargas de trabajo en contenedor implementadas en el dispositivo, puede usar Nvidia System Management Interface (nvidia-smi). Nvidia-smi es una utilidad de línea de comandos que le ayuda a administrar y supervisar dispositivos GPU de Nvidia. Para obtener más información, consulte [Nvidia System Management Interface](https://developer.nvidia.com/nvidia-system-management-interface).

Para ver el uso de la GPU, primero conéctese a la interfaz de PowerShell del dispositivo. Ejecute el comando `Get-HcsNvidiaSmi` y vea la salida de Nvidia SMI. También puede ver cómo cambia el uso de la GPU habilitando MPS e implementando varias cargas de trabajo en el dispositivo. Para más información, consulte [Habilitación del servicio multiproceso](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps).


## <a name="next-steps"></a>Pasos siguientes

- [Uso compartido de GPU para implementaciones de Kubernetes en Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Uso compartido de GPU para implementaciones de IoT en Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
