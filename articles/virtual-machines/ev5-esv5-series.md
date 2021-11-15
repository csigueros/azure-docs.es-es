---
title: 'Series Ev5 y Esv5: Azure Virtual Machines'
description: Especificaciones de las máquinas virtuales de las series Ev5 y Esv5.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: de1f9694bac6cb57c6d14e352a958e7bfc7f0516
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866796"
---
# <a name="ev5-and-esv5-series"></a>Series Ev5 y Esv5

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las máquinas virtuales de las series Ev5 y Esv5 se ejecutan en los procesadores Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) de 3ª generación en una configuración con [Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), lo que supone una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general. Este nuevo procesador presenta una velocidad de reloj Turno de todos los núcleos de 3,5 GHz con [tecnología Intel&reg; Turbo Boost](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [extensiones de vector avanzadas Intel&reg; 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) e [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Con hasta 672 GiB de RAM, estas máquinas virtuales son ideales para aplicaciones empresariales con un uso intensivo de memoria, servidores de bases de datos relacionales y cargas de trabajo de análisis en memoria. Las series Ev5 y Esv5 proporcionan una mejor propuesta de valor para las cargas de trabajo que no requieren disco temporal local. Para obtener información sobre máquinas virtuales similares con disco local, vea [Máquinas virtuales de las series Edv5 y Edsv5](edv5-edsv5-series.md).

> [!NOTE]
> Para ver las preguntas más frecuentes, consulte [Tamaños de máquina virtual de Azure sin disco temporal local](azure-vms-no-temp-disk.yml).

## <a name="ev5-series"></a>Serie Ev5

Las máquinas virtuales de la serie Ev5 se ejecutan en el procesador Intel® Xeon® Platinum 8370C (Ice Lake) de 3ª generación que alcanza una velocidad de reloj turbo de todos los núcleos de hasta 3,5 GHz.  Estas máquinas virtuales ofrecen hasta 104 vCPU y 672 GiB de RAM. Las máquinas virtuales de la serie Ev5 no tienen ningún almacenamiento temporal, lo que reduce el precio de entrada.

La serie Ev5 admite los tipos de disco SSD estándar y HDD estándar. Para usar almacenamiento SSD prémium o Disco Ultra, seleccione las máquinas virtuales de la serie Esv5. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).

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
| Standard_E2_v5<sup>1,2</sup>  | 2   | 16  | Solo almacenamiento remoto | 4  | 2 | 12500 |
| Standard_E4_v5                | 4   | 32  | Solo almacenamiento remoto | 8  | 2 | 12500 |
| Standard_E8_v5                | 8   | 64  | Solo almacenamiento remoto | 16 | 4 | 12500 |
| Standard_E16_v5               | 16  | 128 | Solo almacenamiento remoto | 32 | 8 | 12500 |
| Standard_E20_v5               | 20  | 160 | Solo almacenamiento remoto | 32 | 8 | 12500  |
| Standard_E32_v5               | 32  | 256 | Solo almacenamiento remoto | 32 | 8 | 16000  |
| Standard_E48_v5               | 48  | 384 | Solo almacenamiento remoto | 32 | 8 | 24000  |
| Standard_E64_v5               | 64  | 512 | Solo almacenamiento remoto | 32 | 8 | 30000  |
| Standard_E96_v5               | 96  | 672 | Solo almacenamiento remoto | 32 | 8 | 30000  |
| Standard_E104i_v5<sup>3</sup> | 104 | 672 | Solo almacenamiento remoto | 64 | 8 | 100000 |

<sup>1</sup> Las redes aceleradas son necesarias y están activadas de forma predeterminada en todas las máquinas virtuales de la serie Ev5.<br>
<sup>2</sup> Las redes aceleradas se pueden aplicar a dos NIC.<br>
<sup>3</sup> La instancia está [aislada](../security/fundamentals/isolation-choices.md#compute-isolation) en el hardware dedicado a un solo cliente.<br>

## <a name="esv5-series"></a>Serie Esv5

Las máquinas virtuales de la serie Esv5 se ejecutan en el procesador Intel® Xeon® Platinum 8370C (Ice Lake) de 3ª generación que alcanza una velocidad de reloj turbo de todos los núcleos de hasta 3,5 GHz.  Estas máquinas virtuales ofrecen hasta 104 vCPU y 672 GiB de RAM. Las máquinas virtuales de la serie Esv5 no tienen ningún almacenamiento temporal, lo que reduce el precio de entrada.

La serie Esv5 admite los tipos de disco SSD estándar, HDD estándar y SSD prémium. También puede conectar almacenamiento en disco Ultra en función de su disponibilidad regional. El almacenamiento en disco se factura independientemente de las máquinas virtuales. [Consulte los precios de los discos](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): obligatorio <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): No compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Rendimiento máx. de disco de expansión sin caché: IOPS/MBps<sup>5</sup> | Nº máx. NIC | Ancho de banda de red máx. (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v5<sup>1,2</sup>  | 2   | 16  | Solo almacenamiento remoto | 4  | 3750/85     | 10 000/1200 | 2 | 12500 |
| Standard_E4s_v5                | 4   | 32  | Solo almacenamiento remoto | 8  | 6400/145    | 20 000/1200 | 2 | 12500 |
| Standard_E8s_v5                | 8   | 64  | Solo almacenamiento remoto | 16 | 12 800/290   | 20 000/1200 | 4 | 12500 |
| Standard_E16s_v5               | 16  | 128 | Solo almacenamiento remoto | 32 | 25 600/600   | 40 000/1200 | 8 | 12500 |
| Standard_E20s_v5               | 20  | 160 | Solo almacenamiento remoto | 32 | 32 000/750   | 64 000/1600 | 8 | 12500  |
| Standard_E32s_v5               | 32  | 256 | Solo almacenamiento remoto | 32 | 51 200/865   | 80000/2000 | 8 | 16000  |
| Standard_E48s_v5               | 48  | 384 | Solo almacenamiento remoto | 32 | 76 800/1315  | 80 000/3000 | 8 | 24000  |
| Standard_E64s_v5               | 64  | 512 | Solo almacenamiento remoto | 32 | 80 000/1735  | 80 000/3000 | 8 | 30000  |
| Standard_E96s_v5<sup>3</sup>   | 96  | 672 | Solo almacenamiento remoto | 32 | 80 000/2600  | 80 000/4000 | 8 | 35000  |
| Standard_E104is_v5<sup>4</sup> | 104 | 672 | Solo almacenamiento remoto | 64 | 120 000/4000 | 120 000/4000 | 8 | 100000 |

<sup>1</sup> Las redes aceleradas son necesarias y están activadas de forma predeterminada en todas las máquinas virtuales de la serie Esv5.<br>
<sup>2</sup> Las redes aceleradas se pueden aplicar a dos NIC.<br>
<sup>3</sup> Tamaños de [núcleo restringidos](constrained-vcpu.md) disponibles.<br>
<sup>4</sup> La instancia está [aislada](../security/fundamentals/isolation-choices.md#compute-isolation) en el hardware dedicado a un solo cliente.<br>
<sup>5</sup> Las máquinas virtuales de la serie Esv5 pueden [expandir](disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.

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
