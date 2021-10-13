---
title: Nota de planificación del grupo de discos para la red virtual
description: Nota sobre la importancia de implementar una red virtual más cerca de los hosts de Azure VMware Solution.
ms.topic: include
ms.date: 07/14/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 04bd1b8b4faf697084dad49fa927394deaad3572
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638181"
---
Si tiene pensado modificar la escala de los hosts de Azure VMware Solution mediante [grupos de discos de Azure](../../virtual-machines/disks-pools.md), es fundamental que implemente la red virtual cerca de los hosts con una puerta de enlace de red virtual de ExpressRoute. Cuanto más cerca esté el almacenamiento de los hosts, mejor será el rendimiento.
