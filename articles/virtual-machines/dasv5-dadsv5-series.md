---
title: 'Series Dasv5 y Dadsv5: Azure Virtual Machines'
description: Especificaciones de las máquinas virtuales de las series Dasv5 y Dadsv5.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: 2a4a48a66def1633aea5b85d9e51bdcf8bc1378e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478256"
---
# <a name="dasv5-and-dadsv5-series"></a>Series Dasv5 y Dadsv5

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las series Dasv5 y Dadsv5 usan el procesador de tercera generación EPYC<sup>TM</sup> 7763v de AMD en una configuración multiproceso con una caché L3 de hasta 256 MB, lo que aumenta las opciones de los clientes para ejecutar sus cargas de trabajo de uso general. Estas máquinas virtuales ofrecen una combinación de CPU virtuales y memoria para satisfacer los requisitos asociados a la mayoría de las cargas de trabajo empresariales, como bases de datos de pequeñas a medianas, servidores web de tráfico de bajo a medio, servidores de aplicaciones, etc.

## <a name="dasv5-series"></a>Serie Dasv5

Las máquinas virtuales de la serie Dasv5 usan procesadores de tercera generación EPYC<sup>TM</sup> 7763v de AMD que pueden lograr una frecuencia máxima aumentada de 3,7 GHz. Los tamaños de la serie Dasv5 ofrecen una combinación de CPU virtual y memoria para la mayoría de las cargas de trabajo de producción. Las nuevas máquinas virtuales sin disco local proporcionan una mejor propuesta de valor para las cargas de trabajo que no requieren disco temporal local.

> [!NOTE]
> Para ver las preguntas más frecuentes, consulte [Tamaños de máquina virtual de Azure sin disco temporal local](azure-vms-no-temp-disk.yml).

Las máquinas virtuales de la serie Dasv5 admiten los tipos de disco SSD estándar, HDD estándar y SSD prémium. También puede conectar almacenamiento en disco Ultra en función de su disponibilidad regional. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): Compatible <br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible <br>
[Migración en vivo](maintenance-and-updates.md): Compatible <br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible <br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2 <br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): No compatible <br><br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Rendimiento máximo del disco sin almacenamiento en la caché expandido: IOPS/MBps<sup>1</sup> | Nº máx. NIC | Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2as_v5  | 2  | 8   | Solo almacenamiento remoto | 4  | 3750/82    | 10 000/600   | 2 | 12500  |
| Standard_D4as_v5  | 4  | 16  | Solo almacenamiento remoto | 8  | 6400/144   | 20 000/600   | 2 | 12500  |
| Standard_D8as_v5  | 8  | 32  | Solo almacenamiento remoto | 16 | 12 800/200  | 20 000/600   | 4 | 12500  |
| Standard_D16as_v5 | 16 | 64  | Solo almacenamiento remoto | 32 | 25600/384  | 40 000/800   | 8 | 12500 |
| Standard_D32as_v5 | 32 | 128 | Solo almacenamiento remoto | 32 | 51200/768  | 80 000/1600  | 8 | 16000 |
| Standard_D48as_v5 | 48 | 192 | Solo almacenamiento remoto | 32 | 76800/1152 | 80000/2000  | 8 | 24000 |
| Standard_D64as_v5 | 64 | 256 | Solo almacenamiento remoto | 32 | 80000/1200 | 80000/2000  | 8 | 32000 |
| Standard_D96as_v5 | 96 | 384 | Solo almacenamiento remoto | 32 | 80 000/1600 | 80000/2000  | 8 | 40000 |


<sup>1</sup> Las máquinas virtuales de la serie Dasv5 pueden [expandir](disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.


## <a name="dadsv5-series"></a>Dadsv5-series

La serie Dadsv5 usa procesadores de tercera generación EPYC<sup>TM</sup> 7763v de AMD que pueden lograr una frecuencia máxima aumentada de 3,7 GHz. Los tamaños de la serie Dadsv5 ofrecen una combinación de CPU virtual, memoria y almacenamiento temporal adecuados para la mayoría de las cargas de trabajo de producción. Las nuevas máquinas virtuales tienen un almacenamiento local un 50 % superior, así como un mejor IOPS del disco local para la lectura y la escritura, en comparación con los tamaños [Dav4/Dasv4](dav4-dasv4-series.md) de las máquinas virtuales de [segunda generación](generation-2.md).

Las máquinas virtuales de la serie Dadsv5 admiten los tipos de disco SSD estándar, HDD estándar y SSD prémium. También puede conectar almacenamiento en disco Ultra en función de su disponibilidad regional. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).


[Premium Storage](premium-storage-performance.md): Compatible <br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible <br>
[Migración en vivo](maintenance-and-updates.md): Compatible <br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible <br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2 <br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br><br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal: IOPS/Mbps | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Rendimiento máximo del disco sin almacenamiento en la caché expandido: IOPS/MBps<sup>1</sup> | Nº máx. NIC | Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ads_v5  | 2  | 8   | 75   | 4  | 9000/125    | 3750/82    | 10 000/600  | 2 | 12500  |
| Standard_D4ads_v5  | 4  | 16  | 150  | 8  | 19 000/250   | 6400/144   | 20 000/600  | 2 | 12500  |
| Standard_D8ads_v5  | 8  | 32  | 300  | 16 | 38 000/500   | 12 800/200  | 20 000/600  | 4 | 12500  |
| Standard_D16ads_v5 | 16 | 64  | 600  | 32 | 75 000/1000  | 25600/384  | 40 000/800  | 8 | 12500 |
| Standard_D32ads_v5 | 32 | 128 | 1200 | 32 | 150 000/2000 | 51200/768  | 80 000/1000 | 8 | 16000 |
| Standard_D48ads_v5 | 48 | 192 | 1800 | 32 | 225 000/3000 | 76800/1152 | 80 000/200 | 8 | 24000 |
| Standard_D64ads_v5 | 64 | 256 | 2400 | 32 | 300 000/4000 | 80000/1200 | 80000/2000 | 8 | 32000 |
| Standard_D96ads_v5 | 96 | 384 | 3600 | 32 | 450 000/4000 | 80 000/1600 | 80000/2000 | 8 | 40000 |

* Estos valores de IOPS se pueden lograr mediante las máquinas virtuales de segunda generación.<br>
<sup>1</sup> Las máquinas virtuales de la serie Dadsv5 pueden [expandir](disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Otros tamaños e información

- [Uso general](sizes-general.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Generaciones anteriores](sizes-previous-gen.md)

Calculadora de precios: [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)

Para obtener más información sobre los tipos de discos, vea [¿Qué tipos de disco están disponibles en Azure?](disks-types.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.