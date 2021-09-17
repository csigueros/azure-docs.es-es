---
title: Rendimiento y escalabilidad de los tamaños de VM de la serie HBv3
description: Obtenga información sobre el rendimiento y la escalabilidad de los tamaños de VM de la serie HBv3 en Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bb32a407813ba7e85f55fc39790fba81ca452c50
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693403"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Rendimiento de las máquinas virtuales de la serie HBv3

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las expectativas de rendimiento que usan micropuntos de referencia de HPC comunes son las siguientes:

| Carga de trabajo                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330-350 GB/s (~82-86 GB/s por NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) para el tamaño de máquina virtual de 120 núcleos               |
| Latencia y ancho de banda de RDMA                        | 1,2 microsegundos (1 byte), 192 GB/s (unidireccional)                                        |
| FIO en SSD de NVMe local (RAID0)                  | 7 GB/s de lecturas, 3 GB/s de escrituras; lecturas de IOPS de 186k, escrituras de IOPS de 201k |

## <a name="process-pinning"></a>Anclaje de procesos

El [anclaje de procesos](compiling-scaling-applications.md#process-pinning) funciona bien en las VM de la serie HBv3 porque exponemos el silicio subyacente tal cual está a la VM invitada. Se recomienda encarecidamente anclar los procesos para disfrutar de una coherencia y un rendimiento óptimos.

## <a name="mpi-latency"></a>Latencia de MPI

Se puede ejecutar la prueba de latencia de MPI del conjunto de pruebas de micropuntos de referencia OSU según se indica a continuación. Los scripts de ejemplo se encuentran en [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>Ancho de banda de MPI
La prueba de ancho de banda de MPI del conjunto de pruebas de micropuntos de referencia OSU se puede ejecutar según se indica a continuación. Los scripts de ejemplo se encuentran en [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
El [paquete Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tiene muchas pruebas de InfiniBand, como la de latencia (ib_send_lat) y la de ancho de banda (ib_send_bw). El siguiente es un ejemplo de comando.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Pasos siguientes
- Más información sobre el [escalado de aplicaciones MPI](compiling-scaling-applications.md).
- Revise los resultados de rendimiento y escalabilidad de las aplicaciones HPC en las VM HBv3 en el [artículo de TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843).
- En los [blogs de Azure Compute Community Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute), encontrará los anuncios más recientes, ejemplos de la carga de trabajo HPC y resultados de HPC.
- Si quiere una visión general de la arquitectura de la ejecución de cargas de trabajo de HPC, consulte [Informática de alto rendimiento (HPC) en Azure](/azure/architecture/topics/high-performance-computing/).
