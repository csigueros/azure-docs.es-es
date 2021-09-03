---
title: Serie Fsv2
description: Especificaciones de las máquinas virtuales de la serie Fsv2.
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: fbe6953f57ac850add4b547ae1d2eeaadb77aafd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741815"
---
# <a name="fsv2-series"></a>Serie Fsv2

La Serie Fsv2 se ejecuta con los procesadores Intel® Xeon® Platino 8272CL (Cascade Lake) y los procesadores Intel® Xeon® Platino 8168 (Skylake). Presenta una velocidad de reloj turbo sostenida de todos los núcleos de hasta 3,4 GHz y una velocidad de reloj turbo de un solo núcleo máxima de 3,7 GHz. Las instrucciones de Intel® AVX-512 son nuevas en los procesadores escalables de Intel. Estas instrucciones pueden llegar a duplicar el rendimiento en las cargas de trabajo de procesamiento vectorial en las operaciones de número de punto flotante de precisión individual y doble. En otras palabras, son muy rápidas para cualquier carga de trabajo de cálculo.

Las máquinas virtuales de la serie Fsv2 cuentan con la tecnología Hyper-Threading de Intel®.

[ACU](acu.md): 195 - 210<br>
[Premium Storage](premium-storage-performance.md): Compatible<br>
[Almacenamiento en caché de Premium Storage](premium-storage-performance.md): Compatible<br>
[Migración en vivo](maintenance-and-updates.md): Compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): Compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 1 y 2<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible <br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
<br>

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal y en caché: IOPS/Mbps (tamaño de caché en GiB) | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/Mbps |  Rendimiento máximo del disco sin almacenamiento en la caché expandido: IOPS/MBps<sup>1</sup> |Nº máx. NIC|Ancho de banda de red esperado (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2<sup>4</sup>  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 4000/200 | 2| 5000   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 8000/200 | 2|10000  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 16 000/400 | 4|12500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 32 000/800 | 4|12500  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 64 000/1600 | 8|16000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 80000/2000 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 80000/2000 | 8|28000 |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 80000/2000 | 8|30000 |

<sup>1</sup> Las máquinas virtuales de la serie Fsv2 pueden [expandir](./disk-bursting.md) su rendimiento de disco y llegar a una expansión máxima de hasta 30 minutos cada vez.

<sup>2</sup> El uso de más de 64 vCPU requiere uno de estos sistemas operativos invitados compatibles:

- Windows Server 2016 o posterior
- Ubuntu 16.04 LTS o posterior, con kernel ajustado para Azure (kernel 4.15 o posterior)
- SLES 12 SP2 o posterior
- RHEL o CentOS, versiones 6.7 a 6.10, con la versión 4.3.1 (o posterior) del paquete LIS proporcionado por Microsoft instalada
- RHEL o CentOS, versión 7.3, con la versión 4.2.1 (o posterior) del paquete LIS proporcionado por Microsoft instalada
- RHEL o CentOS, versión 7.6 o posterior
- Oracle Linux con UEK4 o posterior
- Debian 9 con el kernel de backports, Debian 10 o posterior
- CoreOS con un kernel 4.14 o posterior

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

Más información sobre los tipos de disco: [Tipos de disco](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.
