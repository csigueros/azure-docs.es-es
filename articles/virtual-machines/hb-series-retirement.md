---
title: Retirada de la serie HB
description: La retirada de la serie HB se inició el 1 de septiembre de 2021
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: f582aad0b5af8053ff7accfac2917d4d5191ce5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712148"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>Migrar las máquinas virtuales de la serie HB antes del 31 de agosto de 2024

Microsoft Azure ha introducido máquinas virtuales (VM) de las series HBv2 y HBv3 para informática de alto rendimiento (HPC). Por este motivo, se recomienda migrar las cargas de trabajo de las máquinas virtuales originales de la serie HB a nuestras ofertas más recientes.

Las VM [HBv2](hbv2-series.md) y [HBv3](hbv3-series.md) de Azure tienen un mayor ancho de banda de memoria, mejores funcionalidades de red de acceso directo remoto a memoria (RDMA), unidades de estado sólido locales más grandes y rápidas y un coste y rendimiento mejores en diferentes cargas de trabajo de HPC. Por consiguiente, retiraremos nuestros tamaños de VM de Azure de la serie HB el 31 de agosto de 2024.

## <a name="how-does-the-hb-series-migration-affect-me"></a>¿Cómo me afecta la migración de la serie HB?

Después del 31 de agosto de 2024, las suscripciones de VM de tamaño HB restantes se establecerán en un estado desasignado. Dejarán de funcionar y ya no incurrirán en cargos de facturación.

> [!NOTE]
> La retirada de estos tamaños de máquina virtual afecta únicamente a los tamaños de VM de la serie HB. Este anuncio de retirada no repercute en las VM más recientes de las series HBv2, HBv3 y HC.

## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar?

Tendrá que cambiar el tamaño o desasignar las VM de la serie H. Se recomienda migrar las cargas de trabajo de las VM originales de la serie H y las VM de promoción de la serie H a nuestras ofertas más recientes.

Las VM [HBv2](hbv2-series.md) y [HBv3](hbv3-series.md) ofrecen niveles considerablemente superiores de rendimiento de la carga de trabajo de HPC y rentabilidad debido a:

- Grandes mejoras en la arquitectura de núcleos de CPU.
- Mayor ancho de banda de memoria.
- Memorias caché L3 más grandes.
- Redes InfiniBand mejoradas en comparación con la serie HB.

Por consiguiente, las series HBv2 and HBv3 ofrecerán en general un rendimiento considerablemente mejor por unidad de costo (maximizando el rendimiento por una cantidad fija de gasto) y el costo por rendimiento (minimizando el costo por una cantidad fija de rendimiento).

Todas las regiones que contienen VM de la serie HB tienen VM de las series HBv2 y HBv3. Las cargas de trabajo existentes que se ejecutan en VM de la serie HB se pueden migrar sin preocuparse por la ubicación geográfica o el acceso a más servicios en esas regiones.

Las VM de la [serie HB](hb-series.md) no se retirarán hasta septiembre de 2024. Se proporciona esta guía de antemano para proporcionarle una ventana larga para evaluar, planear y ejecutar la migración.

### <a name="recommendations-for-workload-migration-from-hb-series-vms"></a>Recomendaciones para migrar las cargas de trabajo de VM de la serie HB

| Tamaño de la máquina virtual actual | Tamaño de la máquina virtual de destino | Diferencia en la especificación  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |CPU más nueva: AMD Rome y MiIan (+20-30 % de IPC) <br> Memoria: hasta el doble de RAM  <br> Ancho de banda de memoria: hasta un 30 % más <br> InfiniBand: 200 Gb de HDR (el doble de ancho de banda) <br> Máximo de discos de datos: hasta 32 (+8) |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |CPU más nueva: AMD Rome y MiIan (+20-30 % de IPC) <br> Memoria: hasta el doble de RAM  <br>  Ancho de banda de memoria: hasta un 30 % más <br> InfiniBand: 200 Gb de HDR (el doble de ancho de banda) <br> Máximo de discos de datos: hasta 32 (+8) |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |CPU más nueva: AMD Rome y MiIan (+20-30 % de IPC) <br> Memoria: hasta el doble de RAM <br> Ancho de banda de memoria: hasta un 30 % más <br> InfiniBand: 200 Gb de HDR (el doble de ancho de banda) <br> Máximo de discos de datos: hasta 32 (+8) |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |CPU más nueva: AMD Rome y MiIan (+20-30 % de IPC) <br> Memoria: hasta el doble de RAM <br> Ancho de banda de memoria: hasta un 30 % más <br> InfiniBand: 200 Gb de HDR (el doble de ancho de banda) <br> Máximo de discos de datos: hasta 32 (+8) |

### <a name="migration-steps"></a>Pasos de migración

1. Elija una serie y un tamaño para la migración.
1. Obtenga la cuota de la serie de máquinas virtuales de destino.
1. Cambie el tamaño actual de la máquina virtual de la serie HB al tamaño de destino.

### <a name="get-a-quota-for-the-target-vm-family"></a>Obtención de la cuota de la familia de máquinas virtuales de destino

Siga la guía para [solicitar un aumento de la cuota de vCPU por familia de máquinas virtuales](../azure-portal/supportability/per-vm-quota-requests.md).

### <a name="resize-the-current-vm"></a>Cambio de tamaño de la máquina virtual actual

Puede [cambiar el tamaño de la máquina virtual](resize-vm.md).
