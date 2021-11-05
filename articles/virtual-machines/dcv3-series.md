---
title: 'Series DCsv3 y DCdsv3: Azure Virtual Machines'
description: Especificaciones de las máquinas virtuales de las series DCsv3 y DCdsv3.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: mmcrey
ms.custom: ignite-fall-2021
ms.openlocfilehash: a7fc9d45db3469fa9198990e4308e431ac0cbc68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093436"
---
# <a name="dcsv3-and-dcdsv3-series"></a>Serie DCsv3 y DCdsv3

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

> [!NOTE] 
> DCsv3 y DCdsv3 están en versión preliminar pública a partir del 1 de noviembre de 2021

Las máquinas virtuales de la serie DCsv3 y DCdsv3 pueden ayudar a proteger la confidencialidad y la integridad del código y de los datos mientras se están procesando en la nube pública. Al aprovechar Intel® Software Guard Extensions e Intel® Total Memory Encryption - Multi Key, los clientes pueden asegurarse de que sus datos siempre están cifrados y protegidos en uso. 

Estas máquinas tienen tecnología de los procesadores escalables Intel® Xeon de tercera generación y aprovechan la tecnología Intel® Turbo Boost Max 3.0 para alcanzar los 3,5 GHz. 

Con esta generación, los núcleos de CPU han aumentado 6 veces (hasta un máximo de 48 núcleos físicos), la memoria cifrada (EPC) ha aumentado de 1500 a 256 GB, la memoria normal ha aumentado de 12 a 384 GB. Todos estos cambios mejoran sustancialmente el rendimiento generación con generación y desbloquean nuevos escenarios totalmente nuevos. 

> [!NOTE]
> El hyperthreading está deshabilitado para una posición de seguridad agregada. Los precios se basan en el rendimiento superior de los núcleos físicos frente a los virtuales, así como en las funcionalidades de seguridad únicas de la serie DC.

Ofrecemos dos variantes que dependen de si la carga de trabajo se beneficia de un disco local o no. Tanto si elige una máquina virtual con un disco local como si no, puede conectar el almacenamiento de disco persistente remoto a todas las máquinas virtuales. Las opciones de disco remoto (por ejemplo, para el disco de arranque de la máquina virtual) se facturan por separado de las máquinas virtuales en cualquier caso, como siempre. 

## <a name="configuration"></a>Configuración

CPU: Procesador escalable Intel® Xeon de 3ª generación 8370C<br>
Frecuencia All-Core base: 2,8 GHz<br>
[Turbo Boost Max 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html): habilitado, frecuencia máxima de 3,5 GHz<br>
[Hyper-Threading](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html): no compatible<br>
Cifrado de memoria total: varias claves habilitadas<br>
[Premium Storage](premium-storage-performance.md): Compatible<br>
[Disk Storage Ultra](disks-enable-ultra-ssd.md): Compatible<br>
[Redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md): Compatible (solo aprovisionamiento de la CLI o plantilla de ARM)<br>
[Azure Kubernetes Service](../aks/intro-kubernetes.md): Compatible (el aprovisionamiento de la CLI solo inicialmente)<br>
[Migración en vivo](maintenance-and-updates.md): No compatible<br>
[Actualizaciones con conservación de memoria](maintenance-and-updates.md): No compatible<br>
[Compatibilidad con generación de VM](generation-2.md): Generación 2<br>
[Discos de sistema operativo efímero](ephemeral-os-disks.md): Compatible <br>
[Dedicated Host](dedicated-hosts.md): Próximamente<br>

## <a name="dcsv3-series-technical-specifications"></a>Especificaciones técnicas de la serie DCsv3

| Size             | Núcleos físicos | GB de memoria | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Nº máx. NIC |  Memoria EPC (GB) |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1s_v3 | 1              | 8           | N/D                    | 4              | 2     |  4                 |
| Standard_DC2s_v3 | 2              | 16          | No aplicable                    | 8              | 2     |  8                 |
| Standard_DC4s_v3 | 4              | 32          | N/D                    | 16             | 4     |  16                |
| Standard_DC8s_v3 | 8              | 64          | N/D                    | 32             | 8     |  32                |
| Standard_DC16s_v3  | 16           | 128         | N/D                    | 32             | 8     |  64                |
| Standard_DC24s_v3  | 24           | 192         | N/D                    | 32             | 8     |  128               |
| Standard_DC32s_v3  | 32           | 256         | N/D                    | 32             | 8     |  192               |
| Standard_DC48s_v3  | 48           | 384         | N/D                    | 32             | 8     |  256               |

## <a name="dcdsv3-series-technical-specifications"></a>Especificaciones técnicas de la serie DCdsv3

| Size             | Núcleos físicos | GB de memoria | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Nº máx. NIC |  Memoria EPC (GB) |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1ds_v3 | 1              | 8           | 75                    | 4              | 2     |  4                 |
| Standard_DC2ds_v3 | 2              | 16          | 150                    | 8              | 2     |  8                 |
| Standard_DC4ds_v3 | 4              | 32          | 300                    | 16             | 4     |  16                |
| Standard_DC8ds_v3 | 8              | 64          | 600                    | 32             | 8     |  32                |
| Standard_DC16ds_v3  | 16           | 128         | 1200                    | 32             | 8     |  64                |
| Standard_DC24ds_v3  | 24           | 192         | 1800                    | 32             | 8     |  128               |
| Standard_DC32ds_v3  | 32           | 256         | 2400                    | 32             | 8     |  192               |
| Standard_DC48ds_v3  | 48           | 384         | 2400                    | 32             | 8     |  256               |

## <a name="get-started"></a>Primeros pasos

- Cree máquinas virtuales DCsv3 y DCdsv3 mediante [Azure Portal](./linux/quick-create-portal.md)
- Las VM de la serie DCsv3 y DCdsv3 son [VM de 2ª generación](./generation-2.md#creating-a-generation-2-vm) y solo admiten imágenes de `Gen2`.
- Actualmente está disponible en las regiones enumeradas en [Productos de Azure por región](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="more-sizes-and-information"></a>Más tamaños e información

- [Uso general](sizes-general.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Generaciones anteriores](sizes-previous-gen.md)
- [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)

Calculadora de precios: [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.
