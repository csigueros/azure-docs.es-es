---
title: Solución de problemas comunes de Azure Chaos Studio
description: Obtenga información sobre cómo solucionar problemas comunes al usar Azure Chaos Studio.
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/01/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 235dccf84d28f159a4ef5337a431c27c0316d6ec
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158936"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Solución de problemas con Azure Chaos Studio

A medida que usa Chaos Studio, es posible que encuentre algunos problemas ocasionalmente. En este artículo se detallan los problemas comunes y los pasos para solucionarlos.

## <a name="issues-due-to-prerequisites"></a>Problemas debidos a los requisitos previos

Algunos problemas se deben a que faltan algunos requisitos previos. 

### <a name="why-do-agent-based-faults-fail-on-my-linux-virtual-machines"></a>¿Por qué se producen errores basados en agentes en mis máquinas virtuales Linux?

Los errores de [presión de CPU](chaos-studio-fault-library.md#cpu-pressure), [presión de memoria física](chaos-studio-fault-library.md#physical-memory-pressure), [presión de E/S de disco](chaos-studio-fault-library.md#disk-io-pressure-linux) y [esfuerzo stress-ng arbitrario](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) requieren la instalación de la [utilidad stress-ng](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) en la máquina virtual. Para obtener más información, consulte las secciones de errores de los requisitos previos.

### <a name="my-aks-chaos-mesh-faults-are-failing"></a>Experimento fallos en los errores de Chaos Mesh de AKS

Antes de usar errores de Chaos Mesh en AKS, debe instalar Chaos Mesh. Puede encontrar las instrucciones en el [tutorial sobre errores de Chaos Mesh en AKS](chaos-studio-tutorial-aks.md#set-up-chaos-mesh-on-your-aks-cluster).

## <a name="experiment-design-and-creation"></a>Diseño y creación de experimentos

### <a name="why-do-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity-when-i-try-to-create-an-experiment"></a>¿Por qué aparece el error `The microsoft:agent provider requires a managed identity` cuando intento crear un experimento? 

Este error se produce cuando el agente no se ha implementado en la máquina virtual. Para obtener instrucciones de instalación, consulte el artículo sobre la [creación y ejecución de un experimento que usa errores basados en agentes](chaos-studio-tutorial-agent-based.md).

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported-what-does-this-mean"></a>Al crear un experimento, aparece el error `The content media type '<null>' is not supported. Only 'application/json' is supported.`. ¿Qué significa?

Si está creando manualmente el objeto JSON del experimento, este error se debe a un elemento JSON con formato incorrecto en la definición del experimento. Compruebe si tiene errores de sintaxis, como llaves o corchetes que no coinciden ({} y \[\]).

## <a name="experiment-execution"></a>Realización de un experimento

### <a name="the-execution-status-of-my-experiment-is-failed-how-do-i-determine-what-went-wrong"></a>El estado de ejecución del experimento es "con errores". ¿Cómo puedo saber qué ha ido mal?

En la página Experimentos, haga clic en el nombre del experimento para ir a la vista Detalles del experimento. En la sección Historial, haga clic en el vínculo Detalles de la instancia de ejecución para obtener más información.

![Historial del experimento](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>Se produjo el siguiente error de mi agente: compruebe que el destino se ha incorporado correctamente y que se han concedido los permisos de lectura adecuados al msi del experimento.

Esto puede ocurrir si se ha usado Azure Portal para incorporar el agente, que tiene un problema conocido: la habilitación de un destino basado en agente no asigna la identidad administrada asignada por el usuario a la máquina virtual o al conjunto de escalado de máquinas virtuales.

Para resolver este problema, vaya a la máquina virtual o al conjunto de escalado de máquinas virtuales en Azure Portal y luego a **Identidad**, abra la pestaña **Asignado por el usuario** y **agregue** la identidad asignada por el usuario a la máquina virtual. Cuando termine, es posible que tenga que reiniciar la máquina virtual para que el agente se conecte.

### <a name="how-do-i-collect-agent-logs-on-a-linux-virtual-machine"></a>¿Cómo puedo recopilar registros de agente en una máquina virtual Linux?

Ejecute este comando en la máquina virtual: `journalctl -u azure-chaos-agent`.
