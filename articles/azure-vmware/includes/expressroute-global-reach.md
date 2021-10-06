---
title: Redes y conectividad de Azure VMware Solution
description: Descripción de redes y conectividad de Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: c80e256691066239d89b101f206f758725809fad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909427"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[Global Reach de ExpressRoute](../../expressroute/expressroute-global-reach.md) se usa para conectar las nubes privadas a entornos locales. Conecta circuitos directamente en el nivel de Microsoft Enterprise Edge (MSEE). La conexión requiere una red virtual con un circuito ExpressRoute en el entorno local en su suscripción.  El motivo es que las puertas de enlace de red virtual (puertas de enlace de ExpressRoute) no pueden hacer transitar el tráfico, lo que significa que puede conectar dos circuitos a la misma puerta de enlace, pero no se enviará tráfico de un circuito al otro.

Cada entorno de Azure VMware Solution es su propia región de ExpressRoute (su propio dispositivo MSEE virtual), que le permite conectar Global Reach a la ubicación de emparejamiento "local".  Permite conectar varias instancias de Azure VMware Solution de una región a la misma ubicación de emparejamiento. 

>[!NOTE]
>Para las ubicaciones en las que Global Reach de ExpressRoute no está habilitado, por ejemplo, debido a normativas locales, tiene que crear una solución de enrutamiento mediante máquinas virtuales IaaS de Azure. Para ver algunos ejemplos, consulte [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking).

