---
title: Redes y conectividad de Azure VMware Solution
description: Descripción de redes y conectividad de Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0ee52f437027690787ca5dc95eda2705b796b604
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638394"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution ofrece un entorno de nube privada accesible desde recursos locales y basados en Azure. Servicios como Azure ExpressRoute y las conexiones VPN o Azure Virtual WAN proporcionan la conectividad. No obstante, estos servicios requieren intervalos de direcciones de red y puertos de firewall específicos para habilitar los servicios.

Cuando se implementa una nube privada, se crean redes privadas para la administración, el aprovisionamiento y vMotion. Puede usar estas redes privadas para acceder a vCenter y NSX-T Manager, así como para la implementación o vMotion de máquinas virtuales.

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

Las máquinas virtuales implementadas en la nube privada son accesibles a través de Internet mediante la funcionalidad de [IP pública de Azure Virtual WAN](../enable-public-internet-access.md). De forma predeterminada, el acceso a Internet está deshabilitado para las nuevas nubes privadas. 



