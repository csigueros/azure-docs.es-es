---
title: Rendimiento del tamaño de las máquinas virtuales de la serie HBv2
description: Conozca los resultado de la prueba de rendimiento de los distintos tamaños de las máquinas virtuales de la serie HBv2 en Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: a15aaece5916ac9e9ff6129754b4f932878ac707
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698640"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>Tamaños de las máquinas virtuales de la serie HBv2

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Se han realizado varias pruebas de rendimiento en las máquinas virtuales de los tamaños de la [serie HBv2](../../hbv2-series.md). Estos son algunos de los resultados de esta prueba.


| Carga de trabajo                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 350 GB/s (21-23 GB/s por CCX)                                     |
| High-Performance Linpack (HPL)                  | 4 TeraFLOPS (Rpeak, FP64), 8 TeraFLOPS (Rmax, FP32)               |
| Latencia y ancho de banda de RDMA                        | 1,2 microsegundos; 190 Gb/s                                        |
| FIO en SSD NVMe local                           | 2,7 GB/s lecturas, 1,1 GB/s escrituras; lecturas de IOPS de 102 k, 115 escrituras de IOPS |
| IOR en 8 * SSD Premium de Azure (P40 Managed Disks, RAID0)**  | 1,3 GB/s lecturas, 2,5 GB/escrituras; lecturas de IOPS de 101 k, escrituras de IOPS de 105 k |


## <a name="mpi-latency"></a>Latencia de MPI

Se ejecuta la prueba de latencia de MPI del conjunto de pruebas OSU de microrrealización. Los scripts de ejemplo se encuentran en [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="Latencia de MPI en HB de Azure.":::


## <a name="mpi-bandwidth"></a>Ancho de banda de MPI

Se ejecuta la prueba de ancho de banda de MPI del conjunto de pruebas OSU de microrrealización. Los scripts de ejemplo se encuentran en [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Ancho de banda de MPI en HB de Azure.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

El [paquete Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tiene muchas pruebas de InfiniBand, como la de latencia (ib_send_lat) y la de ancho de banda (ib_send_bw). El siguiente es un ejemplo de comando. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Pasos siguientes

- En los [blogs de Azure Compute Community Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute), encontrará los anuncios más recientes, ejemplos de la carga de trabajo HPC y resultados de HPC.
- Si quiere una visión general de la arquitectura de la ejecución de cargas de trabajo de HPC, consulte [Informática de alto rendimiento (HPC) en Azure](/azure/architecture/topics/high-performance-computing/).