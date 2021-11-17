---
title: Series Edv4 y Edsv4
description: Especificaciones para las VM de las series Ev4, Edv4, Esv4 and Edsv4.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: f4138c2b2835ede93e0a33693f49ed75ee6bb499
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449096"
---
# <a name="edv4-and-edsv4-series"></a>Series Edv4 y Edsv4

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las series Edv4 y Edsv4 se ejecutan en procesadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) en una configuración con subprocesamiento múltiple, son ideales para distintas aplicaciones empresariales con un uso intensivo de memoria y cuentan con hasta 504 GiB de RAM, tecnología [Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), tecnología [Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). También admiten [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Estos nuevos tamaños de VM tendrán un almacenamiento local un 50 % más grande, así como una mayor E/S por segundo del disco local para lectura y escritura, en comparación con los tamaños [Ev3/Esv3](./ev3-esv3-series.md) con [VM de segunda generación](./generation-2.md). Cuenta con una velocidad de reloj en todos los núcleos de 3,4 GHz. 

## <a name="edv4-series"></a>Serie Edv4

Los tamaños de la serie Edv4 se ejecutan en procesadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Los tamaños de máquina virtual Edv4 cuentan con hasta 504 GiB de RAM, además de almacenamiento SSD local rápido y de gran tamaño (hasta 2400 GiB). Estas máquinas virtuales son ideales para aplicaciones empresariales de uso intensivo de memoria y aplicaciones que se benefician del almacenamiento local de baja latencia y alta velocidad. Puede conectar almacenamiento de discos SSD y HDD estándar a las VM Edv4. 

[ACU](acu.md): 195 - 210<br>
[Premium Storage](premium-storage-performance.md): No compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): No compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible<sup>1</sup> <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): No compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal: IOPS/Mbps<sup>*</sup>  | Nº máx. NIC|Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4<sup>1</sup>  | 2  | 16  | 75   | 4  | 9000/125    | 2 | 1000  |
| Standard_E4d_v4              | 4  | 32  | 150  | 8  | 19000/250   | 2 | 2000  |
| Standard_E8d_v4              | 8  | 64  | 300  | 16 | 38000/500   | 4 | 4000  |
| Standard_E16d_v4             | 16 | 128 | 600  | 32 | 75000/1000   | 8 | 8000  |
| Standard_E20d_v4             | 20 | 160 | 750  | 32 | 94000/1250  | 8 | 10000 |
| Standard_E32d_v4             | 32 | 256 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_E48d_v4             | 48 | 384 | 1800 | 32 | 225000/3000 | 8 | 24000 |
| Standard_E64d_v4             | 64 | 504 | 2400 | 32 | 300000/4000 | 8 | 30000 |

<sup>*</sup> Estos valores de IOPs se pueden conseguir utilizando [VM de Gen2](generation-2.md)
<sup>1</sup> Las redes acelerada solo pueden aplicarse a una única NIC. <br>

## <a name="edsv4-series"></a>Serie Edsv4

Los tamaños de la serie Edsv4 se ejecutan en procesadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Los tamaños de máquina virtual Edsv4 cuentan con hasta 504 GiB de RAM, además de almacenamiento SSD local, rápido y de gran tamaño (hasta 2400 GiB). Estas máquinas virtuales son ideales para aplicaciones empresariales de uso intensivo de memoria y aplicaciones que se benefician del almacenamiento local de baja latencia y alta velocidad.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal: IOPS/Mbps<sup>*</sup> | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Rendimiento máximo del disco sin almacenamiento en la caché expandido: IOPS/MBps<sup>1</sup> | Nº máx. NIC|Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4<sup>4</sup>    | 2  | 16  | 75   | 4  | 9000/125    | 3200/48    | 4000/200   | 2 | 1000  |
| Standard_E4ds_v4                | 4  | 32  | 150  | 8  | 19000/250   | 6400/96    | 8000/200   | 2 | 2000  |
| Standard_E8ds_v4                | 8  | 64  | 300  | 16 | 38000/500   | 12800/192  | 16 000/400  | 4 | 4000  |
| Standard_E16ds_v4               | 16 | 128 | 600  | 32 | 75000/1000   | 25600/384  | 32 000/800  | 8 | 8000  |
| Standard_E20ds_v4               | 20 | 160 | 750  | 32 | 94000/1250  | 32000/480  | 40000/1000 | 8 | 10000 |
| Standard_E32ds_v4               | 32 | 256 | 1200 | 32 | 150000/2000 | 51200/768  | 64 000/1600 | 8 | 16000 |
| Standard_E48ds_v4               | 48 | 384 | 1800 | 32 | 225000/3000 | 76800/1152 | 80000/2000 | 8 | 24000 |
| Standard_E64ds_v4 <sup>2</sup>  | 64 | 504 | 2400 | 32 | 300000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |
| Standard_E80ids_v4 <sup>3</sup> | 80 | 504 | 2400 | 64 | 375000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |

<sup>*</sup> Estos valores de IOPS se pueden garantizar mediante las [VM Gen2](generation-2.md)<br>
<sup>1</sup> Las máquinas virtuales de la serie Edsv4 pueden [expandir](./disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.<br>
<sup>2</sup> [Tamaños de núcleo restringidos disponibles](./constrained-vcpu.md).<br>
<sup>3</sup> La instancia está aislada en el hardware dedicado a un solo cliente.<br>
<sup>4</sup> Las redes aceleradas solo se pueden aplicar a una única NIC. 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Otros tamaños e información

- [Uso general](sizes-general.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Generaciones anteriores](sizes-previous-gen.md)

Calculadora de precios: [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)

Más información sobre los tipos de disco: [Tipos de disco](./disks-types.md#ultra-disks)


## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.
