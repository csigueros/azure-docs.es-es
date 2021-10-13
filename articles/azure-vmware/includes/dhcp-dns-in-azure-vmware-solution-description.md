---
title: DHCP y DNS en la descripción de Azure VMware Solution
description: Descripción de Azure VMware Solution, DHCP y DNS.
ms.topic: include
ms.date: 05/28/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0e92259c2ece3b92b841d2be192684679d37a635
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638641"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

Las aplicaciones y cargas de trabajo que se ejecutan en un entorno de nube privada requieren servicios de resolución de nombres y DHCP para la búsqueda y las asignaciones de direcciones IP. Para proporcionar estos servicios se requiere una infraestructura de DHCP y DNS adecuada. Puede configurar una máquina virtual para proporcionar estos servicios en el entorno de nube privada.  

Utilice el servicio DHCP integrado en NSX o un servidor DHCP local en la nube privada en lugar de enrutar el tráfico DHCP de difusión sobre la WAN de nuevo al entorno local.
