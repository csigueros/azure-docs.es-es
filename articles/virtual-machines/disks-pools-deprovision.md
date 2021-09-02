---
title: Desaprovisionamiento de un grupo de discos de Azure (versión preliminar)
description: Aprenda a desaprovisionar, detener y eliminar un grupo de discos de Azure.
author: roygara
ms.date: 07/19/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 823dd066e1751896b3f7986a18d18d80d68219a1
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469876"
---
# <a name="deprovision-an-azure-disk-pool-preview"></a>Desaprovisionamiento de un grupo de discos de Azure (versión preliminar)

En este artículo se trata el proceso de eliminación de un grupo de discos de Azure (versión preliminar) y cómo deshabilitar la compatibilidad con iSCSI.

## <a name="stop-a-disk-pool"></a>Detención de un grupo de discos

Puede detener un grupo de discos para ahorrar costos y conservar todas las configuraciones. Cuando se detiene un grupo de discos, deja de ser posible conectarse a él a través de iSCSI. Los recursos administrados implementados para dar soporte técnico al grupo de discos no se eliminarán. Antes de detener un grupo de discos es preciso desconectar todos los clientes con conexiones iSCSI al grupo de discos. Los grupos de discos se pueden iniciar en cualquier momento. Esto reactivará el destino iSCSI expuesto en este grupo de discos.
# <a name="portal"></a>[Portal](#tab/azure-portal)

Actualmente no se admite actualmente en Azure Portal.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name 'myDiskPool' -ResourceGroupName 'myResourceGroupt'
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az disk-pool stop --name "myDiskPool" --resource-group "myResourceGroup"
```
---

## <a name="delete-a-disk-pool"></a>Eliminación de un grupo de discos

Cuando se elimina un grupo de discos, también se eliminan todos los recursos del grupo de recursos administrados. Si hay conexiones iSCSI pendientes con el grupo de discos, no puede eliminar el grupo. Primero es preciso desconectar todos los clientes con conexiones iSCSI al grupo de discos. Los discos que se han agregado al grupo de discos no se eliminarán.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Busque **Grupo de discos**, selecciónelo y, después, seleccione el grupo de discos que desea eliminar.
1. Seleccione **Eliminar** en la parte superior de la página.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPool -Name "myDiskpoolName" -ResourceGroupName "myRGName"
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az disk-pool delete --name "myDiskPool" --resource-group "myResourceGroup"
```

---

## <a name="disable-iscsi-support"></a>Deshabilitación de la compatibilidad con iSCSI

Si deshabilita la compatibilidad con iSCSI en un grupo de discos, no podrá conectarse a un grupo de discos.

La primera vez que se habilita la compatibilidad con iSCSI en un grupo de discos, se crea un destino iSCSI como punto de conexión de la conexión iSCSI. Para deshabilitar la compatibilidad con iSCSI en el grupo de discos, elimine el destino iSCSI. Cada grupo de discos no puede tener configurado más de un destino iSCSI.

Puede volver a habilitar la compatibilidad con iSCSI en un grupo de discos existente. La compatibilidad con iSCSI no se puede deshabilitar en el grupo de discos si hay conexiones iSCSI pendientes al grupo de discos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Busque **Grupo de discos** y seleccione su grupo existente.
1. En **Configuración**, seleccione **iSCSI**.
1. Desactive la casilla **Enable iSCSI** (Habilitar iSCSI) y seleccione **Save** (Guardar).    

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPoolIscsiTarget -DiskPoolName "myDiskpoolName" -Name "myiSCSITargetName" -ResourceGroupName "myRGName"
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az disk-pool iscsi-target delete --disk-pool-name "myDiskPool" --name "myIscsiTarget" --resource-group "myResourceGroup"
```

---

## <a name="next-steps"></a>Pasos siguientes

Información sobre [Azure Managed Disks](managed-disks-overview.md).