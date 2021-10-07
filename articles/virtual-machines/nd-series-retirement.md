---
title: Retirada de la serie ND
description: Retirada de la serie ND antes del 31 de agosto de 2022
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: 7111af4f275f97ff35e4a0cf9846eb6b68a807f9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699841"
---
# <a name="migrate-your-nd-series-virtual-machines-by-august-31-2022"></a>Migración de las máquinas virtuales de la serie ND antes del 31 de agosto de 2022
A medida que seguimos incorporando en Azure instancias de máquinas virtuales modernas y optimizadas aprovechando las innovaciones más recientes en las tecnologías de centros de datos, planeamos cuidadosamente la retirada de hardware antiguo. Con esto en mente, retiraremos los tamaños de máquinas virtuales GPU ND, con tecnología de GPU de NVIDIA Tesla P40 el 31 de agosto de 2022. 

## <a name="how-does-the-nd-series-migration-affect-me"></a>¿Cómo me afecta la migración de la serie ND?  

Después del 31 de agosto de 2022, las máquinas virtuales de tamaño ND que queden en la suscripción se establecerán en un estado desasignado. Estas máquinas virtuales se detendrán y quitarán del host. En el estado desasignado, ya no se facturará por estas máquinas virtuales. 

La retirada de estos tamaños de máquina virtual afecta únicamente a los tamaños de máquina virtual de la [serie ND](nd-series.md). Esto no afecta a las máquinas virtuales más recientes de las series [NCv3](ncv3-series.md), [NC T4 v3](nct4-v3-series.md) y [ND v2](ndv2-series.md). 

## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar?  
Tendrá que cambiar el tamaño o desasignar las máquinas virtuales NC. Se recomienda mover las cargas de trabajo de GPU a otro tamaño de máquina virtual con GPU. Obtenga más información sobre cómo migrar las cargas de trabajo a otro [tamaño de máquina virtual de Azure con GPU](sizes-gpu.md).

## <a name="next-steps"></a>Pasos siguientes
[Más información](n-series-migration.md) sobre cómo migrar las cargas de trabajo a otros tamaños de máquina virtual de Azure con GPU. 

Si tiene preguntas, póngase en contacto con nosotros a través del departamento de soporte técnico.
