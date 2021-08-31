---
title: Guía de migración para cargas de trabajo de proceso de GPU en Azure
description: Guía de migración de la serie NC, ND, NCv2.
author: iafinder
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 08/15/2020
ms.author: iafinder
ms.openlocfilehash: 7ba99bca79fe25a740d11cfe5d87937d13ccdfd0
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272339"
---
# <a name="migration-guide-for-gpu-compute-workloads-in-azure"></a>Guía de migración para cargas de trabajo de proceso de GPU en Azure

A medida que las GPU más eficaces estén disponibles en Marketplace y en los centros de datos de Microsoft Azure, se recomienda volver a evaluar el rendimiento de las cargas de trabajo y considerar la posibilidad de migrar a GPU más recientes.

Por la misma razón, así como para mantener una oferta de servicio confiable y de alta calidad, Azure retira periódicamente el hardware que impulsa los tamaños de máquina virtual más antiguos. El primer grupo de productos de GPU que se retirarán en Azure son las máquinas virtuales originales de las series NC, NC v2 y ND, con tecnología de los aceleradores de GPU nvidia Tesla K80, P100 y P40, respectivamente. Estos productos se retirarán el 31 de agosto de 2022 y las máquinas virtuales más antiguas de esta serie lanzadas en 2016.

Desde entonces, las GPU han realizado avances increíbles junto con todo el aprendizaje profundo y el sector de HPC, lo que suele superar el doble de rendimiento entre generaciones. Desde el lanzamiento de LAS GPU NVIDIA K80, P40 y P100, Azure ha enviado varias generaciones y categorías más recientes de productos de máquina virtual orientados a procesos y AI acelerados por GPU, basados en las GPU T4, V100 y A100 de NVIDIA, y se ha diferenciado por características opcionales como tejidos de interconexión basados en InfiniBand. Estas son todas las opciones que animamos a los clientes a explorar como rutas de migración.

En la mayoría de los casos, el aumento drástico del rendimiento que ofrecen las nuevas generaciones de GPU reduce el TCO general al reducir la duración del trabajo, para los trabajos ampliables, o reducir la cantidad total de máquinas virtuales habilitadas para GPU necesarias para cubrir una demanda de tamaño fijo de los recursos de proceso, aunque los costes por hora de GPU pueden variar. Además de estas ventajas, los clientes pueden mejorar el tiempo de solución a través de máquinas virtuales de mayor rendimiento y mejorar el estado y la compatibilidad de su solución mediante la adopción de software más reciente, tiempo de ejecución de CUDA y versiones de controladores.

## <a name="migration-vs-optimization"></a>Migración frente a optimización

Azure reconoce que los clientes tienen una gran variedad de requisitos que pueden dictar la selección de un producto de máquina virtual de GPU específico, incluidas consideraciones de arquitectura de GPU, interconexiones, TCO, tiempo de solución y disponibilidad regional en función de la localidad de cumplimiento o los requisitos de latencia, y algunos de ellos incluso cambian con el tiempo.

Al mismo tiempo, la aceleración de GPU es un área nueva y en constante evolución.

Por lo tanto, no hay ninguna guía única verdadera para esta área de producto y una migración es un momento perfecto para volver a evaluar los cambios potencialmente drásticos en una carga de trabajo, como pasar de un modelo de implementación en clúster a una sola máquina virtual de 8 GPU grande o viceversa, aprovechar tipos de datos de precisión reducida, adoptar características como GPU de varias instancias y mucho más.

Este tipo de consideraciones, cuando se hace que el contexto de aumentos de rendimiento de GPU por generación ya drásticos, donde una característica como la adición de TensorCores, puede aumentar el rendimiento en un orden de magnitud, es muy específico de la carga de trabajo.

La combinación de la migración con la rearquitectura de la aplicación puede producir un gran valor y una mejora en el coste y el tiempo de solución.

Sin embargo, este tipo de mejoras están fuera del ámbito de este documento, que pretende centrarse en las clases de equivalencia directa para cargas de trabajo generalizadas que pueden ejecutar los clientes en la actualidad, para identificar las opciones de máquina virtual más similares en precio *y* rendimiento por GPU a las familias de máquinas virtuales existentes que están en proceso de retirada.

Por lo tanto, en este documento se supone que es posible que el usuario no tenga ninguna información o control sobre las propiedades específicas de la carga de trabajo, como el número de instancias de máquina virtual necesarias, GPU, interconexiones, etc.

## <a name="recommended-upgrade-paths"></a>Rutas de actualización recomendadas

### <a name="nc-series-vms-featuring-nvidia-k80-gpus"></a>Máquinas virtuales de la serie NC con GPU NVIDIA K80

Las máquinas virtuales de la [serie NC (v1)](./nc-series.md) son el tipo de máquina virtual de proceso acelerado por GPU más antiguo de Azure, con la tecnología de 1 a 4 aceleradores de GPU del centro de datos NVIDIA Tesla K80 emparejados con procesadores Intel Xeon E5-2690 v3 (Haswell). Durante un tiempo fueron un tipo de máquina virtual estrella para aplicaciones exigentes de IA, ML y HPC, y siguen siendo una opción popular en los últimos tiempos del ciclo de vida del producto (especialmente a través de los precios promocionales de la serie NC) para los usuarios que valoraban tener un coste absoluto muy bajo por hora de GPU sobre GPU con mayor rendimiento por dólar.

En la actualidad, dado el rendimiento de proceso relativamente bajo del envejecimiento de la plataforma GPU NVIDIA K80, en comparación con las series de máquinas virtuales que incluyen GPU más recientes, un caso de uso popular para la serie NC son las cargas de trabajo de inferencia y análisis en tiempo real, donde una máquina virtual acelerada debe estar disponible en un estado estable para atender la solicitud de las aplicaciones a medida que llegan. En estos casos, el tamaño de volumen o lote de las solicitudes puede ser insuficiente para beneficiarse de GPU de mayor rendimiento. Las máquinas virtuales NC también son populares para desarrolladores y alumnos que aprenden, desarrollan o experimentan con la aceleración de GPU, que necesitan un objetivo de implementación de CUDA basado en la nube económico en el que iterar que no es necesario realizar en los niveles de producción.

En general, los clientes de la serie NC deben considerar la posibilidad de pasar directamente de los tamaños NC a los tamaños [NC T4 v3](./nct4-v3-series.md), la nueva plataforma acelerada por GPU de Azure para cargas de trabajo ligeras con tecnología de GPU NVIDIA Tesla T4, aunque se deben tener en cuenta otras SKU de máquina virtual para cargas de trabajo que se ejecutan en tamaños de NC-Series habilitados para InfiniBand.

| Tamaño de la máquina virtual actual | Tamaño de la máquina virtual de destino | Diferencia en la especificación | 
|---|---|---|
Standard_NC6 <br> Standard_NC6_Promo | Standard_NC4as_T4_v3 <br>o<br>Standard_NC8as_T4 | CPU: Intel Haswell frente a AMD Rome<br>Recuento de GPU: 1 (igual)< br>Generación de GPU: NVIDIA Keppler frente a Turing (+2 generaciones, ~2x FP32 FLOP)<br>Memoria de GPU (GiB por GPU): 16 (+4)<br>vCPU: 4 (-2) o 8 (+2)<br>GiB de memoria: 16 (-40) o 56 (iguales)<br>GiB de almacenamiento temporal (SSD): 180 (-160) o 360 (+20)<br>Nº máx. de discos de datos: 8 (-4) o 16 (+4)<br>Redes aceleradas: Sí (+)<br>Premium Storage: Sí (+)| 
| Standard_NC24<br>Standard_NC24_Promo | Standard_NC64as_T4_v3* | CPU: Intel Haswell frente a AMD Rome<br>Recuento de GPU: 4 (iguales)<br>Generación de GPU: NVIDIA Keppler frente a Turing (+2 generaciones, ~2x FP32 FLOP)<br>Memoria de GPU (GiB por GPU): 16 (+4)<br>vCPU: 64 (+40)<br>GiB de memoria: 440 (+216)<br>GiB de almacenamiento temporal (SSD): 2880 (+1440)<br>Número máximo de discos de datos: 32 (-32)<br>Redes aceleradas: Sí (+)<br>Premium Storage: Sí (+) | 
|Standard_NC24r<br>Standard_NC24r_Promo<br><br>(Tamaños habilitados para agrupación en clústeres infiniBand) | Standard_NC24rs_v3* | CPU: Intel Haswell frente a Intel Broadwell<br>Recuento de GPU: 4 (iguales)<br>Generación de GPU: NVIDIA Keppler frente a Volta (+2 generaciones)<br>Memoria de GPU (GiB por GPU): 16 (+4)<br>vCPU: 24 (+0)<br>GiB de memoria: 448 (+224)<br>GiB de almacenamiento temporal (SSD): 2948 (+1440)<br>Nº máx. de discos de datos: 32 (iguales)<br>Redes aceleradas: No (iguales)<br>Premium Storage: Sí (+)<br>Interconexión InfiniBand: Sí | 


### <a name="nd-series-vms-featuring-nvidia-tesla-p40-gpus"></a>Máquinas virtuales de la serie ND con GPU NVIDIA Tesla P40

Las máquinas virtuales de la serie ND son una plataforma intermedia originalmente diseñada para cargas de trabajo inteligencia artificial y aprendizaje profundo. Ofrecen un rendimiento excelente para la inferencia por lotes a través de operaciones mejoradas de punto flotante de precisión sencilla sobre sus predecesores y cuentan con la tecnología de GPU NVIDIA Tesla P40 e CPU Intel Xeon E5-2690 v4 (Broadwell). Al igual que la serie NC, la serie NC v2 presenta una configuración con una baja latencia secundaria, una red de alto rendimiento mediante RDMA y conectividad InfiniBand para que pueda ejecutar trabajos de entrenamiento a gran escala que abarquen muchas GPU.

| Tamaño de la máquina virtual actual | Tamaño de la máquina virtual de destino | Diferencia en la especificación | 
|---|---|---|
|Standard_ND6 | Standard_NC4as_T4_v3<br>o<br>Standard_NC8as_T4 | CPU: Intel Broadwell frente a AMD Rome<br>Recuento de GPU: 1 (igual)<br>Generación de GPU: NVIDIA Pascal frente a Turing (+1 generación)<br>Memoria de GPU (GiB por GPU): 16 (+8)<br>vCPU: 4 (-2) o 8 (+2)<br>GiB de memoria: 16 (-40) o 56 (-56)<br>GiB de almacenamiento temporal (SSD): 180 (-552) o 360 (-372)<br>Nº máx. de discos de datos: 8 (-4) o 16 (+4)<br>Redes aceleradas: Sí (+)<br>Premium Storage: Sí (+) | 
| Standard_ND12 | Standard_NC16as_T4_v3 | CPU: Intel Broadwell frente a AMD Rome<br>Recuento de GPU: 1 (-1)<br>Generación de GPU: NVIDIA Pascal frente a Turing (+1 generaciones)<br>Memoria de GPU (GiB por GPU): 16 (+8)<br>vCPU: 16 (+4)<br>GiB de memoria: 110 (-114)<br>GiB de almacenamiento temporal (SSD): 360 (-1114)<br>Número máximo de discos de datos: 48 (+16)<br>Redes aceleradas: Sí (+)<br>Premium Storage: Sí (+) | 
| Standard_ND24 | Standard_NC64as_T4_v3* | CPU: Intel Broadwell frente a AMD Rome<br>Recuento de GPU: 4 (iguales)<br>Generación de GPU: NVIDIA Pascal frente a Turing (+1 generaciones)<br>Memoria de GPU (GiB por GPU): 16 (+8)<br>vCPU: 64 (+40)<br>GiB de memoria: 440 (iguales)<br>GiB de almacenamiento temporal (SSD): 2880 (iguales)<br>Nº máx. de discos de datos: 32 (iguales)<br>Redes aceleradas: Sí (+)<br>Premium Storage: Sí (+) | 
| Standard_ND24r | Standard_NC24rs_v3* | CPU: Intel Broadwell (igual)<br>Recuento de GPU: 4 (iguales)<br>Generación de GPU: NVIDIA Pascal frente a Volta (+1 generación)<br>Memoria de GPU (GiB por GPU): 16 (+8)<br>vCPU: 24 (+0)<br>GiB de memoria: 448 (iguales)<br>GiB de almacenamiento temporal (SSD): 2948 (igual)<br>Nº máx. de discos de datos: 32 (iguales)<br>Redes aceleradas: No (iguales)<br>Premium Storage: Sí (+)<br>Interconexión InfiniBand: Sí (igual) | 

### <a name="nc-v2-series-vms-featuring-nvidia-tesla-p100-gpus"></a>Máquinas virtuales de la serie NC v2 con GPU NVIDIA Tesla P100

Las máquinas virtuales de la serie ND v2 son una plataforma insignia originalmente diseñada para cargas de trabajo inteligencia artificial y aprendizaje profundo. Ofrecían un rendimiento excelente para el entrenamiento de aprendizaje profundo, con un rendimiento por GPU aproximadamente dos veces superior al del la serie NC original y con tecnología de GPU NVIDIA Tesla P100 e CPU Intel Xeon E5-2690 v4 (Broadwell). Al igual que la serie NC y la serie ND, la serie v2 presenta una configuración con una baja latencia secundaria, una red de alto rendimiento mediante RDMA y conectividad InfiniBand para que pueda ejecutar trabajos de entrenamiento a gran escala que abarquen muchas GPU.

| Tamaño de la máquina virtual actual | Tamaño de la máquina virtual de destino | Diferencia en la especificación | 
|---|---|---|
| Standard_NC6s_v2 | Standard_NC6s_v3 | CPU: Intel Broadwell (igual)<br>Recuento de GPU: 1 (igual)<br>Generación de GPU: NVIDIA Pascal frente a Volta (+1 generación)<br>Memoria de GPU (GiB por GPU): 16 (iguales)<br>vCPU: 6 (iguales)<br>GiB de memoria: 112 (iguales)<br>GiB de almacenamiento temporal (SSD): 736 (iguales)<br>Nº máx. de discos de datos: 12 (iguales)<br>Redes aceleradas: No (igual)<br>Premium Storage: Sí (+) | 
| Standard_NC12s_v2 | Standard_NC12s_v3 | CPU: Intel Broadwell (igual)<br>Recuento de GPU: 2 (iguales)<br>Generación de GPU: NVIDIA Pascal frente a Volta (+1 generaciones)<br>Memoria de GPU (GiB por GPU): 16 (iguales)<br>vCPU: 12 (iguales)<br>GiB de memoria: 112 (iguales)<br>GiB de almacenamiento temporal (SSD): 1474 (igual)<br>Nº máx. de discos de datos: 24 (iguales)<br>Redes aceleradas: No (igual)<br>Premium Storage: Sí (+) | 
| Standard_NC24s_v2 | Standard_NC24s_v3 | CPU: Intel Broadwell (igual)<br>Recuento de GPU: 4 (iguales)<br>Generación de GPU: NVIDIA Pascal frente a Volta (+1 generaciones)<br>Memoria de GPU (GiB por GPU): 16 (iguales)<br>vCPU: 24 (iguales)<br>GiB de memoria: 448 (iguales)<br>GiB de almacenamiento temporal (SSD): 2948 (igual)<br>Nº máx. de discos de datos: 32 (iguales)<br>Redes aceleradas: No (igual)<br>Premium Storage: Sí (+) | 
| Standard_NC24rs_v2 | Standard_NC24rs_v3* | CPU: Intel Broadwell (igual)<br>Recuento de GPU: 4 (iguales)<br>Generación de GPU: NVIDIA Pascal frente a Volta (+1 generaciones)<br>Memoria de GPU (GiB por GPU): 16 (iguales)<br>vCPU: 24 (iguales)<br>GiB de memoria: 448 (iguales)<br>GiB de almacenamiento temporal (SSD): 2948 (igual)<br>Nº máx. de discos de datos: 32 (iguales)<br>Redes aceleradas: No (igual)<br>Premium Storage: Sí (+)<br>Interconexión InfiniBand: Sí (igual)| 


## <a name="migration-steps"></a>Pasos de migración

### <a name="general-changes"></a>Cambios generales

1.  Elija una serie y un tamaño para la migración. Aproveche la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) para obtener más información.

2.  Obtención de la cuota para la serie de máquinas virtuales de destino

3.  Cambie el tamaño actual de la serie N\* de VM al tamaño objetivo. También puede ser un buen momento para actualizar el sistema operativo que usa la imagen de máquina virtual o adoptar una de las imágenes de HPC con controladores preinstalados como punto de partida.

    > [!IMPORTANT]
    > Es posible que la imagen de máquina virtual se haya producido con una versión anterior del entorno de ejecución de CUDA, el controlador NVIDIA y (si procede, solo para tamaños habilitados para RDMA) los controladores Mellanox OFED que requiere la nueva serie de máquinas virtuales de GPU, que se pueden actualizar [siguiendo las instrucciones de la documentación de Azure.](./sizes-gpu.md)

### <a name="breaking-changes"></a>Últimos cambios

#### <a name="select-target-size-for-migration"></a>Selección del tamaño de destino para la migración

Después de evaluar el uso actual, decida qué tipo de máquina virtual de GPU necesita. En función de los requisitos de carga de trabajo, tiene algunas opciones diferentes.

> [!NOTE]
> Un procedimiento recomendado es seleccionar un tamaño de máquina virtual en función del coste y el rendimiento. Las recomendaciones de esta guía se basan en una comparación uno a uno de uso general de las métricas de rendimiento y la coincidencia más cercana en otra serie de máquinas virtuales. Antes de decidir el tamaño correcto, obtenga una comparación de costes mediante la calculadora de precios de Azure.

> [!IMPORTANT]
> Todos los tamaños heredados de las series NC, NC v2 y ND están disponibles en tamaños de varias GPU, incluidos los tamaños de 4 GPU con y sin interconexión InfiniBand para cargas de trabajo de escalado horizontal y estrechamente acopladas que exigen más potencia de proceso que una sola máquina virtual de 4 GPU, o una sola GPU K80, P40 o P100, respectivamente. Aunque las recomendaciones anteriores ofrecen una ruta de acceso directa, los usuarios de estos tamaños deben considerar la posibilidad de lograr sus objetivos de rendimiento con series de máquinas virtuales basadas en GPU NVIDIA V100 más eficaces, como las [series NC v3](./ncv3-series.md) y [ND v2](./ndv2-series.md), que normalmente permiten el mismo nivel de rendimiento de la carga de trabajo con costes más bajos y una mayor capacidad de administración al proporcionar un rendimiento considerablemente mayor por GPU y por máquina virtual antes de que se requieran configuraciones de varios nodos y GPU, respectivamente.
<br>

#### <a name="get-quota-for-the-target-vm-family"></a>Obtención de la cuota para la familia de máquinas virtuales de destino

Siga la guía para [solicitar un aumento de la cuota de vCPU por familia de máquinas virtuales.](../azure-portal/supportability/per-vm-quota-requests.md) Seleccione el tamaño de máquina virtual de destino que ha elegido para la migración.

#### <a name="resize-the-current-virtual-machine"></a>Cambio del tamaño de la máquina virtual actual

Puede [cambiar el tamaño de la máquina virtual mediante Azure Portal o PowerShell](./windows/resize-vm.md). También puede [cambiar el tamaño de la máquina virtual con la CLI de Azure](./linux/change-vm-size.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener una lista completa de los tamaños de máquina virtual habilitados para GPU, consulte [GPU: información general sobre el proceso acelerado](sizes-gpu.md).