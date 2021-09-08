---
title: 'Eliminación de grupos de hosts de Azure Virtual Desktop: Azure'
description: Procedimiento para eliminar un grupo de hosts en Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: f0ea0e89ef70983f5c74b37365305cfab3e984bc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098511"
---
# <a name="delete-a-host-pool"></a>Eliminación de un grupo de hosts

Todos los grupos de hosts creados en Azure Virtual Desktop se adjuntan a los grupos de aplicaciones y hosts de sesión. Para eliminar un grupo de hosts, debe eliminar los grupos de aplicaciones y los hosts de sesión asociados. Eliminar un grupo de aplicaciones es bastante sencillo, pero eliminar un host de sesión es más complicado. Cuando se elimina un host de sesión, es necesario asegurarse de que no tiene ninguna sesión de usuario activa. Se deben cerrar todas las sesiones de usuario en el host de sesión para evitar que los usuarios pierdan datos.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para eliminar un grupo de hosts en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

2. Busque y seleccione **Azure Virtual Desktop**.

3. Seleccione **Grupos de hosts** en el menú de la izquierda de la página y seleccione el nombre del grupo de hosts que quiere eliminar.

4. En el menú de la izquierda de la página, seleccione **Grupos de aplicaciones**.

5. Seleccione todos los grupos de aplicaciones del grupo de hosts que va a eliminar y seleccione **Quitar**.

6. Una vez que quite los grupos de aplicaciones, vaya al menú del lado izquierdo de la página y seleccione **Información general**.

7. Seleccione **Quitar**.

8. Si hay hosts de sesión en el grupo de hosts que está eliminando, verá un mensaje que le pedirá permiso para continuar. Seleccione **Sí**.

9. En Azure Portal se quitarán todos los hosts de sesión y se eliminará el grupo de hosts. Las máquinas virtuales relacionadas con el host de sesión no se eliminarán y permanecerán en la suscripción.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para eliminar un grupo de hosts con PowerShell, primero debe eliminar todos los grupos de aplicaciones del grupo de hosts. Para eliminar todos los grupos de aplicaciones, ejecute el cmdlet de PowerShell siguiente:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

A continuación, ejecute este cmdlet para eliminar el grupo de hosts:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

Este cmdlet quita todas las sesiones de usuario existentes en el host de sesión del grupo de hosts. También anula el registro del host de sesión del grupo de hosts. Las máquinas virtuales (VM) relacionadas seguirán existiendo dentro de su suscripción.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para eliminar un grupo de hosts con la CLI de Azure, primero debe eliminar todos los grupos de aplicaciones del grupo de hosts. 

Para eliminar todos los grupos de aplicaciones, use el comando [az desktopvirtualization applicationgroup delete](/cli/azure/desktopvirtualization/applicationgroup#az_desktopvirtualization_applicationgroup_delete):

```azurecli
az desktopvirtualization applicationgroup delete --name "MyApplicationGroup" --resource-group "MyResourceGroup"
```

A continuación, elimine el grupo de hosts con el comando [az desktopvirtualization hostpool delete](/cli/azure/desktopvirtualization/hostpool#az_desktopvirtualization_hostpool_delete).

```azurecli
az desktopvirtualization hostpool delete --force true --name "MyHostPool" --resource-group "MyResourceGroup"
```

Esta eliminación quita todas las sesiones de usuario existentes en el host de sesión del grupo de hosts. También anula el registro del host de sesión del grupo de hosts. Las máquinas virtuales (VM) relacionadas seguirán existiendo dentro de su suscripción.

---

## <a name="next-steps"></a>Pasos siguientes

Para información sobre cómo crear un grupo de hosts, consulte estos artículos:

- [Creación de un grupo de hosts con Azure Portal](create-host-pools-azure-marketplace.md)
- [Creación de un grupo host con PowerShell](create-host-pools-powershell.md)

Para información sobre cómo configurar grupos de hosts, consulte estos artículos:

- [Personalización de las propiedades de Protocolo de escritorio remoto para un grupo de hosts](customize-rdp-properties.md)
- [Configuración del método de equilibrio de carga de Azure Virtual Desktop](configure-host-pool-load-balancing.md)
- [Configuración del tipo de asignación de grupo de host de escritorio personal](configure-host-pool-personal-desktop-assignment-type.md)
