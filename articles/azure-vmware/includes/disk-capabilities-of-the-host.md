---
title: Funcionalidad de disco de los hosts
description: Los hosts que se usan para crear o escalar clústeres provienen de un grupo de hosts aislado.
ms.topic: include
ms.date: 04/23/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 29a16c506d4e381e25c9c12a424b3e1fba7ad08d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638420"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-cloud-clusters.md -->


Los clústeres de Azure VMware Solution se basan en la infraestructura de reconstrucción completa e hiperconvergida. En la tabla siguiente se muestran las capacidades de RAM, CPU y disco del host.

| Tipo de host | CPU   | RAM (GB)  | Nivel de caché NVMe de vSAN (TB, sin procesar)  | Nivel de capacidad SSD de vSAN (TB, sin procesar)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  Intel doble de 18 núcleos, 2,3 GHz  |     576      |                3.2               |                15,20               |

Los hosts que se usan para crear o escalar clústeres provienen de un grupo de hosts aislado. Esos hosts han superado las pruebas de hardware, y se han eliminado todos los datos de manera segura. 