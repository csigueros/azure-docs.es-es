---
title: 'Tamaños de las máquinas virtuales de Azure: HPC | Microsoft Docs'
description: Enumera los tamaños diferentes disponibles para las máquinas virtuales de informática de alto rendimiento en Azure. Se proporciona información sobre el número de unidades vCPU, discos de datos y NIC, así como sobre el rendimiento de almacenamiento y el ancho de banda de red para los tamaños de esta serie.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 1a82c13d0ad64f100f2fb8b826b1786afa81abac
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253372"
---
# <a name="high-performance-computing-vm-sizes"></a>Tamaños de VM para informática de alto rendimiento

> [!TIP]
> Pruebe la **[herramienta Selector de máquinas virtuales](https://aka.ms/vm-selector)** para buscar otros tamaños que se adapten mejor a la carga de trabajo.

La máquinas virtuales de la serie H de Azure están diseñada para ofrecer rendimiento de primer nivel, escalabilidad y rentabilidad para diversas cargas de trabajo de HPC del mundo real.

Las máquinas virtuales de la [serie HBv3](hbv3-series.md) están optimizadas para aplicaciones HPC como dinámica de fluidos, análisis de elementos finitos explícitos e implícitos, modelado meteorológico, procesamiento sísmico, simulación de depósitos y simulación de RTL. Las máquinas virtuales HBv3 cuentan con un máximo de 120 núcleos de CPU AMD EPYC™ de la serie 7003 (Milan), 448 GB de RAM y sin hyperthreading. Las máquinas virtuales de la serie HBv3 también proporcionan 350 GB/s de ancho de banda de memoria, hasta 32 MB de caché L3 por núcleo, hasta 7 GB/s de rendimiento de SSD de dispositivo en bloque y frecuencias de reloj de hasta 3,675 GHz. 

Todas las máquinas virtuales de la serie HBv3 incorporan InfiniBand HDR de 200 Gb/s de redes NVIDIA para permitir cargas de trabajo MPI de gran tamaño. Estas máquinas virtuales están conectadas en un árbol FAT sin bloqueos para un rendimiento de RDMA optimizado y coherente. El tejido InfiniBand de HDR también admite el enrutamiento adaptativo y el transporte conectado dinámico (DCT, además de los transportes estándar RC y UD). Estas características mejoran el rendimiento, la escalabilidad y la coherencia de las aplicaciones, y se recomienda su uso.

Las máquinas virtuales de la [serie HBv2](hbv2-series.md) están optimizadas para aplicaciones controladas por el ancho de banda de memoria, como la dinámica de fluidos, el análisis de elementos finitos y la simulación de yacimientos. Las máquinas virtuales HBv2 cuentan con 120 núcleos de procesador AMD EPYC 7742, 4 GB de RAM por núcleo de CPU y sin multithreading simultáneo. Cada máquina virtual de HBv2 proporciona hasta 340 GB/s de ancho de banda de memoria y hasta 4 teraFLOPS de proceso FP64.

Las máquinas virtuales de la serie HBv2 cuentan con la característica Mellanox HDR InfiniBand a 200 GB/s, mientras que las máquinas virtuales de las series HB y HC cuentan con la característica Mellanox EDR InfiniBand a 100 GB/s. Cada uno de estos tipos de máquinas virtuales están conectados en un árbol FAT sin bloqueos para un rendimiento de RDMA optimizado y coherente. Las máquinas virtuales HBv2 admiten el enrutamiento adaptable y el transporte conectado dinámico (DCT, además de los transportes estándar RC y UD). Estas características mejoran el rendimiento, la escalabilidad y la coherencia de las aplicaciones, y se recomienda su uso.

Las máquinas virtuales de la [serie HB](hb-series.md) están optimizadas para aplicaciones que funcionan con ancho de banda de la memoria, como la dinámica de fluidos, el análisis explícito de elementos finitos y los modelos de clima. Las máquinas virtuales HB cuentan con 60 núcleos de procesador AMD EPYC 7551, 4 GB de RAM por núcleo de CPU y no tienen hyperthreading. La plataforma AMD EPYC proporciona un ancho de banda de memoria de más de 260 GB/s.

Las máquinas virtuales de la [serie HC](hc-series.md) están optimizadas para aplicaciones basadas en cálculos intensivos, como el análisis implícito de elementos finitos, la dinámica molecular y la química computacional. Las máquinas virtuales HC cuentan con 44 núcleos de procesador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo de CPU y no tienen hyperthreading. La plataforma Intel Xeon Platinum admite el rico ecosistema de herramientas de software de Intel, como la biblioteca Intel Math Kernel Library.

Las máquinas virtuales de la [serie H](h-series.md) están optimizadas para aplicaciones basadas en frecuencias altas de CPU o requisitos de gran cantidad de memoria por núcleo. Las máquinas virtuales de la serie H cuentan con 8 o 16 núcleos de procesador Intel Xeon E5 2667 v3, 7 o 14 GB de RAM por núcleo de CPU y no tienen hyperthreading. Las máquinas de la serie H cuentan con una interconexión Mellanox FDR InfiniBand de 56 Gb/s en una configuración de árbol de FAT sin bloqueo para proporcionar un rendimiento de RDMA consistente. Las máquinas virtuales de la serie H admiten Intel MPI 5.x y MS-MPI.

> [!NOTE]
> Todas las máquinas virtuales de la serie HBv3, HBv2, HB y HC tienen acceso exclusivo a los servidores físicos. Solo hay una máquina virtual por cada servidor físico y no hay ningún multiinquilino compartido con otras máquinas virtuales para estos tamaños de máquina virtual.

> [!NOTE]
> Las [máquinas virtuales A8-A11](./sizes-previous-gen.md#a-series---compute-intensive-instances) se retiran a partir de 3/2021. Ya no se pueden implementar nuevas máquinas virtuales de estos tamaños. Si tiene máquinas virtuales existentes, consulte notificaciones por correo electrónico para conocer los pasos siguientes, como la migración a otros tamaños de máquina virtual en la [Guía de migración de HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instancias compatibles con RDMA

La mayoría de los tamaños de VM para HPC incluye una interfaz de red para la conectividad de acceso directo a memoria remota (RDMA). Los tamaños seleccionados de la [serie N](./nc-series.md) designados con "r" también son compatibles con RDMA. Esta interfaz se agrega a la interfaz de red estándar de Azure Ethernet disponible en los otros tamaños de máquina virtual.

Esta interfaz secundaria permite que las instancias compatibles con RDMA se comuniquen través de una red InfiniBand (IB), que funciona a velocidades HDR en la serie HBv3, HBv2, EDR en las series HB, HC y NDv2, a velocidades FDR en las series H16r, H16mr y en otras máquinas virtuales de la serie N compatibles con RDMA. Estas funcionalidades RDMA pueden mejorar la escalabilidad y el rendimiento basado en las aplicaciones de la Interfaz de paso de mensajes (MPI).

> [!NOTE]
> **Compatibilidad con SR-IOV**: En Azure HPC, hay dos clases de máquinas virtuales en función de si están habilitadas para SR-IOV de InfiniBand. Actualmente, casi todas las máquinas virtuales de la generación más reciente, compatibles con RDMA o InfiniBand en Azure, están habilitadas para SR-IOV, excepto H16r, H16mr y NC24r.
> RDMA solo se habilita a través de la red InfiniBand (IB) y es compatible con todas las máquinas virtuales que admiten RDMA.
> Solo se admite IP sobre IB en máquinas virtuales habilitadas para SR-IOV.
> RDMA no se habilita a través de la red Ethernet.

- **Sistema operativo**: habitualmente se usan distribuciones de Linux como CentOS, RHEL, Ubuntu y SUSE. En todas las máquinas virtuales de la serie HPC se admite Windows Server 2016 y versiones más recientes. Windows Server 2012 R2 y Windows Server 2012 también se admiten en las máquinas virtuales no habilitadas para SR-IOV. Tenga en cuenta que [Windows Server 2012 R2 no se admite en HBv2 en adelante como máquinas virtuales con más de 64 núcleos (virtuales o físicos)](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Consulte [Imágenes de máquina virtual](./workloads/hpc/configure.md) para obtener una lista de imágenes de máquina virtual compatibles en Marketplace y cómo se pueden configurar de forma adecuada. En las páginas con tamaño de máquina virtual correspondientes también se muestra la compatibilidad con la pila de software.

- **InfiniBand y controladores**: en las máquinas virtuales compatibles con InfiniBand, se necesitan los controladores adecuados para habilitar RDMA. Consulte [Imágenes de máquina virtual](./workloads/hpc/configure.md) para obtener una lista de imágenes de máquina virtual compatibles en Marketplace y cómo se pueden configurar de forma adecuada. Consulte también [habilitación de InfiniBand](./workloads/hpc/enable-infiniband.md) para obtener información acerca de las extensiones de máquina virtual o la instalación manual de los controladores Infiniband.

- **MPI**: los tamaños de máquina virtual habilitados para SR-IOV en Azure permiten que se use prácticamente cualquier tipo de MPI con Mellanox OFED. En las máquinas virtuales que no están habilitadas para SR-IOV, las implementaciones de MPI compatibles usan la interfaz Microsoft Network Direct (ND) para comunicarse entre máquinas virtuales. Por lo tanto, solo se admiten las versiones de Intel MPI 5.x y Microsoft MPI (MS-MPI) 2012 R2 o posteriores. Las versiones posteriores de la biblioteca en tiempo de ejecución de MPI pueden ser o no compatibles con los controladores de Azure RDMA. Consulte [Configuración de MPI para HPC](./workloads/hpc/setup-mpi.md) para obtener más información sobre cómo configurar MPI en máquinas virtuales de HPC en Azure.

  > [!NOTE]
  > **Espacio de direcciones de la red RDMA**: la red RDMA en Azure reserva el espacio de direcciones 172.16.0.0/16. Para ejecutar aplicaciones MPI en instancias implementadas en una red virtual Azure, asegúrese de que el espacio de direcciones de la red virtual no se superpone a la red RDMA.

## <a name="cluster-configuration-options"></a>Opciones de configuración del clúster

Azure ofrece varias opciones para crear clústeres de máquinas virtuales de HPC que se pueden comunicar con la red RDMA, incluidos: 

- **Máquinas virtuales**: implemente las máquinas virtuales de HPC compatibles con RDMA en el mismo conjunto de escalado o de disponibilidad (cuando use el modelo de implementación de Azure Resource Manager). Si usa el modelo de implementación clásica, implemente las máquinas virtuales en el mismo servicio en la nube.

- **Conjuntos de escalado de máquinas virtuales**: en un conjunto de escalado de máquinas virtuales, asegúrese de limitar la implementación a un único grupo de selección de ubicación para la comunicación InfiniBand dentro del conjunto de escalado. Por ejemplo, en una plantilla de Resource Manager, establezca la propiedad `singlePlacementGroup` en `true`. Tenga en cuenta que el tamaño de conjunto de escalado máximo que se puede usar con `singlePlacementGroup=true` está limitado a 100 máquinas virtuales de manera predeterminada. Si los requisitos de escalado de su trabajo de HPC son superiores a 100 máquinas virtuales en un único inquilino, puede solicitar un aumento. Para ello, [realice una solicitud de soporte técnico al cliente en línea](../azure-portal/supportability/how-to-create-azure-support-request.md) sin cargo alguno. El número máximo de máquinas virtuales en un único conjunto de escalado se puede aumentar hasta 300. Tenga en cuenta que al implementar máquinas virtuales con conjuntos de disponibilidad, el límite máximo es de 200 máquinas virtuales por conjunto de disponibilidad.

  > [!NOTE]
  > **MPI entre las máquinas virtuales**: si la característica de RDMA (por ejemplo, para usar la comunicación de MPI) es necesaria entre las máquinas virtuales (VM), asegúrese de que las VM estén en el mismo conjunto de escalado de máquinas virtuales o conjunto de disponibilidad.

- **Azure CycleCloud**: cree un clúster de HPC en [Azure CycleCloud](/azure/cyclecloud/) para ejecutar trabajos MPI.

- **Azure Batch**: cree un grupo de [Azure Batch](../batch/index.yml) para ejecutar cargas de trabajo MPI. Para usar instancias de proceso intensivo para ejecutar aplicaciones MPI con Azure Batch, consulte [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack**: [HPC Pack](/powershell/high-performance-computing/overview) incluye un entorno de tiempo de ejecución para MS-MPI que usa la red RDMA de Azure cuando se implementa en máquinas virtuales Linux compatibles con RDMA. Para obtener ejemplos de implementación, consulte [Configuración de un clúster de RDMA de Linux con HPC Pack para ejecutar aplicaciones MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Consideraciones de la implementación

- **Suscripción de Azure**: para implementar más que algunas instancias de proceso intensivo, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.

- **Precios y disponibilidad**: compruebe la [disponibilidad](https://azure.microsoft.com/global-infrastructure/services/) y los [precios de las máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en las regiones de Azure.

- **Cuota de núcleos**: quizás tenga que aumentar la cuota de núcleos de su suscripción de Azure partiendo del valor predeterminado. La suscripción también podría limitar el número de núcleos que se pueden implementar en ciertas familias de tamaño de máquina virtual, como la serie H. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../azure-portal/supportability/how-to-create-azure-support-request.md) sin cargo alguno. (Los límites predeterminados pueden variar según la categoría de suscripción).

  > [!NOTE]
  > Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure. Las cuotas de Azure son límites de crédito, no garantías de capacidad. Independientemente de la cuota, solamente se le cobrarán los núcleos que use.
  
- **Red virtual** : no se necesita una [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) de Azure para usar instancias de proceso intensivo. Sin embargo, para muchas implementaciones necesita al menos una red virtual de Azure basada en la nube o una conexión de sitio a sitio si necesita acceder a recursos locales. Si es necesario, cree una red virtual para implementar las instancias. No se admite la adición de máquinas virtuales de proceso intensivo a las redes virtuales de grupos de afinidad.

- **Cambio de tamaño**: debido a su hardware especializado, solo se puede cambiar el tamaño de las instancias de proceso intensivo dentro de la misma familia de tamaño (serie H o serie N). Por ejemplo, una máquina virtual de la serie H solo se puede cambiar de un tamaño de serie H a otro. Es posible que tenga que tener en cuenta las consideraciones adicionales sobre la compatibilidad del controlador de InfiniBand y los discos de NVMe para determinadas máquinas virtuales.


## <a name="other-sizes"></a>Otros tamaños

- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)
- [Generaciones anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [configuración de VM](./workloads/hpc/configure.md), la [habilitación de InfiniBand](./workloads/hpc/enable-infiniband.md), la [configuración de MPI](./workloads/hpc/setup-mpi.md) y la optimización de las aplicaciones HPC para Azure en [cargas de trabajo de HPC](./workloads/hpc/overview.md).
- Revise la [información general de la serie HBv3](./workloads/hpc/hbv3-series-overview.md) y la [información general de la serie HC](./workloads/hpc/hc-series-overview.md).
- En los [blogs de Azure Compute Community Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute), encontrará los anuncios más recientes, ejemplos de la carga de trabajo HPC y resultados de HPC.
- Si desea una visión general de la arquitectura de la ejecución de cargas de trabajo de HPC, consulte [Informática de alto rendimiento (HPC) en Azure](/azure/architecture/topics/high-performance-computing/).
