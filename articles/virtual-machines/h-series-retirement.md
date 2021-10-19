---
title: Retirada de la serie H
description: La retirada de la serie H se inició el 1 de septiembre de 2021.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 1ad9a9384d00ec0961235c167379d9984ec417fe
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705233"
---
# <a name="migrate-your-h-and-h-series-promo-virtual-machines-by-august-31-2022"></a>Migración de las máquinas virtuales de la serie H y de promoción de la serie H antes del 31 de agosto de 2022

Microsoft Azure ha presentado nuevas generaciones de máquinas virtuales de informática de alto rendimiento (HPC), de uso general y optimizadas para memoria. Por este motivo, se recomienda migrar las cargas de trabajo de las VM originales de la serie H y las VM de promoción de la serie H a nuestras ofertas más recientes.

Las máquinas virtuales Azure [HC](hc-series.md), [HBv2](hbv2-series.md), [HBv3](hbv3-series.md), [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md), [Ev4](ev4-esv4-series.md) y [Eav4](eav4-easv4-series.md) tienen mayor ancho de banda de memoria, funcionalidades de redes mejoradas y una mejor relación costo/rendimiento en varias cargas de trabajo de HPC. El 31 de agosto de 2022, retiraremos estos tamaños de máquina virtual de Azure de la serie H:

- H8
- H8m
- H16
- H16r
- H16m
- H16mr
- H8 Promo
- H8m Promo
- H16 Promo
- H16r Promo
- H16m Promo
- H16mr Promo

## <a name="how-does-the-h-series-migration-affect-me"></a>¿Cómo me afecta la migración de la serie H?

Después del 31 de agosto de 2022, las suscripciones de VM de la serie H restantes de la lista anterior se establecerán en un estado desasignado. Dejarán de funcionar y ya no incurrirán en cargos de facturación.

La retirada de los tamaños de VM actuales solo afecta a los tamaños de VM de la [serie H](h-series.md), incluidos los de promoción de la serie H.

## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar?

Tendrá que cambiar el tamaño o desasignar las VM de la serie H. Se recomienda migrar las cargas de trabajo de las VM originales de la serie H y las VM de promoción de la serie H a nuestras ofertas más recientes.

**Cargas de trabajo HPC:** Las VM [HC](hc-series.md), [HBv2](hbv2-series.md) y [HBv3](hbv3-series.md) ofrecen niveles considerablemente superiores de rendimiento de la carga de trabajo de HPC y rentabilidad debido a:

- Grandes mejoras en la arquitectura de núcleos de CPU.
- Mayor ancho de banda de memoria.
- Memorias caché L3 más grandes.
- Compatibilidad mejorada con hardware y software de redes InfiniBand en comparación con la serie H.

Como resultado, las series HC, HBv2 y HBv3 ofrecerán en general un rendimiento considerablemente mejor por unidad de costo (maximizando el rendimiento por una cantidad fija de gasto) y el costo por rendimiento (minimizando el costo por una cantidad fija de rendimiento).

**Cargas de trabajo de uso general:** Las VM [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md) y Dv5 ofrecen un rendimiento de CPU igual o superior con recuentos de núcleos idénticos o mayores, una cantidad comparable de memoria por núcleo de CPU física, mejores funcionalidades de Redes de Azure y un menor costo general.

**Cargas de trabajo optimizadas para memoria:** Las VM [Ev4](ev4-esv4-series.md), [Eav4](eav4-easv4-series.md) y Ev5 ofrecen un rendimiento de CPU igual o superior con recuentos de núcleos idénticos o mayores, una cantidad comparable de memoria por núcleo de CPU física, mejores funcionalidades de Redes de Azure y un menor costo general.

Las VM de la [serie H](h-series.md) y la VM de promoción de la serie H no se retirarán antes de septiembre de 2022. Se proporciona esta guía de antemano para proporcionarle una ventana larga para evaluar, planear y ejecutar la migración.

### <a name="migration-steps"></a>Pasos de migración

1. Elija una serie y un tamaño para la migración.
1. Obtenga la cuota de la serie de máquinas virtuales de destino.
1. Cambie el tamaño actual de la serie N de VM al tamaño objetivo.

### <a name="breaking-changes"></a>Cambios importantes

Si usa tamaños de VM de la serie H que exponen una interfaz de redes InfiniBand (por ejemplo, aquellos con una "r" en el nombre de tamaño de la VM) y quiere que los tamaños nuevos de VM también admitan redes InfiniBand, ya no podrá usar imágenes de sistema operativo heredadas compatibles con controladores InfiniBand integrados (CentOS 7.4 y versiones anteriores, Windows Server 2012).

En su lugar, use imágenes de sistemas operativos modernos como las que están disponibles en Azure Marketplace, que admiten sistemas operativos modernos (CentOS 7.5 y versiones más recientes, Windows Server 2016 y más recientes) y controladores OFED estándar. Consulte la [pila de software compatible](hbv3-series.md#get-started), que incluye el sistema operativo admitido para los tamaños de VM respectivos.

### <a name="get-a-quota-for-the-target-vm-family"></a>Obtención de la cuota de la familia de máquinas virtuales de destino

Siga la guía para [solicitar un aumento de la cuota de vCPU por familia de máquinas virtuales](../azure-portal/supportability/per-vm-quota-requests.md).

### <a name="resize-the-current-vm"></a>Cambio de tamaño de la máquina virtual actual

Puede [cambiar el tamaño de la máquina virtual](resize-vm.md).
