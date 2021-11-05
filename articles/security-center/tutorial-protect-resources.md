---
title: Tutorial sobre controles de acceso y aplicación en Microsoft Defender for Cloud
description: En este tutorial se muestra cómo configurar una directiva de acceso a las máquinas virtuales Just-In-Time y una directiva de control de aplicaciones.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: e8656730d0edf3fe08a217b5acd5c94d886dadea
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475262"
---
# <a name="tutorial-protect-your-resources-with-microsoft-defender-for-cloud"></a>Tutorial: Protección de los recursos mediante Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud limita la exposición a amenazas mediante controles de acceso y aplicación para bloquear la actividad malintencionadas. El acceso a las máquinas virtuales Just-In-Time (JIT) reduce la exposición a ataques mediante la posibilidad de denegar el acceso persistente a las máquinas virtuales. En su lugar, se proporciona acceso controlado y auditado a VM solo cuando se necesita. Los controles de aplicación adaptables ayudan a proteger las VM frente a malware controlando qué aplicaciones se pueden ejecutar en dichas VM. Defender for Cloud usa el aprendizaje automático para analizar los procesos que se ejecutan en la máquina virtual y le ayuda a aplicar reglas de inclusión en listas de permitidos mediante esta inteligencia.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configuración de una directiva de acceso a las máquinas virtuales Just-In-Time
> * Configuración de una directiva de control de aplicación

## <a name="prerequisites"></a>Prerrequisitos
Para examinar las características que se tratan en este tutorial, debe tener habilitadas las características de seguridad mejoradas de Defender for Cloud. Hay una evaluación gratuita disponible. Para efectuar la actualización, consulte [Habilitación de las protecciones mejoradas](enable-enhanced-security.md).

## <a name="manage-vm-access"></a>Administración de acceso a VM
El acceso a VM JIT se puede usar para bloquear el tráfico entrante a las VM de Azure. Para ello, se reduce la exposición a ataques al mismo tiempo que se proporciona un acceso sencillo para conectarse a las VM cuando sea necesario.

No es necesario que los puertos de administración estén abiertos en todo momento. Solo deben estar abiertos mientras se está conectado a la máquina virtual, por ejemplo, para realizar tareas de administración o mantenimiento. Cuando se habilita la funcionalidad Just-In-Time, Defender for Cloud usa reglas del grupo de seguridad de red (NSG) que restringen el acceso a los puertos de administración para que no puedan ser objeto de ataques.

Siga las instrucciones de [Protección de los puertos de administración con el acceso Just-in-Time](just-in-time-access-usage.md).

## <a name="harden-vms-against-malware"></a>Proteger VM frente a malware
Los controles de aplicación adaptables ayudan a definir un conjunto de aplicaciones que se pueden ejecutar en grupos de recursos configurados que, entre otras ventajas, le ayuda a proteger las VM frente a malware. Defender for Cloud usa el aprendizaje automático para analizar los procesos que se ejecutan en la máquina virtual y le ayuda a aplicar reglas de inclusión en listas de permitidos mediante esta inteligencia.

Siga las instrucciones de [Uso de controles de aplicaciones adaptables para reducir las superficies de ataque de las máquinas](adaptive-application-controls.md).

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a limitar la exposición a amenazas mediante:

> [!div class="checklist"]
> * La configuración de un directiva de acceso a las máquinas virtuales Just-In-Time para proporcionar acceso controlado y auditado a las máquinas virtuales solo cuando sea necesario
> * La configuración de una directiva de controles de aplicación adaptables para controlar qué aplicaciones se pueden ejecutar en las VM

Pase al siguiente tutorial para aprender a responder a incidentes relacionados con la seguridad.

> [!div class="nextstepaction"]
> [Tutorial: Respuesta a incidentes de seguridad](tutorial-security-incident.md)