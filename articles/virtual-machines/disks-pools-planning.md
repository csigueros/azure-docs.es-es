---
title: Optimización del rendimiento de los grupos de discos de Azure (versión preliminar)
description: Aprenda a obtener el máximo rendimiento de un grupo de discos de Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 801930dc58993a81c29969f602da64f201919bd0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428014"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Guía de planeamiento de grupos de discos de Azure (versión preliminar)

Es importante comprender los requisitos de rendimiento de la carga de trabajo antes de implementar un grupo de discos de Azure (versión preliminar). Determinar los requisitos de antemano le permite obtener el máximo rendimiento del grupo de discos. El rendimiento de un grupo de discos viene determinado por tres factores principales y, si ajusta cualquiera de ellos, se ajustará el rendimiento del grupo de discos:

- Objetivo de escalabilidad del grupo de discos
- Los objetivos de escalabilidad de los discos individuales contenidos en el grupo de discos
- La conexión de red entre las máquinas cliente y el grupo de discos.

## <a name="optimize-for-low-latency"></a>Optimización para una latencia baja

Si va a priorizar una latencia baja, agregue discos Ultra al grupo de discos. Los discos Ultra proporcionan una latencia de disco inferior a milisegundos. Para obtener la menor latencia posible, también debe evaluar la configuración de red y asegurarse de que usa la ruta de acceso óptima. Considere la posibilidad de utilizar [ExpressRoute FastPath](../expressroute/about-fastpath.md) para minimizar la latencia de red.

## <a name="optimize-for-high-throughput"></a>Optimización para lograr un alto rendimiento

Si va a priorizar el rendimiento, empiece por evaluar los destinos de rendimiento de las distintas SKU del grupo de discos, así como el número de grupos de discos necesarios para conseguir los objetivos de rendimiento. Si las necesidades de rendimiento superan lo que puede proporcionar un grupo de discos prémium, puede dividir la implementación entre varios grupos de discos. A continuación, puede decidir cómo utilizar mejor el rendimiento ofrecido en un grupo de discos entre cada disco individual y sus tipos. En el caso de un grupo de discos, puede mezclar y combinar SSD prémium y estándar, o bien usar solo Ultra Disks. Los discos Ultra Disks no se pueden usar con SSD prémium o estándar. Seleccione el tipo de disco que mejor se adapte a sus necesidades. Además, confirme que la conectividad de red entre los clientes y el grupo de discos no es un cuello de botella, especialmente en relación con el rendimiento.


## <a name="use-cases"></a>Casos de uso

En la tabla siguiente se enumeran algunos casos de uso típicos de grupos de discos con Azure VMware Solution y una configuración recomendada.


|Casos de uso de Azure VMware Solution  |Tipo de disco sugerido  |SKU del grupo de discos sugerida  |Configuración de red sugerida  |
|---------|---------|---------|---------|
|Bloquee el almacenamiento de los conjuntos de trabajo activos, como una extensión de vSAN para Azure VMware Solution.     |Discos Ultra         |Premium         |Use la puerta de enlace de red virtual de ExpressRoute: Rendimiento Ultra o ErGw3AZ (10 Gbps) para conectar la red virtual del grupo de discos a la nube de Azure VMware Solution y habilitar FastPath para minimizar la latencia de red.         |
|Niveles: datos de niveles a los que se accede con poca frecuencia desde la instancia de vSAN para Azure VMware Solution al grupo de discos.     |SSD prémium, SSD estándar         |Estándar         |Use la puerta de enlace de red virtual de ExpressRoute: Estándar (1 Gbps) o Alto rendimiento (2 Gbps) para conectar la red virtual del grupo de discos a la nube de Azure VMware Solution.         |
|Almacenamiento de datos para el sitio de recuperación ante desastres en Azure VMware Solution: replique los datos del entorno de VMware local o principal al grupo de discos como sitio secundario.     |SSD prémium, SSD estándar         |Nivel Estándar o Básico         |Use la puerta de enlace de red virtual de ExpressRoute: Estándar (1 Gbps) o Alto rendimiento (2 Gbps) para conectar la red virtual del grupo de discos a la nube de Azure VMware Solution.         |


Consulte [Lista de comprobación del planeamiento de red para Azure VMware Solution](../azure-vmware/tutorial-network-checklist.md) para planear la configuración de la red y conocer otros aspectos de Azure VMware Solution.

## <a name="disk-pool-scalability-and-performance-targets"></a>Objetivos de escalabilidad y rendimiento del grupo de discos

|Resource  |Grupo de discos básico  |Grupo de discos estándar  |Grupo de discos prémium  |
|---------|---------|---------|---------|
|Número máximo de discos por grupo de discos     |16         |32         |32         |
|Número máximo de IOPS por grupo de discos     |12.800         |25.600         |51 200         |
|Número máximo de Mbps por grupo de discos     |192         |384         |768         |

El ejemplo siguiente debería proporcionarle una idea de cómo funcionan conjuntamente los distintos factores de rendimiento:

Por ejemplo, si agrega dos SSD prémium de 1 TiB (P30, con un destino aprovisionado de 5000 IOPS y 200 Mbps) a un grupo de discos estándar, puede lograr 2 x 5000 = 10 000 IOPS. Sin embargo, el rendimiento se vería limitado en 384 MBps por el grupo de discos. Para superar este límite de 384 MBps, puede implementar más grupos de discos para escalar horizontalmente para obtener un rendimiento adicional. El rendimiento de la red limitará la eficacia del escalado horizontal.

Los grupos de discos creados sin especificar la SKU en la API REST son el grupo de discos estándar, de forma predeterminada.

## <a name="availability"></a>Disponibilidad

Los grupos de discos se encuentran actualmente en versión preliminar y no deben usarse para cargas de trabajo de producción. De forma predeterminada, un grupo de discos solo admite SSD prémium y estándar. En su lugar, puede habilitar la compatibilidad con Ultra Disks en un grupo de discos, pero un grupo de discos con Ultra Disks no es compatible con SSD prémium o estándar.

Los grupos de discos con compatibilidad con SSD prémium y estándar se basan en una arquitectura de alta disponibilidad, hospedando varios de ellos el punto de conexión iSCSI. Los grupos de discos compatibles con Ultra Disks se hospedan en una implementación de instancia única.

Si el grupo de discos deja de ser accesible para la nube de Azure VMware Solution por cualquier motivo, experimentará lo siguiente:

- Ya no se podrá acceder a todos los almacenes de datos asociados al grupo de discos.
- Todas las máquinas virtuales de VMware hospedadas en la nube de Azure VMware Solution que usa los almacenes de datos afectados estarán en un estado incorrecto.
- El estado de los clústeres de la nube de Azure VMware Solution no se verá afectado, excepto en una operación: no podrá colocar un host en modo de mantenimiento. Azure VMware Solution controlará este error e intentará la recuperación mediante la desconexión de los almacenes de datos afectados.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de un grupo de discos](disks-pools-deploy.md).
- Para obtener información sobre cómo las instancias de Azure VMware Solution integran grupos de discos, consulte [Conexión de grupos de discos a hosts de Azure VMware Solution (versión preliminar)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md).
