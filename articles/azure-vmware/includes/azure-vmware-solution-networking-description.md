---
title: Redes y conectividad de Azure VMware Solution
description: Descripción de redes y conectividad de Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: d758d42ff1e5c77ddd1e17811e2eea24717878aa
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070744"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution ofrece un entorno de nube privada accesible desde recursos locales y basados en Azure. Servicios como Azure ExpressRoute y las conexiones VPN o Azure Virtual WAN proporcionan la conectividad. No obstante, estos servicios requieren intervalos de direcciones de red y puertos de firewall específicos para habilitar los servicios.

Cuando se implementa una nube privada, se crean redes privadas para la administración, el aprovisionamiento y vMotion. Puede usar estas redes privadas para acceder a vCenter y NSX-T Manager, así como para la implementación o vMotion de máquinas virtuales.

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

Las máquinas virtuales implementadas en la nube privada son accesibles a través de Internet mediante la funcionalidad de [IP pública de Azure Virtual WAN](../enable-public-internet-access.md). De forma predeterminada, el acceso a Internet está deshabilitado para las nuevas nubes privadas. 



