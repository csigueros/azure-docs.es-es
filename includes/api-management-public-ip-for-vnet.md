---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 413328496d35346ef6b7219bbed40755160ce9eb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288406"
---
* **Una [dirección IPv4 pública](../articles/virtual-network/ip-services/public-ip-addresses.md#standard) de SKU estándar**. El recurso de la dirección IP pública es necesario al configurar la red virtual para el acceso externo o interno. Con una red virtual interna, la dirección IP pública solo se usa para las operaciones de administración. Obtenga más información sobre las [direcciones IP de API Management](../articles/api-management/api-management-howto-ip-addresses.md).

  * La dirección IP debe estar en la misma región y suscripción que la instancia de API Management y la red virtual.

  * El valor de la dirección IP se asigna como la dirección IPv4 pública virtual de la instancia de API Management en esa región. 

  * Al cambiar de una red virtual externa a otra interna (o viceversa), o al cambiar las subredes de la red o actualizar las zonas de disponibilidad para la instancia de API Management, debe configurar una dirección IP pública diferente.