---
title: Redes y conectividad de Azure VMware Solution
description: Descripción de redes y conectividad de Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: add146bb5b64e12b09bec89c080e1222154d4e2f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638353"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[Global Reach de ExpressRoute](../../expressroute/expressroute-global-reach.md) se usa para conectar las nubes privadas a entornos locales. Conecta circuitos directamente en el nivel de Microsoft Enterprise Edge (MSEE). La conexión requiere una red virtual con un circuito ExpressRoute en el entorno local en su suscripción.  El motivo es que las puertas de enlace de red virtual (puertas de enlace de ExpressRoute) no pueden hacer transitar el tráfico, lo que significa que puede conectar dos circuitos a la misma puerta de enlace, pero no se enviará tráfico de un circuito al otro.

Cada entorno de Azure VMware Solution es su propia región de ExpressRoute (su propio dispositivo MSEE virtual), que le permite conectar Global Reach a la ubicación de emparejamiento "local".  Permite conectar varias instancias de Azure VMware Solution de una región a la misma ubicación de emparejamiento. 

>[!NOTE]
>Para las ubicaciones en las que Global Reach de ExpressRoute no está habilitado, por ejemplo, debido a normativas locales, tiene que crear una solución de enrutamiento mediante máquinas virtuales IaaS de Azure. Para ver algunos ejemplos, consulte [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking).

