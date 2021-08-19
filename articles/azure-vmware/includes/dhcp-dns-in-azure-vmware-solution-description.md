---
title: DHCP y DNS en la descripción de Azure VMware Solution
description: Descripción de Azure VMware Solution, DHCP y DNS.
ms.topic: include
ms.date: 05/28/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 98f2fd71cc4657a3837278c3b7a761678b8a1005
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750111"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

Las aplicaciones y cargas de trabajo que se ejecutan en un entorno de nube privada requieren servicios de resolución de nombres y DHCP para la búsqueda y las asignaciones de direcciones IP. Para proporcionar estos servicios se requiere una infraestructura de DHCP y DNS adecuada. Puede configurar una máquina virtual para proporcionar estos servicios en el entorno de nube privada.  

Utilice el servicio DHCP integrado en NSX o un servidor DHCP local en la nube privada en lugar de enrutar el tráfico DHCP de difusión sobre la WAN de nuevo al entorno local.
