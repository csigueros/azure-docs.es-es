---
title: Retirada de la serie H
description: Retirada de la serie H a partir del 1 de septiembre de 2021
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 023fb8a352e1e02023fe0ce16afba9531625e370
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515840"
---
# <a name="migrate-your-h-and-h_promo-series-virtual-machines-by-august-31-2022"></a>Migración de las máquinas virtuales de la serie H_Promo antes del 31 de agosto de 2022
Dado que Microsoft Azure ha introducido nuevas generaciones de máquinas virtuales de informática de alto rendimiento (HPC), de uso general y optimizadas para memoria, se recomienda migrar las cargas de trabajo de las máquinas virtuales originales de la serie H (incluidas las de promoción de la serie H) a nuestras ofertas más recientes.

Las máquinas virtuales Azure [HC](hc-series.md), [HBv2](hbv2-series.md), [HBv3](hbv3-series.md), [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md), [Ev4](ev4-esv4-series.md) y [Eav4](eav4-easv4-series.md) tienen mayor ancho de banda de memoria, funcionalidades de redes mejoradas y un mejor costo/rendimiento en una amplia variedad de cargas de trabajo de HPC. En consecuencia, retiramos los tamaños de máquinas virtuales de Azure de la serie H (H8, H8m, H16, H16r, H16m, H16mr, H8 Promo, H8m Promo, H16 Promo, H16r Promo, H16m Promo y H16mr Promo) el 31 de agosto de 2022.

## <a name="how-does-the-h-series-migration-affect-me"></a>¿Cómo me afecta la migración de la serie H?  

Después del 31 de agosto de 2022, las suscripciones de máquina virtual de la serie H restantes indicadas anteriormente se establecerán en un estado desasignado, dejarán de funcionar y ya no incurrirán en cargos de facturación. 

La retirada de los tamaños de máquina virtual actuales solo afecta a los tamaños de VM de la [serie H](h-series.md), incluidos los de la promoción de la serie H. 

## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar?  

Tendrá que cambiar el tamaño o desasignar las máquinas virtuales de la serie H. Se recomienda migrar las cargas de trabajo de las máquinas virtuales originales de la serie H (incluidas las de promoción de la serie H) a nuestras ofertas más recientes.

En el caso de las cargas de trabajo de HPC, las VM [HC](hc-series.md), [HBv2](hbv2-series.md) y [HBv3](hbv3-series.md) ofrecen niveles considerablemente mayores de rendimiento y rentabilidad de la carga de trabajo de HPC gracias a las enormes mejoras en la arquitectura de núcleos de CPU, un mayor ancho de banda de memoria, memorias caché L3 más grandes y compatibilidad mejorada de software y hardware de redes InfiniBand en comparación con la serie H. Como resultado, las series HC, HBv2 y HBv3 ofrecerán en general un rendimiento considerablemente mejor por unidad de costo (maximizando el rendimiento por una cantidad fija de gasto) y el costo por rendimiento (minimizando el costo por una cantidad fija de rendimiento). 

En el caso de las cargas de trabajo de uso general, las VM [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md) y Dv5 ofrecen un rendimiento de CPU igual o superior con recuentos de núcleos idénticos o mayores, una cantidad comparable de memoria por núcleo de CPU física, mejores funcionalidades de Redes de Azure y un menor costo general. 

En el caso de las cargas de trabajo optimizadas para memoria, las VM [Ev4](ev4-esv4-series.md), [Eav4](eav4-easv4-series.md) y Ev5 ofrecen un rendimiento de CPU igual o superior con recuentos de núcleos idénticos o mayores, una cantidad comparable de memoria por núcleo de CPU físico, mejores funcionalidades de Redes de Azure y un menor costo general. 

Las VM de la [serie H](h-series.md) (incluidas las de promoción de la serie H) no se retirarán hasta septiembre de 2022, por lo que proporcionamos esta guía de antemano para ofrecer a los clientes un período largo para evaluar, planear y ejecutar su migración. 


### <a name="migration-steps"></a>Pasos de migración 
1. Elija una serie y un tamaño para la migración. 
2. Obtenga la cuota para la serie de máquinas virtuales de destino. 
3. Cambie el tamaño actual de la serie N de VM al tamaño objetivo. 


### <a name="breaking-changes"></a>Últimos cambios 
Si usa tamaños de VM de la serie H que exponen una interfaz de redes InfiniBand (por ejemplo, aquellos con una "r" en el nombre de tamaño de VM) y quiere que los nuevos tamaños de VM también admitan redes InfiniBand, ya no podrá usar imágenes de sistema operativo heredadas compatibles con controladores InfiniBand integrados (CentOS 7.4 y versiones anteriores, Windows Server 2012). En su lugar, use imágenes de sistemas operativos modernos como las de Azure Marketplace, que admiten sistemas operativos modernos (CentOS 7.5 y versiones más recientes, Windows Server 2016 y más recientes) y controladores OFED estándar. Consulte [pila de software compatible](hbv3-series.md#get-started), que incluye el sistema operativo admitido para los tamaños de VM respectivos. 


### <a name="get-quota-for-the-target-vm-family"></a>Obtención de la cuota para la familia de máquinas virtuales de destino 

Siga la guía para [solicitar un aumento de la cuota de vCPU por familia de máquinas virtuales](../azure-portal/supportability/per-vm-quota-requests.md).


### <a name="resize-the-current-virtual-machine"></a>Cambio del tamaño de la máquina virtual actual
Puede [cambiar el tamaño de la máquina virtual mediante PowerShell](./windows/resize-vm.md), la [CLI de Azure](./linux/change-vm-size.md) o Azure Portal.
