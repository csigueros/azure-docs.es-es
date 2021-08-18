---
title: Funcionalidad de disco de los hosts
description: Los hosts que se usan para crear o escalar clústeres provienen de un grupo de hosts aislado.
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 847f69950a47208b27976972da8dc3427727e774
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747623"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-cloud-clusters.md -->


Los clústeres de Azure VMware Solution se basan en la infraestructura de reconstrucción completa e hiperconvergida. En la tabla siguiente se muestran las capacidades de RAM, CPU y disco del host.

| Tipo de host | CPU   | RAM (GB)  | Nivel de caché NVMe de vSAN (TB, sin procesar)  | Nivel de capacidad SSD de vSAN (TB, sin procesar)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  Intel doble de 18 núcleos, 2,3 GHz  |     576      |                3.2               |                15,20               |

Los hosts que se usan para crear o escalar clústeres provienen de un grupo de hosts aislado. Esos hosts han superado las pruebas de hardware, y se han eliminado todos los datos de manera segura. 