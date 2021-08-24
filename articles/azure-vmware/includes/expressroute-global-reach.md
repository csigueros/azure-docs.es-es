---
title: Redes y conectividad de Azure VMware Solution
description: Descripción de redes y conectividad de Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 569beec244e2ccf80aae3e4bb3f7a3e2cf65980a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070745"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[Global Reach de ExpressRoute](../../expressroute/expressroute-global-reach.md) se usa para conectar las nubes privadas a entornos locales. Conecta circuitos directamente en el nivel de Microsoft Enterprise Edge (MSEE), por lo que la conexión requiere una red virtual (vNet) con un circuito de ExpressRoute en un entorno local en la suscripción.  El motivo es que las puertas de enlace de red virtual (puertas de enlace de ExpressRoute) no pueden hacer transitar el tráfico, lo que significa que puede conectar dos circuitos a la misma puerta de enlace, pero no se enviará tráfico de un circuito al otro.

>[!NOTE]
>Para las ubicaciones en las que Global Reach de ExpressRoute no está habilitado, por ejemplo, debido a normativas locales, tiene que crear una solución de enrutamiento mediante máquinas virtuales IaaS de Azure. Para ver algunos ejemplos, consulte [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking).

