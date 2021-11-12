---
title: 'Series Edv5 y Edsv5: Azure Virtual Machines'
description: Especificaciones de las VM de las series Edv5 y Edsv5.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 8a2fac1ded1cd17d9e47500eda06e58ca21fc6e7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850059"
---
# <a name="edv5-and-edsv5-series"></a>Series Edv5 y Edsv5

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las máquinas virtuales de las series Edv5 y Edsv5 se ejecutan en los procesadores Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) de 3ª generación en una configuración con [Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), lo que supone una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general. Este nuevo procesador presenta una velocidad de reloj Turno de todos los núcleos de 3,5 GHz con [tecnología Intel&reg; Turbo Boost](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [extensiones de vector avanzadas Intel&reg; 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) e [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Con hasta 672 GiB de RAM, estas máquinas virtuales son ideales para aplicaciones empresariales con un uso intensivo de memoria, servidores de bases de datos relacionales y cargas de trabajo de análisis en memoria. Estas VM también cuentan con almacenamiento SSD local rápido y de gran tamaño (hasta 3900 GiB).

## <a name="edv5-series"></a>Serie Edv5

Las máquinas virtuales de la serie Edv5 se ejecutan en el procesador Intel® Xeon® Platinum 8370C (Ice Lake) de 3ª generación que alcanza una velocidad de reloj turbo de todos los núcleos de hasta 3,5 GHz.  Estas máquinas virtuales ofrecen hasta 104 CPU virtuales y 672 GiB de RAM, así como almacenamiento SSD local rápido hasta 3800 GiB. Las máquinas virtuales de la serie Edv5 son ideales para aplicaciones empresariales de uso intensivo de memoria y aplicaciones que se benefician del almacenamiento local de baja latencia y alta velocidad.

Las máquinas virtuales de la serie Edv5 admiten los tipos de discos SSD estándar y HDD estándar. Para usar almacenamiento SSD prémium o Disco Ultra, seleccione Máquinas virtuales de la serie Edsv5. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): No compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): No compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): obligatorio <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): No compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal y en caché: IOPS/MBps<sup>*</sup>  | Nº máx. NIC|Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v5<sup>1,2</sup>  | 2   | 16  | 75   | 4  | 9000/125    | 2 | 12500 |
| Standard_E4d_v5                | 4   | 32  | 150  | 8  | 19000/250   | 2 | 12500 |
| Standard_E8d_v5                | 8   | 64  | 300  | 16 | 38000/500   | 4 | 12500 |
| Standard_E16d_v5               | 16  | 128 | 600  | 32 | 75000/1000  | 8 | 12500 |
| Standard_E20d_v5               | 20  | 160 | 750  | 32 | 94000/1250  | 8 | 12500  |
| Standard_E32d_v5               | 32  | 256 | 1200 | 32 | 150000/2000 | 8 | 16000  |
| Standard_E48d_v5               | 48  | 384 | 1800 | 32 | 225000/3000 | 8 | 24000  |
| Standard_E64d_v5               | 64  | 512 | 2400 | 32 | 300000/4000 | 8 | 30000  |
| Standard_E96d_v5               | 96  | 672 | 3600 | 32 | 450000/4000 | 8 | 35000  |
| Standard_E104id_v5<sup>3</sup> | 104 | 672 | 3800 | 64 | 450000/4000 | 8 | 100000 |

<sup>*</sup> Estos valores de IOPS se pueden garantizar mediante las [VM Gen2](generation-2.md)<br>
<sup>1</sup> Las redes aceleradas son necesarias y están activadas de forma predeterminada en todas las máquinas virtuales de la serie Edv5.<br>
<sup>2</sup> Las redes aceleradas se pueden aplicar a dos NIC.<br>
<sup>3</sup> La instancia está [aislada](../security/fundamentals/isolation-choices.md#compute-isolation) en el hardware dedicado a un solo cliente.


## <a name="edsv5-series"></a>Serie Edsv5

Las máquinas virtuales de la serie Edsv5 se ejecutan en el procesador Intel® Xeon® Platinum 8370C (Ice Lake) de 3ª generación que alcanza una velocidad de reloj turbo de todos los núcleos de hasta 3,5 GHz.  Estas máquinas virtuales ofrecen hasta 104 CPU virtuales y 672 GiB de RAM, así como almacenamiento SSD local rápido hasta 3800 GiB. Las máquinas virtuales de la serie Edsv5 son ideales para aplicaciones empresariales de uso intensivo de memoria y aplicaciones que se benefician del almacenamiento local de baja latencia y alta velocidad.

Las máquinas virtuales de la serie Edsv5 admiten los tipos de discos SSD estándar y HDD estándar. Puede conectar almacenamiento de discos SSD estándar, HDD estándar y SSD prémium a estas VM. También puede conectar almacenamiento en disco Ultra en función de su disponibilidad regional. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): obligatorio <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal y en caché: IOPS/MBps<sup>*</sup> | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Rendimiento máx. de disco de expansión sin caché: IOPS/MBps<sup>5</sup> | Nº máx. NIC | Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v5<sup>1,2</sup>  | 2   | 16  | 75   | 4  | 9000/125    | 3750/85      | 10000/1200 | 2 | 12500 |
| Standard_E4ds_v5                | 4   | 32  | 150  | 8  | 19000/250   | 6400/145     | 20000/1200 | 2 | 12500 |
| Standard_E8ds_v5                | 8   | 64  | 300  | 16 | 38000/500   | 12800/290    | 20000/1200 | 4 | 12500 |
| Standard_E16ds_v5               | 16  | 128 | 600  | 32 | 75000/1000  | 25600/600    | 40000/1200 | 8 | 12500 |
| Standard_E20ds_v5               | 20  | 160 | 750  | 32 | 94000/1250  | 32000/750    | 64 000/1600 | 8 | 12500  |
| Standard_E32ds_v5               | 32  | 256 | 1200 | 32 | 150000/2000 | 51200/865    | 80000/2000 | 8 | 16000  |
| Standard_E48ds_v5               | 48  | 384 | 1800 | 32 | 225000/3000 | 76800/1315   | 80000/3000 | 8 | 24000  |
| Standard_E64ds_v5               | 64  | 512 | 2400 | 32 | 375000/4000 | 80000/1735   | 80000/3000 | 8 | 30000  |
| Standard_E96ds_v5<sup>3</sup>   | 96  | 672 | 3600 | 32 | 450000/4000 | 80000/2600   | 80000/4000 | 8 | 35000  |
| Standard_E104ids_v5<sup>4</sup> | 104 | 672 | 3800 | 64 | 450000/4000 | 120000/4000  | 120000/4000 | 8 | 100000 |

<sup>*</sup> Estos valores de IOPS se pueden garantizar mediante las [VM Gen2](generation-2.md)<br>
<sup>1</sup> Las redes aceleradas son necesarias y están activadas de forma predeterminada en todas las máquinas virtuales de la serie Edsv5.<br>
<sup>2</sup> Las redes aceleradas se pueden aplicar a dos NIC.<br>
<sup>3</sup> Tamaños de [núcleos restringidos](constrained-vcpu.md) disponibles.<br>
<sup>4</sup> La instancia está [aislada](../security/fundamentals/isolation-choices.md#compute-isolation) en el hardware dedicado a un solo cliente.<br>
<sup>5</sup> Las máquinas virtuales de la serie Edsv5 pueden [expandir](disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.

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
