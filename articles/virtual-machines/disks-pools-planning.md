---
title: Optimización del rendimiento de los grupos de discos de Azure (versión preliminar)
description: Aprenda a obtener el máximo rendimiento de un grupo de discos de Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e3ffadcdc77d09d04518cdf577208250c2906407
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469893"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Guía de planeamiento de grupos de discos de Azure (versión preliminar)

Es importante comprender los requisitos de rendimiento de la carga de trabajo antes de implementar un grupo de discos de Azure (versión preliminar). Determinar los requisitos de antemano le permite obtener el máximo rendimiento del grupo de discos. El rendimiento de un grupo de discos viene determinado por tres factores principales y, si ajusta cualquiera de ellos, se ajustará el rendimiento del grupo de discos:

- Objetivo de escalabilidad del grupo de discos
- Los objetivos de escalabilidad de los discos individuales contenidos en el grupo de discos
- La conexión de red entre las máquinas cliente y el grupo de discos.

## <a name="optimize-for-low-latency"></a>Optimización para una latencia baja

Si va a priorizar una latencia baja, agregue discos Ultra al grupo de discos. Los discos Ultra proporcionan una latencia de disco inferior a milisegundos. Para obtener la menor latencia posible, también debe evaluar la configuración de red y asegurarse de que usa la ruta de acceso óptima. Considere la posibilidad de utilizar [ExpressRoute FastPath](../expressroute/about-fastpath.md) para minimizar la latencia de red.

## <a name="optimize-for-high-throughput"></a>Optimización para lograr un alto rendimiento

Si va a priorizar el rendimiento, empiece por evaluar el número de grupos de discos necesarios para conseguir los objetivos de rendimiento. Una vez que tenga los objetivos necesarios, puede dividirlo entre cada disco individual y sus tipos. Actualmente, se pueden usar dos tipos de disco en un grupo de discos, SSD prémium y Ultra. Los discos SSD prémium pueden proporcionar un alto número de IOPS y MBps que se escalan con su capacidad de almacenamiento, mientras que los discos Ultra pueden escalar su rendimiento independientemente de su capacidad de almacenamiento. Seleccione el tipo que mejor se adapte al equilibrio entre costo y rendimiento. Además, confirme que la conectividad de red entre los clientes y el grupo de discos no es un cuello de botella, especialmente en relación con el rendimiento.


## <a name="use-cases"></a>Casos de uso

En la tabla siguiente se enumeran algunos casos de uso típicos de grupos de discos con Azure VMware Solution y una configuración recomendada.


|Casos de uso de Azure VMware Solution  |Tipo de disco sugerido  |Configuración de red sugerida  |
|---------|---------|---------|
|Bloquee el almacenamiento de los conjuntos de trabajo activos, como una extensión de vSAN para Azure VMware Solution.     |Discos Ultra         |Use la puerta de enlace de red virtual de ExpressRoute: Rendimiento Ultra o ErGw3AZ (10 Gbps) para conectar la red virtual del grupo de discos a la nube de Azure VMware Solution y habilitar FastPath para minimizar la latencia de red.         |
|Niveles: datos de niveles a los que se accede con poca frecuencia desde la instancia de vSAN para Azure VMware Solution al grupo de discos.     |SSD Premium         |Use la puerta de enlace de red virtual de ExpressRoute: Estándar (1 Gbps) o Alto rendimiento (2 Gbps) para conectar la red virtual del grupo de discos a la nube de Azure VMware Solution.         |
|Almacenamiento de datos para el sitio de recuperación ante desastres en Azure VMware Solution: replique los datos del entorno de VMware local o principal al grupo de discos como sitio secundario.     |SSD Premium         |Use la puerta de enlace de red virtual de ExpressRoute: Estándar (1 Gbps) o Alto rendimiento (2 Gbps) para conectar la red virtual del grupo de discos a la nube de Azure VMware Solution.         |

Consulte [Lista de comprobación del planeamiento de red para Azure VMware Solution](../azure-vmware/tutorial-network-checklist.md) para planear la configuración de la red y conocer otros aspectos de Azure VMware Solution.

## <a name="disk-pool-scalability-and-performance-targets"></a>Objetivos de escalabilidad y rendimiento del grupo de discos

|Resource  |Límite  |
|---------|---------|
|Número máximo de discos por grupo de discos|8|
|Número máximo de IOPS por grupo de discos|25.600|
|Número máximo de Mbps por grupo de discos|384|

El ejemplo siguiente debería proporcionarle una idea de cómo funcionan conjuntamente los distintos factores de rendimiento:

Por ejemplo, si agrega dos discos SSD prémium de 1 TiB (P30, con un destino aprovisionado de 5000 IOPS y 200 Mbps) a un grupo de discos, puede lograr 2 x 5000 = 10 000 IOPS. Sin embargo, el rendimiento se vería limitado en 384 MBps por el grupo de discos. Para superar este límite de 384 MBps, puede implementar más grupos de discos para escalar horizontalmente para obtener un rendimiento adicional. El rendimiento de la red limitará la eficacia del escalado horizontal.

## <a name="availability"></a>Disponibilidad

Los grupos de discos se encuentran actualmente en versión preliminar y no deben usarse para cargas de trabajo de producción.

Si el grupo de discos deja de ser accesible para la nube de Azure VMware Solution por cualquier motivo, experimentará lo siguiente:

- Ya no se podrá acceder a todos los almacenes de datos asociados al grupo de discos.
- Todas las máquinas virtuales de VMware hospedadas en esta nube de Azure VMware Solution que está utilizando los almacenes de datos afectados estarán en un estado incorrecto.
- El estado de los clústeres de esta nube de Azure VMware Solution no se ve afectado, excepto en una operación: no podrá poner un host en modo de mantenimiento. Azure VMware Solution controlará este error e intentará la recuperación mediante la desconexión de los almacenes de datos afectados.

Si experimenta este estado, realice estos [pasos](disks-pools-troubleshoot.md#recover-a-disk-pool-or-an-iscsi-target) para recuperar el grupo de discos.

## <a name="next-steps"></a>Pasos siguientes

[Implementación de un grupo de discos](disks-pools-deploy.md).
