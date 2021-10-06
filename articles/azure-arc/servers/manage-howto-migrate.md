---
title: Migración de servidores habilitados para Azure Arc entre regiones
description: Aprenda a migrar un servidor habilitado para Azure Arc de una región a otra.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: ea44fbf060588ca3859e7f0d51b7d7449dc318fa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807393"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Migración de servidores habilitados para Azure Arc entre regiones

Hay varios escenarios en los que podría convenirle migrar su servidor habilitado para Azure Arc de una región a otra. Por ejemplo, se ha dado cuenta de que la máquina se ha registrado en la región equivocada, quiere mejorar la capacidad de administración o por motivos de gobernanza.

Para migrar un servidor habilitado para Azure Arc de una región de Azure a otra, tiene que desinstalar las extensiones de máquina virtual, eliminar el recurso en Azure y volver a crearlo en la otra región. Antes de realizar estos pasos, debe auditar la máquina para comprobar qué extensiones de máquina virtual están instaladas.

> [!NOTE]
> Aunque las extensiones instaladas continúan ejecutándose y mantienen su funcionamiento normal una vez completado este procedimiento, no podrá administrarlas. Si intenta volver a implementar las extensiones en la máquina, es posible que experimente un comportamiento imprevisible.

## <a name="move-machine-to-other-region"></a>Movimiento de la máquina a otra región

> [!NOTE]
> Durante esta operación, se produce tiempo de inactividad en la migración.

1. Quite las extensiones de VM instaladas desde [Azure Portal](manage-vm-extensions-portal.md#uninstall-extensions), con la [CLI de Azure](manage-vm-extensions-cli.md#remove-an-installed-extension) o con [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

2. Use la herramienta **azcmagent** con el parámetro [Disconnect](manage-agent.md#disconnect) para desconectar la máquina de Azure Arc y eliminar el recurso de la máquina de Azure. Al desconectar la máquina de los servidores habilitados para Azure Arc, no se quita el agente de Connected Machine y no es necesario quitarlo como parte de este proceso. Puede realizar esta operación manualmente con la sesión iniciada de forma interactiva, o bien usar la misma entidad de servicio que utilizó para incorporar varios agentes o un [token de acceso](../../active-directory/develop/access-tokens.md) de la Plataforma de identidad de Microsoft. Si no ha usado una entidad de servicio para registrar la máquina con los servidores habilitados para Azure Arc, vea el siguiente [artículo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) para crear una entidad de servicio.

3. Vuelva a registrar el agente de Connected Machine en los servidores habilitados para Azure Arc de la otra región. Ejecute la herramienta `azcmagent` con el parámetro [Connect](manage-agent.md#connect) para completar este paso.

4. Vuelva a implementar las extensiones de máquina virtual que se implementaron originalmente en la máquina desde los servidores habilitados para Azure Arc. Si ha implementado el agente de Azure Monitor para VM (conclusiones) o el agente de Log Analytics mediante una definición de Azure Policy, los agentes se vuelven a implementar después del siguiente [ciclo de evaluación](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="next-steps"></a>Pasos siguientes

* Puede encontrar información sobre la solución de problemas en la guía [Solución de problemas de conexión del agente de Connected Machine](troubleshoot-agent-onboard.md).

* Aprenda a administrar la máquina con [Azure Policy](../../governance/policy/overview.md) para tareas como la [configuración de invitado](../../governance/policy/concepts/guest-configuration.md) de la máquina virtual, la comprobación de que la máquina informa al área de trabajo de Log Analytics esperada, la habilitación de la supervisión con la directiva de [Información de máquinas virtuales](../../azure-monitor/vm/vminsights-enable-policy.md) y mucho más.
