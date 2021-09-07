---
title: Retirada de la serie NV
description: Retirada de la serie NV a partir del 1 de septiembre de 2021
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: fcd0d460837195817018882d92b94b0012a14a6f
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254274"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>Migración de las máquinas virtuales de las series NV y NV_Promo antes del 31 de agosto de 2022
A medida que seguimos incorporando en Azure instancias de máquinas virtuales modernas y optimizadas gracias a las innovaciones más recientes en las tecnologías de centros de datos, planeamos cuidadosamente cómo retiramos el hardware antiguo.
Teniendo esto en cuenta, vamos a retirar los tamaños de máquinas virtuales de Azure de la serie NV el 1 de septiembre de 2022.

## <a name="how-does-the-nv-series-migration-affect-me"></a>¿Cómo me afecta la migración de la serie NV?  

A partir del 1 de septiembre de 2022, las máquinas virtuales de tamaño NV y NV_Promo que queden en la suscripción se establecerán en un estado desasignado. Estas máquinas virtuales se detendrán y quitarán del host. En el estado desasignado, ya no se facturará por estas máquinas virtuales. 

La retirada actual de estos tamaños de máquina virtual solo afecta a los tamaños de máquina virtual de la [serie NV](nv-series.md). Esto no afecta a las máquinas virtuales de las series [NVv3](nvv3-series.md) y [NVv4](nvv4-series.md). 

## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar?  

Tendrá que cambiar el tamaño o desasignar las máquinas virtuales NV. Se recomienda mover las cargas de trabajo de visualización y gráficos de GPU a otro [tamaño de máquina virtual acelerada por GPU](sizes-gpu.md).

[Más información](nv-series-migration-guide.md) sobre cómo migrar las cargas de trabajo a otros tamaños de máquina virtual de Azure con GPU. 

Si tiene preguntas, póngase en contacto con nosotros a través del departamento de soporte técnico.
