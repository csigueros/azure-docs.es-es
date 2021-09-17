---
title: Retirada de la serie HB
description: Inicio de la retirada de la serie HB el 1 de septiembre de 2021
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 8a718f315324638f834316eb6dae46a9497ec48e
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515848"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>Migrar las máquinas virtuales de la serie HB antes del 31 de agosto de 2024
Tras la aparición de las máquinas virtuales de las series HBv2 y HBv3 de Microsoft Azure para una informática de alto rendimiento, se recomienda migrar cualquier carga de trabajo de máquina virtual original de la serie HB a nuestras ofertas más recientes.  

Las máquinas virtuales [HBv2](hbv2-series.md) y [HBv3](hbv3-series.md) de Azure tienen un mayor ancho de banda de memoria, mejores funcionalidades de red de acceso directo remoto a memoria (RDMA), unidades de estado sólido locales más grandes y rápidas y una mejor relación coste/rendimiento en una amplia gama de cargas de trabajo de HPC. Por ello, el 31 de agosto de 2024 retiraremos los tamaños de máquina virtual de Azure de la serie HB.

## <a name="how-does-the-hb-series-migration-affect-me"></a>¿Cómo me afecta la migración de la serie HB?  

Después del 31 de agosto de 2024, las suscripciones de máquinas virtuales de tamaño HB restantes se establecerán en un estado desasignado, dejarán de funcionar y ya no incurrirán en cargos de facturación.  
> [!NOTE]
> La retirada de estos tamaños de máquina virtual afecta únicamente a los tamaños de máquina virtual de la serie HB. Este anuncio de retirada no repercute en las máquinas virtuales más recientes de las series HBv2, HBv3 y HC. 

## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar?  

Deberá cambiar el tamaño o desasignar las máquinas virtuales de la serie H. Se recomienda migrar las cargas de trabajo de las máquinas virtuales originales de la serie H (incluidas las de la promoción de la serie H) a nuestras ofertas más recientes.

Las máquinas virtuales [HBv2](hbv2-series.md)y [HBv3](hbv3-series.md) ofrecen niveles considerablemente mayores de rendimiento y rentabilidad de las cargas de trabajo de HPC gracias a la tremenda mejora de la arquitectura del núcleo de CPU, a un mayor ancho de banda de memoria, a memorias caché L3 más grandes y a mejores redes InfiniBand en comparación con la serie HB. Como resultado, las series HBv2 y HBv3 proporcionarán por lo general un rendimiento considerablemente mejor por unidad de coste (es decir, se maximiza el rendimiento según un importe fijo), así como el coste por rendimiento (es decir, se minimiza el coste de una cantidad fija de rendimiento).

Todas las regiones que contienen máquinas virtuales de la serie HB contienen máquinas virtuales de las series HBv2 y HBv3, por lo que las cargas de trabajo existentes que se ejecutan en máquinas virtuales de la serie HB se pueden migrar sin tener que preocuparse por la ubicación geográfica ni por el acceso a otros servicios en esas regiones. 

Las máquinas virtuales de la [serie HB](hb-series.md) no se retirarán hasta septiembre de 2024, y ofrecemos esta guía de con bastante antelación para que los clientes dispongan de tiempo suficiente para evaluar, planear y llevar a cabo la migración. 

### <a name="recommendations-for-workload-migration-from-hb-series-virtual-machines"></a>Recomendaciones para migrar las cargas de trabajo de máquinas virtuales de la serie HB 

| Tamaño de la máquina virtual actual | Tamaño de la máquina virtual de destino | Diferencia en la especificación  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |CPU más nueva: AMD Rome y MiIan (+20-30 % de IPC) <br> Memoria: hasta el doble de RAM  <br> Ancho de banda de memoria: hasta un 30 % más <br> InfiniBand: 200 Gb de HDR (el doble de ancho de banda) <br> Máximo de discos de datos: hasta 32 (+8) |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |CPU más nueva: AMD Rome y MiIan (+20-30 % de IPC) <br> Memoria: hasta el doble de RAM  <br>  Ancho de banda de memoria: hasta un 30 % más <br> InfiniBand: 200 Gb de HDR (el doble de ancho de banda) <br> Máximo de discos de datos: hasta 32 (+8) |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |CPU más nueva: AMD Rome y MiIan (+20-30 % de IPC) <br> Memoria: hasta el doble de RAM <br> Ancho de banda de memoria: hasta un 30 % más <br> InfiniBand: 200 Gb de HDR (el doble de ancho de banda) <br> Máximo de discos de datos: hasta 32 (+8) |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |CPU más nueva: AMD Rome y MiIan (+20-30 % de IPC) <br> Memoria: hasta el doble de RAM <br> Ancho de banda de memoria: hasta un 30 % más <br> InfiniBand: 200 Gb de HDR (el doble de ancho de banda) <br> Máximo de discos de datos: hasta 32 (+8) |


### <a name="migration-steps"></a>Pasos de migración 
1. Elija una serie y un tamaño para la migración. 
2. Obtenga la cuota de la serie de máquinas virtuales de destino. 
3. Cambie el tamaño actual de la máquina virtual de la serie HB al tamaño de destino. 


### <a name="get-quota-for-the-target-vm-family"></a>Obtener la cuota de la familia de máquinas virtuales de destino 

Siga la guía para [solicitar un aumento de la cuota de vCPU por familia de máquinas virtuales](../azure-portal/supportability/per-vm-quota-requests.md).


### <a name="resize-the-current-virtual-machine"></a>Cambiar el tamaño de la máquina virtual actual
Puede [cambiar el tamaño de la máquina virtual con PowerShell](./windows/resize-vm.md), con la [CLI de Azure](./linux/change-vm-size.md) o a través de Azure Portal.
