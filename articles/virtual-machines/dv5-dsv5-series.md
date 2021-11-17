---
title: 'Series Dv5 y Dsv5: Azure Virtual Machines'
description: Especificaciones de las VM de las series Dv5 y Dsv5.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 0bdd2c517a6bfef059ad89d7c3cbf1a825aba526
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866795"
---
# <a name="dv5-and-dsv5-series"></a>Series Dv5 y Dsv5

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las máquinas virtuales de las series Dv5 y Dsv5 se ejecutan en los procesadores Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) de 3ª generación en una configuración con [Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), lo que supone una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general. Este nuevo procesador presenta una velocidad de reloj Turno de todos los núcleos de 3,5 GHz con [tecnología Intel&reg; Turbo Boost](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [extensiones de vector avanzadas Intel&reg; 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) e [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Estas máquinas virtuales ofrecen una combinación de CPU virtuales y memoria para satisfacer los requisitos asociados a la mayoría de las cargas de trabajo empresariales, como bases de datos de pequeñas a medianas, servidores web de tráfico de bajo a medio, servidores de aplicaciones, etc. Las series Dv5 y Dsv5 proporcionan una mejor propuesta de valor para las cargas de trabajo que no requieren disco temporal local. Para obtener información sobre máquinas virtuales similares con disco local, vea [Máquinas virtuales de las series Ddv5 y Ddsv5](ddv5-ddsv5-series.md).

> [!NOTE]
> Para ver las preguntas más frecuentes, consulte [Tamaños de máquina virtual de Azure sin disco temporal local](azure-vms-no-temp-disk.yml).

## <a name="dv5-series"></a>Serie Dv5

Las máquinas virtuales de la serie Dv5 se ejecutan en el procesador Intel® Xeon® Platinum 8370C (Ice Lake) de 3ª generación que alcanza una velocidad de reloj turbo de todos los núcleos de hasta 3,5 GHz.  Estas máquinas virtuales ofrecen hasta 96 vCPU y 384 GiB de RAM.  Las máquinas virtuales de la serie Dv5 proporcionan una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general en comparación con la generación anterior (por ejemplo, mayor escalabilidad y una clase de CPU actualizada).

Las máquinas virtuales de la serie Dv5 no tienen ningún almacenamiento temporal, lo que reduce el precio de entrada.  Puede conectar almacenamiento de discos SSD estándar y HDD estándar a estas máquinas virtuales. Para usar almacenamiento SSD prémium o Disco Ultra, seleccione máquinas virtuales de la serie Dsv5. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): No compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): No compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): obligatorio <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): No compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Nº máx. NIC|Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v5<sup>1, 2</sup> | 2  | 8   | Solo almacenamiento remoto | 4  | 2 | 12500 |
| Standard_D4_v5                | 4  | 16  | Solo almacenamiento remoto | 8  | 2 | 12500 |
| Standard_D8_v5                | 8  | 32  | Solo almacenamiento remoto | 16 | 4 | 12500 |
| Standard_D16_v5               | 16 | 64  | Solo almacenamiento remoto | 32 | 8 | 12500 |
| Standard_D32_v5               | 32 | 128 | Solo almacenamiento remoto | 32 | 8 | 16000 |
| Standard_D48_v5               | 48 | 192 | Solo almacenamiento remoto | 32 | 8 | 24000 |
| Standard_D64_v5               | 64 | 256 | Solo almacenamiento remoto | 32 | 8 | 30000 |
| Standard_D96_v5               | 96 | 384 | Solo almacenamiento remoto | 32 | 8 | 35000 |

<sup>1</sup> Las redes aceleradas son necesarias y están activadas de forma predeterminada en todas las máquinas virtuales de la serie Dv5.<br>
<sup>2</sup> Las redes aceleradas se pueden aplicar a dos NIC.

## <a name="dsv5-series"></a>Serie Dsv5

Las máquinas virtuales de la serie Dsv5 se ejecutan en el procesador Intel® Xeon® Platinum 8370C (Ice Lake) de 3ª generación que alcanza una velocidad de reloj turbo de todos los núcleos de hasta 3,5 GHz.  Estas máquinas virtuales ofrecen hasta 96 vCPU y 384 GiB de RAM.  Las máquinas virtuales de la serie Dsv5 proporcionan una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general en comparación con la generación anterior (por ejemplo, mayor escalabilidad y una clase de CPU actualizada).

Las máquinas virtuales de la serie Dsv5 no tienen ningún almacenamiento temporal, lo que reduce el precio de entrada.  Puede conectar almacenamiento de discos SSD estándar, HDD estándar y SSD prémium a estas máquinas virtuales. También puede conectar almacenamiento en disco Ultra en función de su disponibilidad regional. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): obligatorio <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): No compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Rendimiento máx. de disco de expansión sin caché: IOPS/MBps<sup>3</sup> | Nº máx. NIC | Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v5<sup>1,2</sup> | 2  | 8   | Solo almacenamiento remoto | 4  | 3750/85    | 10 000/1200 | 2 | 12500 |
| Standard_D4s_v5               | 4  | 16  | Solo almacenamiento remoto | 8  | 6400/145   | 20 000/1200 | 2 | 12500 |
| Standard_D8s_v5               | 8  | 32  | Solo almacenamiento remoto | 16 | 12 800/290  | 20 000/1200 | 4 | 12500 |
| Standard_D16s_v5              | 16 | 64  | Solo almacenamiento remoto | 32 | 25 600/600  | 40 000/1200 | 8 | 12500 |
| Standard_D32s_v5              | 32 | 128 | Solo almacenamiento remoto | 32 | 51 200/865  | 80000/2000 | 8 | 16000 |
| Standard_D48s_v5              | 48 | 192 | Solo almacenamiento remoto | 32 | 76 800/1315 | 80 000/3000 | 8 | 24000 |
| Standard_D64s_v5              | 64 | 256 | Solo almacenamiento remoto | 32 | 80 000/1735 | 80 000/3000 | 8 | 30000 |
| Standard_D96s_v5              | 96 | 384 | Solo almacenamiento remoto | 32 | 80 000/2600 | 80 000/4000 | 8 | 35000 |

<sup>1</sup> Las redes aceleradas son necesarias y están activadas de forma predeterminada en todas las máquinas virtuales de la serie Dsv5.<br>
<sup>2</sup> Las redes aceleradas se pueden aplicar a dos NIC.<br>
<sup>3</sup> Las máquinas virtuales de la serie Dsv5 pueden [expandir](disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.

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
