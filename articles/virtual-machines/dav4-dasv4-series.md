---
title: Series Dav4 y Dasv4
description: Especificaciones de las máquinas virtuales de las series Dav4 y Dasv4.
author: mamccrea
ms.author: mamccrea
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.reviewer: jushiman
ms.openlocfilehash: 5bdd4ab5e7be84b817c9ce5e90820ab6801069f7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739008"
---
# <a name="dav4-and-dasv4-series"></a>Series Dav4 y Dasv4

Las series Dav4 y Dasv4 son tamaños nuevos que utilizan el procesador EPYC<sup>TM</sup> 7452 de 2,35 GHz de AMD en una configuración de varios subprocesos con una caché L3 de hasta 256 MB que dedica 8 MB de esa caché L3 a cada 8 núcleos. De este modo, aumentan las opciones que tiene el cliente para ejecutar sus cargas de trabajo de uso general. Las series Dav4 y Dasv4 tienen las mismas configuraciones de memoria y disco que las series D y Dsv3.

## <a name="dav4-series"></a>Serie Dav4

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): No compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): No compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible<br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
<br>


Los tamaños de la serie Dav4 se basan en el procesador EPYC<sup>TM</sup> 7452 de AMD de 2,35 Ghz que pueden alcanzar una frecuencia máxima incrementada de 3,35 Ghz. Los tamaños de la serie Dav4 ofrecen una combinación de vCPU, memoria y almacenamiento local adecuados para la mayoría de las cargas de trabajo de producción. El almacenamiento en disco de datos se factura de forma independiente a las máquinas virtuales. Para usar SSD Premium, use los tamaños de Dasv4. El precio y los medidores de facturación para los tamaños Dasv4 son los mismos que para la serie Dav4.

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal: IOPS / MBps de lectura / MBps de escritura | Nº máx. NIC | Ancho de banda de red esperado (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4<sup>1</sup> |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 | 2000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 | 4000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 | 8000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 | 10000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 | 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000 / 1000 / 500 | 8 | 24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000 / 1000 / 500 | 8 | 32000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000 / 1000 / 500 | 8 | 40000 |

<sup>1</sup> Las redes aceleradas solo se pueden aplicar a una única NIC. 

## <a name="dasv4-series"></a>Serie Dasv4

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible<br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
<br>

Los tamaños de la serie Dasv4 se basan en el procesador EPYC<sup>TM</sup> 7452 de AMD de 2,35 Ghz que pueden alcanzar una frecuencia máxima incrementada de 3,35 Ghz y usar SSD Premium. Los tamaños de la serie Dasv4 ofrecen una combinación de vCPU, memoria y almacenamiento local adecuados para la mayoría de las cargas de trabajo de producción.

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal y en caché: IOPS / MBps (tamaño de caché en GiB) | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS / MBps |  Rendimiento máximo del disco sin almacenamiento en la caché expandido: IOPS/MBps<sup>1</sup> | Nº máx. NIC | Ancho de banda de red esperado (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4<sup>2</sup>|2|8|16|4|4000/32 (50)|3200/48| 4000/200 | 2 | 2000 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96| 8000/200 |2 | 4000 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192| 16 000/400 |4 | 8000 |
| Standard_D16as_v4|16|64|128|32|32 000 / 255 (400)|25600/384| 32 000/800 |8 | 10000 |
| Standard_D32as_v4|32|128|256|32|64 000 / 510 (800)|51200/768| 64 000/1600 |8 | 16000 |
| Standard_D48as_v4|48|192|384|32|96000/1020 (1200)|76800/1148| 80000/2000 |8 | 24000 |
| Standard_D64as_v4|64|256|512|32|128000/1020 (1600)|80000/1200| 80000/2000 |8 | 32000 | 
| Standard_D96as_v4|96|384|768|32|192000/1020 (2400)|80000/1200| 80000/2000 |8 | 40000 |

<sup>1</sup> Las máquinas virtuales de la serie Dasv4 pueden [expandir](./disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.
<sup>2</sup> Las redes aceleradas solo se pueden aplicar a una única NIC. 

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Otros tamaños e información

- [Uso general](sizes-general.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Generaciones anteriores](sizes-previous-gen.md)

Calculadora de precios: [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)

Más información sobre los tipos de disco: [Tipos de disco](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.
