---
title: Nota de planificación del grupo de discos para la red virtual
description: Nota sobre la importancia de implementar una red virtual más cerca de los hosts de Azure VMware Solution.
ms.topic: include
ms.date: 07/14/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: ddee39189fd93d5651e8e879375b977a4222e9fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729116"
---
Si tiene pensado modificar la escala de los hosts de Azure VMware Solution mediante [grupos de discos de Azure](../../virtual-machines/disks-pools.md), es fundamental que implemente la red virtual cerca de los hosts con una puerta de enlace de red virtual de ExpressRoute. Cuanto más cerca esté el almacenamiento de los hosts, mejor será el rendimiento.
