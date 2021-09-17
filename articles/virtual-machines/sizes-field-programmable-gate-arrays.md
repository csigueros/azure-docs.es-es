---
title: Tamaños de máquina virtual de Azure para matrices de puertas programables (FPGA)
description: Enumera los distintos tamaños optimizados para FPGA disponibles para las máquinas virtuales en Azure. Se proporciona información sobre el número de unidades vCPU, discos de datos y NIC, así como sobre el rendimiento de almacenamiento y el ancho de banda de red para los tamaños de esta serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: c421c30e9a8f117806e9e93b2df5154bd881e7c2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697935"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>Tamaños de máquinas virtuales optimizados para FPGA

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Los tamaños de VM optimizados para FPGA son máquinas virtuales especializadas con una o varias matrices FPGA. Estos tamaños están diseñados para las cargas de trabajo de procesos intensivos. En este artículo se proporciona información sobre el número y el tipo de matrices FPGA, CPU virtuales, discos de datos y tarjetas NIC. El ancho de banda de red y el rendimiento del almacenamiento también se incluyen para cada tamaño de esta agrupación.

- Los tamaños de la [serie NP](np-series.md) están optimizados para cargas de trabajo, que incluyen la inferencia de aprendizaje automático, la transcodificación de vídeo, y el análisis y la búsqueda de bases de datos. La serie NP está equipada con aceleradores Xilinx U250.


## <a name="deployment-considerations"></a>Consideraciones de la implementación

- Para ver la disponibilidad de máquinas virtuales de la serie N, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

- Las máquinas virtuales de la serie N solo se pueden implementar en el modelo de implementación de Resource Manager.

- Si desea implementar más de un pequeño número de máquinas virtuales de la serie N, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.

- Es posible que necesite aumentar la cuota de núcleos (por región) de la suscripción de Azure y la cuota independiente para núcleos NP. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../azure-portal/supportability/how-to-create-azure-support-request.md) sin cargo alguno. Los límites predeterminados pueden variar según la categoría de suscripción.

## <a name="other-sizes"></a>Otros tamaños

- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Proceso acelerado de la GPU](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [Generaciones anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.
