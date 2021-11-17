---
title: 'Series Ddv5 y Ddsv5: Azure Virtual Machines'
description: Especificaciones de las VM de las series Ddv5 y Ddsv5.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 003b53a733e3b64c5ff5c822743f48df361a214b
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370032"
---
# <a name="ddv5-and-ddsv5-series"></a>Series Ddv5 y Ddsv5

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las máquinas virtuales de las series Ddv5 y Ddsv5 se ejecutan en los procesadores Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) de 3ª generación en una configuración con [Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), lo que supone una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general. Este nuevo procesador presenta una velocidad de reloj Turno de todos los núcleos de 3,5 GHz con [tecnología Intel&reg; Turbo Boost](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [extensiones de vector avanzadas Intel&reg; 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) e [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Estas máquinas virtuales ofrecen una combinación de CPU virtuales, memoria y almacenamiento temporal para satisfacer los requisitos asociados a la mayoría de las cargas de trabajo empresariales, como bases de datos de pequeñas a medianas, servidores web de tráfico de bajo a medio, servidores de aplicaciones, etc.


## <a name="ddv5-series"></a>Serie Ddv5
Las máquinas virtuales de la serie Ddv5 se ejecutan en el procesador Intel® Xeon® Platinum 8370C (Ice Lake) de 3ª generación que alcanza una velocidad de reloj turbo de todos los núcleos de hasta 3,5 GHz. Estas máquinas virtuales ofrecen hasta 96 CPU virtuales y 384 GiB de RAM, así como almacenamiento SSD local rápido hasta 3600 GiB. Las máquinas virtuales de la serie Ddv5 proporcionan una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general en comparación con la generación anterior (por ejemplo, mayor escalabilidad y una clase de CPU actualizada). Estas máquinas virtuales también cuentan con almacenamiento SSD local rápido y de gran tamaño (hasta 3600 GiB).

Las máquinas virtuales de la serie Ddv5 admiten los tipos de discos SSD estándar y HDD estándar. Para usar almacenamiento SSD prémium o Disco Ultra, seleccione máquinas virtuales de la serie Ddsv5. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).


[Premium Storage](premium-storage-performance.md): No compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): No compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): obligatorio <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
[Virtualización anidada](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md): compatible <br>
<br> 

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal y en caché: IOPS/MBps<sup>*</sup> | Nº máx. NIC|Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 2 | 12500 |
| Standard_D4d_v5               | 4  | 16  | 150  | 8  | 19 000/250   | 2 | 12500 |
| Standard_D8d_v5               | 8  | 32  | 300  | 16 | 38 000/500   | 4 | 12500 |
| Standard_D16d_v5              | 16 | 64  | 600  | 32 | 75 000/1000  | 8 | 12500 |
| Standard_D32d_v5              | 32 | 128 | 1200 | 32 | 150 000/2000 | 8 | 16000 |
| Standard_D48d_v5              | 48 | 192 | 1800 | 32 | 225 000/3000 | 8 | 24000 |
| Standard_D64d_v5              | 64 | 256 | 2400 | 32 | 300 000/4000 | 8 | 30000 |
| Standard_D96d_v5              | 96 | 384 | 3600 | 32 | 450 000/4000 | 8 | 35000 |

<sup>*</sup> Estos valores de IOPS se pueden garantizar mediante las [VM Gen2](generation-2.md)<br>
<sup>1</sup> Las redes aceleradas son necesarias y están activadas de forma predeterminada en todas las máquinas virtuales de la serie Ddv5.<br>
<sup>2</sup> Las redes aceleradas se pueden aplicar a dos NIC.

## <a name="ddsv5-series"></a>Serie Ddsv5

Las máquinas virtuales de la serie Ddsv5 se ejecutan en el procesador Intel® Xeon® Platinum 8370C (Ice Lake) de 3ª generación que alcanza una velocidad de reloj turbo de todos los núcleos de hasta 3,5 GHz.  Estas máquinas virtuales ofrecen hasta 96 CPU virtuales y 384 GiB de RAM, así como almacenamiento SSD local rápido hasta 3600 GiB. Las máquinas virtuales de la serie Ddsv5 proporcionan una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general en comparación con la generación anterior (por ejemplo, mayor escalabilidad y una clase de CPU actualizada).

Las máquinas virtuales de la serie Ddsv5 admiten los tipos de disco SSD estándar, HDD estándar y SSD prémium. También puede conectar almacenamiento en disco Ultra en función de su disponibilidad regional. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): obligatorio <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
[Virtualización anidada](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md): compatible <br>
<br> 


| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal y en caché: IOPS/MBps<sup>*</sup> | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Rendimiento máx. de disco de expansión sin caché: IOPS/MBps<sup>3</sup> | Nº máx. NIC | Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 3750/85     | 10 000/1200 | 2 | 12500 |
| Standard_D4ds_v5               | 4  | 16  | 150  | 8  | 19 000/250   | 6400/145    | 20 000/1200 | 2 | 12500 |
| Standard_D8ds_v5               | 8  | 32  | 300  | 16 | 38 000/500   | 12 800/290   | 20 000/1200 | 4 | 12500 |
| Standard_D16ds_v5              | 16 | 64  | 600  | 32 | 75 000/1000  | 25 600/600   | 40 000/1200 | 8 | 12500 |
| Standard_D32ds_v5              | 32 | 128 | 1200 | 32 | 150 000/2000 | 51 200/865   | 80000/2000 | 8 | 16000 |
| Standard_D48ds_v5              | 48 | 192 | 1800 | 32 | 225 000/3000 | 76 800/1315  | 80 000/3000 | 8 | 24000 |
| Standard_D64ds_v5              | 64 | 256 | 2400 | 32 | 375 000/4000 | 80 000/1735  | 80 000/3000 | 8 | 30000 |
| Standard_D96ds_v5              | 96 | 256 | 3600 | 32 | 450 000/4000 | 80 000/2600  | 80 000/4000 | 8 | 35000 |

<sup>*</sup> Estos valores de IOPS se pueden garantizar mediante las [VM Gen2](generation-2.md)<br>
<sup>1</sup> Las redes aceleradas son necesarias y están activadas de forma predeterminada en todas las máquinas virtuales de la serie Ddsv5.<br>
<sup>2</sup> Las redes aceleradas se pueden aplicar a dos NIC.<br>
<sup>3</sup> Las máquinas virtuales de la serie Ddsv5 pueden [expandir](disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.

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
