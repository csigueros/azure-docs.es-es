---
title: Frecuencia de actualización de software de VMware
description: Frecuencia de actualización de software de VMware compatible con Azure VMware Solution.
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 3295ab9da93b1e1ec1cace62653670de366c880d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324322"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

El mantenimiento y la administración del ciclo de vida no afectan la capacidad ni el rendimiento de los clústeres de la nube privada. Las actualizaciones del software de la nube privada se programan según un cronograma que sigue la publicación del conjunto de productos de software de VMware.  Como resultado, su nube privada no requiere tiempo de inactividad para las actualizaciones.

Las actualizaciones del conjunto de productos de software de la nube privada mantienen el software dentro de una de las versiones más recientes del conjunto de productos de software de VMware. Las versiones de software de la nube privada pueden diferir de las versiones más recientes de los componentes de software individuales (ESXi, NSX-T, vCenter, vSAN). Las actualizaciones también incluyen controladores, software de los conmutadores de red y firmware de los nodos sin sistema operativo.

Recibirá una notificación antes y después de que se apliquen las revisiones a las nubes privadas. También trabajaremos con usted para programar una ventana de mantenimiento antes de aplicar actualizaciones o mejoras a la nube privada. 

Entre las actualizaciones de software se incluyen las siguientes:

- **Revisiones**: revisiones de seguridad o correcciones de errores publicadas por VMware.
- **Actualizaciones**: cambio de versión secundaria de un componente de la pila de VMware.
- **Actualizaciones**: cambio de versión principal de un componente de la pila de VMware.

>[!NOTE]
>Microsoft prueba una revisión de seguridad crítica en cuanto está disponible desde VMware.

Se implementan soluciones documentadas de VMware en lugar de instalar una revisión correspondiente hasta que se implementan las siguientes actualizaciones programadas.
