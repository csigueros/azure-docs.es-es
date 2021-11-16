---
title: Actualización automática de extensiones (versión preliminar) para servidores habilitados para Azure Arc
description: Obtenga información sobre cómo habilitar la actualización automática de extensiones (versión preliminar) para los servidores habilitados para Azure Arc.
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: aea00dbe8609d0a8481d9ca742c12e6d0215f189
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132033754"
---
# <a name="automatic-extension-upgrade-preview-for-azure-arc-enabled-servers"></a>Actualización automática de extensiones (versión preliminar) para servidores habilitados para Azure Arc

La actualización automática de extensiones (versión preliminar) está disponible para los servidores habilitados para Azure Arc que tienen instaladas extensiones de máquina virtual compatibles. Cuando la actualización automática de extensiones (versión preliminar) está habilitada en una máquina, la extensión se actualiza automáticamente cada vez que el editor de la extensión publica una nueva versión de esta.

 La actualización automática de extensiones tiene las siguientes características:

- Puede optar por recibir las actualizaciones automáticas en cualquier momento.
- Cada extensión admitida se inscribe individualmente, y puede elegir qué extensiones se van a actualizar automáticamente.
- Se admite en todas las regiones de la nube pública.

> [!NOTE]
> En esta versión, solo se admite la CLI de Azure para configurar la actualización automática de extensiones.

## <a name="how-does-automatic-extension-upgrade-work"></a>¿Cómo funciona la actualización automática de extensiones?

El proceso de actualización de extensiones reemplaza la versión de la extensión existente de la máquina virtual de Azure admitida por servidores habilitados para Azure Arc por una nueva versión de la misma extensión que ha publicado el editor de la extensión.

Las actualizaciones de extensiones con errores se reintentan automáticamente. Los reintentos se realizan cada pocos días automáticamente sin la intervención del usuario.

### <a name="availability-first-updates"></a>Actualizaciones por orden de disponibilidad

El modelo de orden de disponibilidad de las actualizaciones orquestadas de la plataforma garantiza que se respeten las configuraciones de disponibilidad en Azure de varios niveles.

En el caso de un grupo de servidores habilitados para Arc que se someten a una actualización, la plataforma Azure orquestará las actualizaciones siguiendo el modelo descrito en la [actualización de la extensión de automatización](../../virtual-machines/automatic-extension-upgrade.md#availability-first-updates). Sin embargo, hay algunas diferencias importantes entre los servidores habilitados para Arc y las máquinas virtuales de Azure:

**Entre regiones:**

- Las regiones emparejadas geográficamente no son aplicables.

**Dentro de una región:**

- Availability Zones no se admite.
- Las máquinas se procesarán por lotes con el mejor esfuerzo para evitar actualizaciones simultáneas para todas las máquinas registradas con servidores habilitados para Arc en una suscripción.  

## <a name="supported-extensions"></a>Extensiones admitidas

La actualización automática de extensiones (versión preliminar) admite las siguientes extensiones (y se agregan más periódicamente):

- Agente de Azure Monitor: Linux y Windows
- Agente de seguridad de Azure: Linux y Windows
- Agente de dependencia: Linux y Windows
- Extensión de Key Vault: solo Linux
- Agente de Log Analytics (agente de OMS): solo Linux

## <a name="enabling-automatic-extension-upgrade-preview"></a>Habilitación de la actualización automática de extensiones (versión preliminar)

Para habilitar la actualización automática de extensiones (versión preliminar) para una extensión, debe asegurarse de que la propiedad `enable-auto-upgrade` esté establecida en `true` y de que se haya agregado a cada definición de extensión de manera individual.

Use el cmdlet [az connectedmachine extension](/cli/azure/connectedmachine/extension) con los parámetros `--name`, `--machine-name`, `--enable-auto-upgrade` y `--resource-group`.

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade true
```

Para comprobar el estado de la actualización automática de extensiones (versión preliminar) para todas las extensiones en un servidor habilitado para Arc, ejecute el siguiente comando:

```azurecli
az connectedmachine extension list --resource-group resourceGroupName --machine-name machineName --query "[].{Name:name, AutoUpgrade:properties.enableAutoUpgrade}" --output table
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Actualizaciones de extensiones con varias extensiones

Una máquina administrada por servidores habilitados para Arc puede tener varias extensiones con la actualización automática de extensiones habilitada. La misma máquina también puede tener otras extensiones sin tener habilitada la actualización automática de extensiones.

Si hay varias actualizaciones de extensiones disponibles para una máquina, es posible que se procesen juntas por lotes, pero cada una se aplica individualmente en una máquina virtual. Un error en una extensión no afecta a las otras extensiones que puedan estar actualizándose. Por ejemplo, si hay dos extensiones programadas para una actualización y se produce un error en la actualización de la primera, la actualización de la segunda extensión sigue su curso.

## <a name="disable-automatic-extension-upgrade"></a>Deshabilitación de la actualización automática de extensiones

Para deshabilitar la actualización automática de extensiones (versión preliminar) para una extensión, debe asegurarse de que la propiedad `enable-auto-upgrade` esté establecida en `false` y de que se haya agregado a cada definición de extensión de manera individual.

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

Use el cmdlet [az connectedmachine extension](/cli/azure/connectedmachine/extension) con los parámetros `--name`, `--machine-name`, `--enable-auto-upgrade` y `--resource-group`.

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade false
```

## <a name="next-steps"></a>Pasos siguientes

- Puede implementar, administrar y quitar extensiones de VM con la [CLI de Azure](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md) o [plantillas de Azure Resource Manager](manage-vm-extensions-template.md).

- Puede encontrar información de solución de problemas en la [guía de solución de problemas de las extensiones de máquina virtual](troubleshoot-vm-extensions.md).
