---
title: Administración de las interrupciones de servicios de Azure que afectan a Azure Cloud Services (clásico)
description: Descubra qué hacer en caso de que se produzca una interrupción del servicio de Azure que afecte a Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 9b76dc82cac030f725882613d4168abd2ebafff9
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113086192"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services-classic"></a>Qué hacer en caso de una interrupción del servicio de Azure que afecte a Azure Cloud Services (clásico)

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

En Microsoft, hacemos todo lo posible para garantizar que nuestros servicios estén siempre disponibles cuando los necesite. En ocasiones, debido a factores externos que escapan de nuestro control, se producen interrupciones de servicio no planeadas.

Microsoft proporciona Acuerdos de Nivel de Servicio para sus servicios como un compromiso en cuanto al tiempo de actividad y la conectividad. Puede encontrar el Acuerdo de Nivel de Servicio para los diferentes servicios de Azure en [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

Azure ya integra en su plataforma muchas características que admiten aplicaciones de alta disponibilidad. Para obtener más información sobre estos servicios, lea [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](/azure/architecture/framework/resiliency/backup-and-recovery).

En este artículo se expone un escenario real de recuperación ante desastres en el que toda una región experimenta una interrupción debido a un desastre natural importante o a una interrupción del servicio generalizada. Se trata de casos muy infrecuentes, pero debe estar preparado para la posibilidad de que se produzca una interrupción en toda una región. Si una región completa experimenta una interrupción del servicio, las copias con redundancia local de los datos estarían temporalmente no disponibles. Si ha habilitado la replicación geográfica, se almacenan en una región distinta tres copias adicionales de los blobs y las tablas de Azure Storage. En caso de una interrupción completa en una región o de un desastre en el que la región primaria no sea recuperable, Azure reasignará todas las entradas DNS a la región de replicación geográfica.

> [!NOTE]
> Tenga en cuenta que no tiene ningún control sobre este proceso y que solo se producirá si se da una interrupción del servicio en todo el centro de datos. Por este motivo, también debe confiar en otras estrategias de copia de seguridad específicas de la aplicación para lograr el máximo nivel de disponibilidad. Para más información, consulte [Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](/azure/architecture/framework/resiliency/backup-and-recovery). Si desea poder influir sobre su propia conmutación por error, plantéese usar un [almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)](../storage/common/storage-redundancy.md), el cual crea una copia de solo lectura de sus datos en otra región.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opción 1: uso de una implementación de copia de seguridad a través de Azure Traffic Manager
La solución de recuperación ante desastres más sólida implica mantener varias implementaciones de la aplicación en diferentes regiones para después utilizar [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) para dirigir el tráfico entre ellas. Azure Traffic Manager proporciona varios [métodos de enrutamiento](../traffic-manager/traffic-manager-routing-methods.md), por lo que puede elegir si desea administrar las implementaciones con un modelo de copia de seguridad o principal o dividir el tráfico entre ellas.

![Equilibrio de Azure Cloud Services en distintas regiones con Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Para obtener la respuesta más rápida a la pérdida de una región, es importante configurar la [Supervisión de puntos de conexión de Traffic Manager](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opción 2: implementación la aplicación en una región nueva
Mantener varias implementaciones activas tal como se describe en la opción anterior supone costos adicionales. Si su objetivo de tiempo de recuperación (RTO) es lo suficientemente flexible y tiene el código original o el paquete de Cloud Services compilado, puede crear una nueva instancia de la aplicación en otra región y actualizar los registros DNS para que apunten a la nueva implementación.

Para más información sobre cómo crear e implementar una aplicación de servicio en la nube, consulte [Creación e implementación de un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).

En función de los orígenes de datos de la aplicación, es posible que deba comprobar los procedimientos de recuperación para el origen de datos de su aplicación.

* Para los orígenes de datos de Azure Storage, consulte [Redundancia de Azure Storage](../storage/common/storage-redundancy.md) a fin de ver las opciones disponibles según el modelo de redundancia elegido para su aplicación.
* Para los orígenes de SQL Database, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la SQL Database](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) a fin de ver las opciones disponibles según el modelo de replicación elegido para su aplicación.


## <a name="option-3-wait-for-recovery"></a>Opción 3: espera a la recuperación
En este caso, no se requiere ninguna acción por su parte, pero el servicio no estará disponible hasta que se restaure la región. El estado actual del servicio se puede ver en el [panel de estado de los servicios de Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo implementar una estrategia de alta disponibilidad y recuperación ante desastres, consulte [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](/azure/architecture/framework/resiliency/backup-and-recovery).

Para obtener unos conocimientos técnicos detallados sobre las funcionalidades de una plataforma de nube, consulte [Guía técnica sobre resistencia en Azure](/azure/architecture/checklist/resiliency-per-service).