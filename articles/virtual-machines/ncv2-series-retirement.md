---
title: Retirada de la serie NCv2
description: Retirada de la serie NCv2 antes del 31 de agosto de 2022
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: 7ed24690ff287c01b5b9c0e404a5f71349a021f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699456"
---
# <a name="migrate-your-ncv2-series-virtual-machines-by-august-31-2022"></a>Migración de las máquinas virtuales de la serie NCv2 antes del 31 de agosto de 2022
A medida que seguimos incorporando en Azure instancias de máquinas virtuales modernas y optimizadas aprovechando las innovaciones más recientes en las tecnologías de centros de datos, planeamos cuidadosamente cómo retiramos hardware antiguo. Con esto en mente, retiraremos los tamaños de máquinas virtuales GPU NC (v2), con tecnología de GPU de NVIDIA Tesla P100 el 31 de agosto de 2022. 

## <a name="how-does-the-ncv2-series-migration-affect-me"></a>¿Cómo me afecta la migración de la serie NCv2?  

Después del 31 de agosto de 2022, las máquinas virtuales de tamaño NCv2 que queden en la suscripción se establecerán en un estado desasignado. Estas máquinas virtuales se detendrán y quitarán del host. En el estado desasignado, ya no se facturará por estas máquinas virtuales. 

La retirada de estos tamaños de máquina virtual afecta únicamente a los tamaños de VM de la [serie NCv2](ncv2-series.md). Esto no afecta a las máquinas virtuales de las series [NCv3](ncv3-series.md), [NC T4 v3](nct4-v3-series.md) y [ND v2](ndv2-series.md). 

## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar?  
Tendrá que cambiar el tamaño o desasignar las máquinas virtuales NC. Se recomienda mover las cargas de trabajo con GPU a otro tamaño de máquina virtual con GPU. Obtenga más información sobre cómo migrar las cargas de trabajo a otro [tamaño de máquina virtual de Azure con GPU](sizes-gpu.md).

## <a name="next-steps"></a>Pasos siguientes

[Más información](n-series-migration.md) sobre cómo migrar las cargas de trabajo a otros tamaños de máquina virtual de Azure con GPU. 

Si tiene preguntas, póngase en contacto con nosotros a través del departamento de soporte técnico.
