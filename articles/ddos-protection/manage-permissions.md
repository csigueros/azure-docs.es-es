---
title: Permisos del plan de Azure DDoS Protection Plan
description: Aprenda a administrar el permiso en un plan de protección.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 30a081ab25e5c7918e7253649983ddea54ddf2b4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439315"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Administración de planes de DDoS Protection: permisos y restricciones

El plan de protección contra DDoS funciona en todas las regiones y suscripciones. El mismo plan se puede vincular a las redes virtuales de otras suscripciones en diferentes regiones, a través de su inquilino. La suscripción a la que está asociado el plan incurre en la factura mensual recurrente del plan, además de cargos de uso por encima del límite, en caso de que la cantidad de direcciones IP públicas protegidas supere las 100. Para más información sobre los precios de DDoS, consulte los [detalles de precios](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Prerrequisitos

- Para poder completar los pasos de este tutorial, primero debe crear un [plan de protección Estándar de Azure DDoS](manage-ddos-protection.md).

## <a name="permissions"></a>Permisos

Para trabajar con planes de protección contra DDoS, su cuenta debe estar asignada al rol de [colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un rol [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenga asignadas las acciones adecuadas que se muestran en la tabla siguiente:

| Acción                                            | Nombre                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Obtención de un plan de protección contra DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Creación o actualización de un plan de protección contra DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Eliminación de un plan de protección contra DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Unión a un plan de protección contra DDoS              |

Para habilitar la protección contra DDoS para una red virtual, su cuenta también debe estar asignada a las [acciones para redes virtuales](../virtual-network/manage-virtual-network.md#permissions) adecuadas.

> [!IMPORTANT]
> Una vez que se haya habilitado un plan de DDoS Protection en una red virtual, las operaciones posteriores en esa red virtual seguirán requiriendo el permiso de acción `Microsoft.Network/ddosProtectionPlans/join/action`.

## <a name="azure-policy"></a>Azure Policy

En la mayoría de las organizaciones, no es necesario crear más de un plan. Un plan no puede moverse entre suscripciones. Si desea cambiar la suscripción en que se encuentra un plan, debe eliminar el plan existente y crear uno nuevo.

En el caso de los clientes que tienen varias suscripciones y que desean asegurarse de que se implementa un plan único para el control de costos, puede usar Azure Policy para [restringir la creación de planes de Azure DDoS Protection Standard](https://aka.ms/ddosrestrictplan). Esta directiva bloqueará la creación de los planes de DDoS, a menos que la suscripción se haya marcado previamente como una excepción. Esta directiva también muestra una lista de todas las suscripciones que tienen un plan de DDoS implementado pero no deberían y las marca como un incumplimiento.


## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo ver y configurar la telemetría del plan de protección contra DDoS, continúe con los tutoriales.

> [!div class="nextstepaction"]
> [Visualización y configuración de la telemetría de protección contra DDoS](telemetry.md)
