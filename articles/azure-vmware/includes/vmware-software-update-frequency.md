---
title: Frecuencia de actualización de software de VMware
description: Frecuencia de actualización de software de VMware compatible con Azure VMware Solution.
ms.topic: include
ms.date: 08/24/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 297fc81559adc9bb3f9c22091ca568da36cffb8f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638625"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Una de las ventajas de las nubes privadas de Azure VMware Solution es que la plataforma se mantiene automáticamente.  Microsoft es responsable de la administración del ciclo de vida del software de VMware (ESXi, vCenter y vSAN). Microsoft también se encarga de la administración del ciclo de vida de los dispositivos NSX-T y de la configuración de la red de arranque, como la creación de la puerta de enlace de nivel 0 y la habilitación del enrutamiento vertical de arriba abajo. Usted es responsable de la configuración de SDN de NSX-T, como los segmentos de red, reglas de firewall distribuidas, las puertas de enlace de nivel 1 y equilibradores de carga. 

Microsoft es responsable de aplicar las revisiones o actualizaciones a ESXi, vCenter, vSAN y NSX-T en la nube privada. El impacto de las revisiones y actualizaciones en ESXi, vCenter y NSX-T es diferente. 

- **ESXi**: no se produce ningún impacto en las cargas de trabajo que se ejecutan en la nube privada. El acceso a vCenter y NSX-T no se bloquea durante este tiempo.  Se recomienda que, durante este tiempo, no planee ninguna otra actividad, como el escalado vertical de la nube privada, entre otras, en la nube privada.

- **vCenter**: no se produce ningún impacto en las cargas de trabajo que se ejecutan en la nube privada. Durante este tiempo, VMware vCenter no estará disponible y no podrá administrar las VM (detener, iniciar, crear ni eliminar VM). Se recomienda que, durante este tiempo, no planee ninguna otra actividad, como el escalado vertical de la nube privada, la creación de redes nuevas, entre otras, en la nube privada.

- **NSX-T**: se produce un impacto en la carga de trabajo y, cuando se actualiza un host determinado, las VM de ese host podrían perder conectividad de 2 segundos a 1 minuto como máximo con cualquiera de los síntomas siguientes:

   - Errores de ping

   - Pérdida de paquetes

   - Mensajes de error (por ejemplo, *Host de destino inaccesible* y *Net inaccesible*)

   Durante esta ventana de actualización, se bloqueará todo el acceso al plano de administración de NSX-T. No puede realizar cambios de configuración en el entorno de NSX-T durante la operación.  Sin embargo, las cargas de trabajo seguirán funcionando con normalidad, sujeto al impacto en la actualización que se ha detallado anteriormente.
 
   Se recomienda que, durante la actualización, no planee ninguna otra actividad, como el escalado vertical de la nube privada, entre otras, en la nube privada. Esto puede impedir que la actualización se inicie o podría tener efectos negativos en la actualización y el entorno.
 
Recibirá una notificación antes y después de que se apliquen las revisiones a las nubes privadas. También trabajaremos con usted para programar una ventana de mantenimiento antes de aplicar actualizaciones o mejoras a la nube privada.


Entre las actualizaciones de software se incluyen las siguientes:

- **Revisiones**: revisiones de seguridad o correcciones de errores publicadas por VMware.

- **Actualizaciones**: cambio de versión secundaria de un componente de la pila de VMware.

- **Actualizaciones**: cambio de versión principal de un componente de la pila de VMware.

>[!NOTE]
>Microsoft prueba una revisión de seguridad crítica en cuanto está disponible desde VMware.

Se implementan soluciones documentadas de VMware en lugar de instalar una revisión correspondiente hasta que se implementan las siguientes actualizaciones programadas.
