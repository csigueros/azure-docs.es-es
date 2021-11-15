---
title: Series Ddv4 y Ddsv4
description: Especificaciones para las máquinas virtuales de la serie Dv4, Ddv4, Dsv4 y Ddsv4
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: ba964b6bc48eaa1a5c0452709d63cf968a14c888
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459717"
---
# <a name="ddv4-and-ddsv4-series"></a>Series Ddv4 y Ddsv4

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las máquinas virtuales de las series Ddv4 y Ddsv4 se ejecutan en los procesadores Intel &reg;Xeon&reg; Platinum 8272CL (Cascade Lake) en una configuración con tecnología Hyper-Threading, lo que proporciona una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general. Presenta una velocidad de reloj turbo de todos los núcleos de 3,4 GHz, las tecnologías [Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) y las extensiones de vector avanzadas 512 de [Intel&reg; (Intel &reg;AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). También admiten [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Estos nuevos tamaños de máquina virtual tendrán un almacenamiento local del 50 % más grande, así como una mayor E/S por segundo del disco local para lectura y escritura, en comparación con los tamaños [Dv3/Dsv3](./dv3-dsv3-series.md) con [máquinas virtuales de segunda generación](./generation-2.md).

Los casos de uso de la serie D son las aplicaciones empresariales, las bases de datos relacionales, el almacenamiento en caché en memoria y el análisis.

## <a name="ddv4-series"></a>Serie Ddv4

Los tamaños de la serie Ddv4 se ejecutan en Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). La serie Ddv4 ofrece una combinación de vCPU, memoria y disco temporal para la mayoría de las cargas de trabajo de producción.

Los nuevos tamaños de máquinas virtuales Ddv4 incluyen almacenamiento SSD local rápido y más grande (hasta 2400 GiB) y están diseñados para aplicaciones que se benefician del almacenamiento local de baja latencia y alta velocidad, como aplicaciones que requieren lecturas y escrituras rápidas en el almacenamiento temporal o que necesitan almacenamiento temporal para cachés o archivos temporales. Puede conectar discos SSD estándar y almacenamiento de disco duro estándar a las máquinas virtuales Ddv4. El almacenamiento en disco de datos remotos se factura de forma independiente a las máquinas virtuales.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): No compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): No compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible<br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
<br> 

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal: IOPS/Mbps<sup>*</sup> | Nº máx. NIC|Ancho de banda de red esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4<sup>1</sup> | 2  | 8   | 75   | 4  | 9000/125    | 2 | 1000  |
| Standard_D4d_v4             | 4  | 16  | 150  | 8  | 19000/250   | 2 | 2000  |
| Standard_D8d_v4             | 8  | 32  | 300  | 16 | 38000/500   | 4 | 4000  |
| Standard_D16d_v4            | 16 | 64  | 600  | 32 | 75000/1000   | 8 | 8000  |
| Standard_D32d_v4            | 32 | 128 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_D48d_v4            | 48 | 192 | 1800 | 32 | 225000/3000 | 8 | 24000 |
| Standard_D64d_v4            | 64 | 256 | 2400 | 32 | 300000/4000 | 8 | 30000 |




<sup>*</sup> Estos valores de IOPS se pueden lograr mediante las [VM Gen2](generation-2.md)<br>
<sup>1</sup> Las redes aceleradas solo se pueden aplicar a una única NIC. 

## <a name="ddsv4-series"></a>Serie Ddsv4

La serie Ddsv4 se ejecuta en Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). La serie Ddsv4 ofrece una combinación de vCPU, memoria y disco temporal para la mayoría de las cargas de trabajo de producción.

Los nuevos tamaños de máquinas virtuales Ddsv4 incluyen almacenamiento SSD local rápido y más grande (hasta 2400 GiB) y están diseñados para aplicaciones que se benefician del almacenamiento local de baja latencia y alta velocidad, como aplicaciones que requieren lecturas y escrituras rápidas en el almacenamiento temporal o que necesitan almacenamiento temporal para cachés o archivos temporales. 

 > [!NOTE]
 >El precio y los medidores de facturación para los tamaños Ddsv4 son los mismos que para la serie Ddv4.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible<br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
<br> 

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal: IOPS/Mbps<sup>*</sup> | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps |  Rendimiento máximo del disco sin almacenamiento en la caché expandido: IOPS/MBps<sup>1</sup> | Nº máx. NIC|Ancho de banda de red esperado (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4<sup>2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 3200/48    | 4000/200   | 2 | 1000  |
| Standard_D4ds_v4             | 4  | 16  | 150  | 8  | 19000/250   | 6400/96    | 8000/200   | 2 | 2000  |
| Standard_D8ds_v4             | 8  | 32  | 300  | 16 | 38000/500   | 12800/192  | 16 000/400  | 4 | 4000  |
| Standard_D16ds_v4            | 16 | 64  | 600  | 32 | 85000/1000   | 25600/384  | 32 000/800  | 8 | 8000  |
| Standard_D32ds_v4            | 32 | 128 | 1200 | 32 | 150000/2000 | 51200/768  | 64 000/1600 | 8 | 16000 |
| Standard_D48ds_v4            | 48 | 192 | 1800 | 32 | 225000/3000 | 76800/1152 | 80000/2000 | 8 | 24000 |
| Standard_D64ds_v4            | 64 | 256 | 2400 | 32 | 300000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |

<sup>*</sup> Estos valores de IOPS se pueden lograr mediante las [VM Gen2](generation-2.md)<br>
<sup>1</sup> Las máquinas virtuales de la serie Ddsv4 pueden [expandir](./disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima durante hasta 30 minutos cada vez.<br>
<sup>2</sup> Las redes aceleradas solo se pueden aplicar a una única NIC. 

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
