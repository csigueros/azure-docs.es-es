---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/27/2021
ms.author: rogarana
ms.openlocfilehash: aedfaf0606547653e88ad6e120f51a542dabdb95
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155178"
---
Puede asociar un número de discos de datos a una máquina virtual de Azure. Según los objetivos de escalabilidad y rendimiento de los discos de datos de una máquina virtual, puede determinar el número y el tipo de disco necesarios para satisfacer sus requisitos de capacidad y rendimiento.

> [!IMPORTANT]
> Para obtener un rendimiento óptimo, limite el número de discos muy usados que se conectan a la máquina virtual para evitar una posible limitación. Si todos los discos asociados no se usan mucho al mismo tiempo, la máquina virtual puede admitir un mayor número de discos.

**Para discos administrados de Azure:**

En la tabla siguiente se muestran los límites predeterminado y máximo del número de recursos por región y suscripción. Los límites siguen siendo los mismos, independientemente de que los discos estén cifrados con claves administradas por la plataforma o claves administradas por el cliente. No hay ningún límite en el número de discos administrados, instantáneas e imágenes por grupo de recursos.  

> | Resource | Límite |
> | --- | --- |
> | Discos administrados estándar | 50.000 |
> | Discos administrados SSD estándar | 50.000 |
> | Discos administrados Premium | 50.000 |
> | Instantáneas Standard_LRS<sup>1</sup> | 75 000 |
> | Instantáneas Standard_ZRS<sup>1</sup> | 75 000 |
> | Imagen administrada | 50.000 |

<sup>1</sup> El número total de instantáneas de disco completo que puede tener un disco individual es 200. Un disco individual también puede tener 200 instantáneas incrementales, que se cuentan por separado de las instantáneas de disco completo. 

**En el caso de cuentas de almacenamiento estándar:** una cuenta de almacenamiento estándar tiene una tasa total máxima de solicitudes de 20 000 E/S por segundo. El número total de IOPS en todos los discos de máquina virtual de una cuenta de almacenamiento estándar no debe superar este límite.
  
Puede calcular aproximadamente el número de discos muy usados que admite una sola cuenta de almacenamiento estándar en función del límite de tasa de solicitudes. Por ejemplo, en el caso de una máquina virtual de nivel Básico, el número máximo de discos muy usados está alrededor de 66, que equivale a 20 000/300 IOPS por disco. El número máximo de discos muy usados para una máquina virtual de nivel Estándar es de aproximadamente 40, que equivale a 20 000/500 IOPS por disco. 

**En el caso de cuentas de almacenamiento premium:** una cuenta de almacenamiento premium tiene una capacidad total máxima de proceso de 50 Gbps. La capacidad total de proceso en todos los discos de la máquina virtual no debe superar este límite.

