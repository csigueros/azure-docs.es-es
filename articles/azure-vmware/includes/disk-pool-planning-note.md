---
title: Nota de planificación del grupo de discos para la red virtual
description: Nota sobre la importancia de implementar una red virtual más cerca de los hosts de Azure VMware Solution.
ms.topic: include
ms.date: 07/14/2021
ms.openlocfilehash: 39bcf823a71852396c1c05db4902abf000433499
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640585"
---
Si tiene pensado modificar la escala de los hosts de Azure VMware Solution mediante [grupos de discos de Azure](../../virtual-machines/disks-pools.md), es fundamental que implemente la red virtual cerca de los hosts con una puerta de enlace de red virtual de ExpressRoute.  Cuanto más cerca esté el almacenamiento de los hosts, mejor será el rendimiento.