---
title: Limitaciones y problemas conocidos de Azure Chaos Studio
description: Conozca las limitaciones y los problemas conocidos actuales al usar Azure Chaos Studio.
services: chaos-studio
author: johnkemnetz
ms.topic: overview
ms.date: 11/02/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: b4df4b7f06c999bfc29154fbbed4133c2664dad8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372461"
---
# <a name="azure-chaos-studio-preview-limitations-and-known-issues"></a>Limitaciones y problemas conocidos de la versión preliminar de Azure Chaos Studio

Durante la versión preliminar pública de Azure Chaos Studio, hay algunas limitaciones y problemas conocidos que el equipo conoce y está trabajando para resolver.

## <a name="limitations"></a>Limitaciones 

* En los errores basados en agente, la máquina debe tener acceso a los siguientes **puntos de conexión HTTPS**:
    * http://agentcommunicationservice-frontdoor-canary.trafficmanager.net 
    * Si se envían datos de telemetría a Application Insights, también se requieren las direcciones IP [de este documento](../azure-monitor/app/ip-addresses.md).
* Si se ejecuta un experimento que usa el agente de Chaos, la máquina virtual debe ejecutar uno de los siguientes **sistemas operativos**:
    * Windows Server 2019, Windows Server 2016, Windows Server 2012 y Windows Server 2012 R2
    * Redhat Enterprise Linux 8.2, SUSE Enterprise Linux 15 SP2, CentOS 8.2, Debian 10 Buster (con instalación de descompresión necesaria), Oracle Linux 7.8, Ubuntu Server 16.04 LTS y Ubuntu Server 18.04 LTS
* El agente de Chaos no se prueba con distribuciones personalizadas de Linux, distribuciones de Linux protegidas (por ejemplo, FIPS o SELinux).
* La experiencia del portal de Chaos Studio solo se ha probado en los siguientes exploradores:
    * **Windows:** Microsoft Edge, Google Chrome, Firefox
    * **MacOS:** Safari, Google Chrome, Firefox

## <a name="known-issues"></a>Problemas conocidos
* La experiencia **Habilitar el destino basado en el agente** en Azure Portal tampoco asigna la identidad administrada asignada por el usuario a la máquina virtual o al conjunto de escalado de máquinas virtuales. Esto debe hacerse manualmente o se producirá un error basado en el agente en un experimento con el error: "Compruebe que el destino está correctamente incorporado y que se proporcionan los permisos de lectura adecuados al MSI del experimento". Esto puede hacerse después de habilitar el destino basado en el agente, pero puede requerir un reinicio.
* La incorporación de un destino en Azure Portal puede producir un error si se desplaza fuera de la vista Destinos antes de que se complete la incorporación.
* Al crear un experimento, después de hacer clic en **Revisar y crear** hay un retraso antes de que el experimento creado aparezca en la lista de experimentos; los usuarios deberán actualizar para ver el experimento en la lista.
* Al seleccionar recursos de destino para un error basado en agente en el diseñador de experimentos, es posible seleccionar máquinas virtuales o conjuntos de escalado de máquinas virtuales con un sistema operativo no compatible con el error seleccionado.


## <a name="next-steps"></a>Pasos siguientes
Empiece a crear y ejecutar experimentos del caos para mejorar la resistencia de las aplicaciones con Chaos Studio mediante los vínculos siguientes.
- [Crear y ejecutar tu primer experimento](chaos-studio-tutorial-service-direct-portal.md)
- [Más información sobre la ingeniería de caos](chaos-studio-chaos-engineering-overview.md)
