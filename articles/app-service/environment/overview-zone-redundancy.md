---
title: Redundancia de zona en App Service Environment
description: Información general sobre la redundancia de zona en App Service Environment.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: 3ce5a0925ae4c1dc1bc33fec73987455d612d661
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525746"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Compatibilidad de zonas de disponibilidad con instancias de App Service Environment

> [!NOTE]
> Este artículo trata sobre App Service Environment v3, que se usa con planes de App Service v2 aislados.
>

App Service Environment (ASE) se puede implementar en [Availability Zones (AZ)](../../availability-zones/az-overview.md). Esta arquitectura también se conoce como redundancia de zona. Cuando se configura una instancia de ASE con redundancia de zona, la plataforma distribuye automáticamente las instancias del plan de App Service en la instancia de ASE entre las tres zonas de la región seleccionada. Si se especifica una capacidad mayor que tres y el número de instancias es divisible entre tres, las instancias se propagarán de manera uniforme. De lo contrario, los recuentos de instancias más allá de 3*N se reparten entre las dos zonas restantes.

La redundancia de zona se configura al crear la instancia de ASE y todos los planes de App Service creados en esa instancia de ASE tendrán redundancia de zona. La redundancia de zona solo se puede especificar al crear una *nueva* instancia de App Service Environment. Actualmente solo se admite la redundancia de zona en un [subconjunto de regiones](./overview.md#regions).

Si una zona está fuera de servicio, la plataforma App Service detectará las instancias perdidas e intentará encontrar automáticamente nuevas instancias de reemplazo. Si también ha configurado la escalabilidad automática y decide que se necesitan más instancias, la escalabilidad automática también emitirá una solicitud a App Service para agregar más instancias (el comportamiento de la escalabilidad automática es independiente del de la plataforma App Service). Es importante tener en cuenta que no hay ninguna garantía de que las solicitudes de instancias en un escenario de zona fuera de servicio se realicen correctamente, ya que el rellenado de instancias perdidas se realiza en función del mejor esfuerzo. La solución recomendada es escalar los planes de App Service para tener en cuenta la pérdida de una zona.

Las aplicaciones implementadas en una instancia de ASE con redundancia de zona se seguirán ejecutando y atenderán el tráfico incluso si otras zonas de la misma región sufren una interrupción. Pero es posible que los comportamientos que no sean de tiempo de ejecución, incluidos el escalado del plan de App Service y la creación, configuración y publicación de aplicaciones, se puedan ver afectados por una interrupción en otras zonas de disponibilidad. La redundancia de zona para App Service Environment solo garantiza un tiempo de actividad continuado para las aplicaciones implementadas.

Cuando la plataforma App Service asigna instancias a un plan de App Service con redundancia de zona en una instancia de ASE, usa el [mejor esfuerzo de equilibrio de zona que ofrece la instancia de Azure Virtual Machine Scale Sets subyacente](../../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing). Un plan de App Service se "equilibrará" si cada zona tiene el mismo número de instancias o +/- 1 instancia en todas las demás zonas usadas por el plan de App Service.

## <a name="pricing"></a>Precios

 Se aplica un cargo mínimo de nueve instancias del plan de App Service en una instancia de ASE con redundancia de zona. No hay ningún cargo adicional por compatibilidad con zonas de disponibilidad si tiene nueve o más instancias de plan de App Service. Si tiene menos de nueve instancias (de cualquier tamaño) en los planes de App Service en la instancia de ASE con redundancia de zona, la diferencia entre nueve y el número de instancias en ejecución se cobra como instancias de Windows I1v2 adicionales.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre las [zonas de disponibilidad](../../availability-zones/az-overview.md)
