---
title: Availability Zones en Azure Monitor
description: Availability Zones en Azure Monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/18/2021
ms.custom: references_regions
ms.openlocfilehash: 6e10ace3ca0932cf5803719429a68b89a4118dfa
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059328"
---
# <a name="availability-zones-in-azure-monitor"></a>Availability Zones en Azure Monitor

La característica [Azure Availability Zones](../../availability-zones/az-overview.md) protege las aplicaciones y los datos de los errores del centro de datos y puede proporcionar resistencia para características de Azure Monitor, como Application Insights y cualquier otra característica que se base en un área de trabajo de Log Analytics. Cuando un área de trabajo está vinculada a una zona de disponibilidad, permanece activa y operativa incluso si un centro de datos específico no funciona correctamente o está completamente fuera de servicio, dependiendo de la disponibilidad de otras zonas de la región. No es necesario hacer nada para cambiar a una zona alternativa, ni siquiera tener en cuenta el incidente. 


## <a name="regions"></a>Regions
Consulte [Regiones y Availability Zones en Azure](https://azure.microsoft.com/global-infrastructure/geographies/#geographies) para más información sobre las regiones de Azure que tienen zonas de disponibilidad. Actualmente, Azure Monitor admite las siguientes regiones. 

- Este de EE. UU. 2
- Oeste de EE. UU. 2

## <a name="dedicated-clusters"></a>Clústeres dedicados
La compatibilidad de Azure Monitor con zonas de disponibilidad requiere un área de trabajo de Log Analytics vinculada a un [clúster dedicado de Azure Monitor](logs-dedicated-clusters.md). Los clústeres dedicados son una opción de implementación que habilita las funcionalidades avanzadas para los registros de Azure Monitor que incluyen zonas de disponibilidad.

No todos los clústeres dedicados pueden usar zonas de disponibilidad. Los clústeres dedicados creados después de mediados de octubre de 2020 se pueden establecer para admitir zonas de disponibilidad cuando se crean. Los nuevos clústeres creados después de esa fecha se habilitan de manera predeterminada para las zonas de disponibilidad en las regiones donde Azure Monitor los admite.


> [!NOTE]
> Los recursos de Application Insights pueden usar zonas de disponibilidad solo si se basan en el área de trabajo y el área de trabajo usa un clúster dedicado. Los recursos de instancias de Application Insights clásicas no pueden usar zonas de disponibilidad.


## <a name="determine-current-cluster-for-your-workspace"></a>Determinación del clúster actual para el área de trabajo
Para determinar el estado actual del vínculo del área de trabajo, use la [CLI, PowerShell o REST](logs-dedicated-clusters.md#check-workspace-link-status) para recuperar los [detalles del clúster](logs-dedicated-clusters.md#check-cluster-provisioning-status). Si el clúster usa una zona de disponibilidad, tendrá una propiedad denominada `isAvailabilityZonesEnabled` con un valor de `true`. Una vez creado un clúster, esta propiedad no se puede modificar.

## <a name="create-dedicated-cluster-with-availability-zone"></a>Creación de un clúster dedicado con zona de disponibilidad
Mueva el área de trabajo a una zona de disponibilidad mediante la [creación de un nuevo clúster dedicado](logs-dedicated-clusters.md#create-a-dedicated-cluster) en una región que admita zonas de disponibilidad. El clúster se habilitará automáticamente para las zonas de disponibilidad. A continuación, [vincule el área de trabajo al nuevo clúster](logs-dedicated-clusters.md#link-a-workspace-to-a-cluster).

> [!IMPORTANT]
> La zona de disponibilidad se define en el clúster en el momento de la creación y no se puede modificar.

La transición a un nuevo clúster puede ser un proceso gradual. No quite el clúster anterior hasta que se haya purgado de los datos. Por ejemplo, si la retención del área de trabajo se establece en 60 días, es posible que desee mantener el clúster antiguo en ejecución durante ese período antes de quitarlo.

Las consultas en el área de trabajo consultarán ambos clústeres según sea necesario para proporcionarle un único conjunto de resultados unificado. Esto significa que todas las características de Azure Monitor que dependen del área de trabajo, como los libros y paneles, seguirán obteniendo el conjunto de resultados completo y unificado basado en los datos de ambos clústeres.

## <a name="billing"></a>Facturación
El uso de [un clúster dedicado tiene un costo](logs-dedicated-clusters.md#create-a-dedicated-cluster). Requiere una reserva de capacidad diaria de 500 GB. 

Si ya tiene un clúster dedicado y decide conservarlo para acceder a sus datos, se le cobrará por ambos clústeres dedicados. A partir del 4 de agosto de 2021, la reserva de capacidad mínima necesaria para clústeres dedicados se reduce de 1000 GB/día a 500 GB/día, por lo que se recomienda aplicar ese mínimo al clúster anterior para reducir los cargos.

El nuevo clúster no se factura durante su primer día para evitar la doble facturación durante la configuración. Solo los datos ingeridos antes de que finalice la migración se seguirán facturando en la fecha de migración. 


## <a name="next-steps"></a>Pasos siguientes

- Consulte [Uso de consultas en Azure Monitor Log Analytics](queries.md) para ver cómo interactúan los usuarios con los paquetes de consultas en Log Analytics.

