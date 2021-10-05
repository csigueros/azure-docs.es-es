---
title: Retirada de la serie NC
description: Retirada de la serie NC antes del 31 de agosto de 2022
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: e95c32903f601a883b62a06eb1b1901c15042dbb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699845"
---
# <a name="migrate-your-nc-and-nc_promo-series-virtual-machines-by-august-31-2022"></a>Migración de las máquinas virtuales de las series NC y NC_Promo antes del 31 de agosto de 2022
A medida que seguimos incorporando en Azure instancias de máquinas virtuales modernas y optimizadas aprovechando las innovaciones más recientes en las tecnologías de centros de datos, planeamos cuidadosamente cómo retiramos hardware antiguo. Con esto en mente, retiraremos los tamaños de máquinas virtuales GPU NC (v1), con tecnología de GPU de NVIDIA Tesla K80 el 31 de agosto de 2022. 

## <a name="how-does-the-nc-series-migration-affect-me"></a>¿Cómo me afecta la migración de la serie NC?  

Después del 31 de agosto de 2022, las máquinas virtuales de tamaño NC que queden en la suscripción se establecerán en un estado desasignado. Estas máquinas virtuales se detendrán y quitarán del host. En el estado desasignado, ya no se facturará por estas máquinas virtuales. 

La retirada de estos tamaños de máquina virtual afecta únicamente a los tamaños de VM de la [serie NC](nc-series.md). Esto no afecta a las máquinas virtuales de las series [NCv3](ncv3-series.md), [NC T4 v3](nct4-v3-series.md) y [ND v2](ndv2-series.md). 


## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar?  
Tendrá que cambiar el tamaño o desasignar las máquinas virtuales NC. Se recomienda mover las cargas de trabajo con GPU a otro tamaño de máquina virtual con GPU. Obtenga más información sobre cómo migrar las cargas de trabajo a otro [tamaño de máquina virtual de Azure con GPU](sizes-gpu.md).

## <a name="next-steps"></a>Pasos siguientes

[Más información](n-series-migration.md) sobre cómo migrar las cargas de trabajo a otros tamaños de máquina virtual de Azure con GPU. 

Si tiene preguntas, póngase en contacto con nosotros a través del departamento de soporte técnico.
