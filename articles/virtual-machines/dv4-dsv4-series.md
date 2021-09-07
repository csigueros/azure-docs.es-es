---
title: 'Series Dv4 y Dsv4: Azure Virtual Machines'
description: Especificaciones de las máquinas virtuales de las series Dv4 y Dsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: c05ab2b89a275b4e33774cca34c0e971657eddb2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751709"
---
# <a name="dv4-and-dsv4-series"></a>Series Dv4 y Dsv4

Las máquinas virtuales de las series Dv4 y Dsv4 se ejecutan en los procesadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) en una configuración con tecnología Hyper-Threading, lo que proporciona una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general. Cuenta con una velocidad de reloj en todos los núcleos de 3,4 GHz. 

> [!NOTE]
> Para ver las preguntas más frecuentes, consulte [Tamaños de máquina virtual de Azure sin disco temporal local](azure-vms-no-temp-disk.yml).

## <a name="dv4-series"></a>Serie Dv4

Los tamaños de la serie Dv4 se ejecutan en procesadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Los tamaños de la serie Dv4 ofrecen una combinación de vCPU, memoria y almacenamiento remoto adecuados para la mayoría de las cargas de trabajo de producción. Las máquinas virtuales de la serie Dv4 cuentan con la [tecnología Hyper-Threading de Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

El almacenamiento en disco de datos remotos se factura de forma independiente a las máquinas virtuales. Para usar discos de Premium Storage, utilice los tamaños de la serie Dsv4. Los precios y los medidores de facturación de los tamaños de la serie Dsv4 son los mismos que para la serie Dv4.

> [!NOTE]
> Después de un reinicio, es posible que aparezca un archivo llamado *Data_loss_warning.txt* junto a la unidad C (el primer disco de datos conectado desde Azure Portal). En este escenario, a pesar del nombre de archivo, no se ha producido ninguna pérdida de datos en el disco. En general, el archivo *Data_loss_warning.txt* se copia normalmente en la unidad temporal. Si usa una máquina virtual que no tiene una unidad temporal, WindowsAzureGuestAgent copia incorrectamente el archivo en la primera letra de unidad. En las máquinas virtuales v4, la primera letra de unidad es un disco de datos.
>
> En la última versión (2.7.41491.999) del agente de máquina virtual, se aplicó una resolución para este problema.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): No compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): No compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): No compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Nº máx. NIC|Ancho de banda de red esperado (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4<sup>1</sup> | 2 | 8 | Solo almacenamiento remoto | 4 | 2|5000 |
| Standard_D4_v4 | 4 | 16  | Solo almacenamiento remoto | 8 | 2|10000 |
| Standard_D8_v4 | 8 | 32 | Solo almacenamiento remoto | 16 | 4|12500 |
| Standard_D16_v4 | 16 | 64 | Solo almacenamiento remoto | 32 | 8|12500 |
| Standard_D32_v4 | 32 | 128 | Solo almacenamiento remoto | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Solo almacenamiento remoto | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Solo almacenamiento remoto | 32 | 8|30000 |

<sup>1</sup> Las redes aceleradas solo se pueden aplicar a una única NIC. 


## <a name="dsv4-series"></a>Serie Dsv4

Los tamaños de la serie Dsv4 se ejecutan en procesadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Los tamaños de la serie Dv4 ofrecen una combinación de vCPU, memoria y almacenamiento remoto adecuados para la mayoría de las cargas de trabajo de producción. Las máquinas virtuales de la serie Dsv4 cuentan con la [tecnología Hyper-Threading de Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). El almacenamiento en disco de datos remotos se factura de forma independiente a las máquinas virtuales.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible<br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): No compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps | Rendimiento máximo del disco sin almacenamiento en la caché expandido: IOPS/MBps<sup>1</sup> | Nº máx. NIC|Ancho de banda de red esperado (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4<sup>2</sup> | 2 | 8  | Solo almacenamiento remoto | 4 | 3200/48 | 4000/200 |2|5000 |
| Standard_D4s_v4 | 4 | 16 | Solo almacenamiento remoto | 8 | 6400/96 | 8000/200 |2|10000 |
| Standard_D8s_v4 | 8 | 32 | Solo almacenamiento remoto | 16 | 12800/192 | 16 000/400 |4|12500 |
| Standard_D16s_v4 | 16 | 64  | Solo almacenamiento remoto | 32 | 25600/384 | 32 000/800 |8|12500 |
| Standard_D32s_v4 | 32 | 128 | Solo almacenamiento remoto | 32 | 51200/768 | 64 000/1600 |8|16000 |
| Standard_D48s_v4 | 48 | 192 | Solo almacenamiento remoto | 32 | 76800/1152 | 80000/2000 |8|24000 |
| Standard_D64s_v4 | 64 | 256 | Solo almacenamiento remoto | 32 | 80000/1200 | 80000/2000 |8|30000 |

<sup>1</sup> Las máquinas virtuales de la serie Dsv4 pueden [expandir](./disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima hasta durante 30 minutos cada vez.<br>
<sup>2</sup> Las redes aceleradas solo se pueden aplicar a una única NIC. 
