---
title: Control de mantenimiento para las actualizaciones de imágenes del sistema operativo en conjuntos de escalado de máquinas virtuales de Azure mediante la CLI de Azure
description: Vea cómo puede controlar cuándo se implementan las actualizaciones automáticas de las imágenes del sistema operativo en los conjuntos de escalado de máquinas virtuales de Azure usando la característica Control de mantenimiento y la CLI de Azure.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e00341b38cca73ccd351b4e16ce71317a8d0c751
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073201"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-cli"></a>Control de mantenimiento para las actualizaciones de imágenes del sistema operativo en conjuntos de escalado de máquinas virtuales de Azure mediante la CLI de Azure

El control de mantenimiento le permite decidir cuándo aplicar las actualizaciones automáticas de imágenes del sistema operativo invitado a los conjuntos de escalado de máquinas virtuales. En este tema se tratan las opciones de la CLI de Azure para el control de mantenimiento. Para más información sobre el uso del control de mantenimiento, consulte [Control de mantenimiento para conjuntos de escalado de máquinas virtuales de Azure](virtual-machine-scale-sets-maintenance-control.md).


## <a name="create-a-maintenance-configuration"></a>Creación de una configuración de mantenimiento

Use `az maintenance configuration create` para crear una configuración de mantenimiento. En este ejemplo, se crea una configuración de mantenimiento denominada *myConfig* en el ámbito de la imagen del sistema operativo. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage\
   --location eastus
```

Copie el identificador de configuración de la salida para usarlo más adelante.

El uso de `--maintenance-scope osimage` garantiza que se usa la configuración de mantenimiento para controlar las actualizaciones del sistema operativo invitado.

Si intenta crear una configuración con el mismo nombre, pero en una ubicación diferente, obtendrá un error. Los nombres de configuración deben ser únicos dentro de su grupo de recursos.

Puede consultar las configuraciones de mantenimiento disponibles mediante `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-a-scheduled-window"></a>Creación de una configuración de mantenimiento con una ventana programada
También puede declarar una ventana programada cuando Azure vaya a aplicar las actualizaciones en los recursos. En este ejemplo, se crea una configuración de mantenimiento denominada *myConfig* con una ventana programada de 5 horas el cuarto lunes de cada mes. Una vez que cree una ventana programada, ya no tendrá que aplicar las actualizaciones manualmente.

> [!IMPORTANT]
> La **duración** del mantenimiento debe ser de *5 horas* o más. La **periodicidad** del mantenimiento debe establecerse en *Día*.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Day" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

## <a name="assign-the-configuration"></a>Asignación de la configuración

Utilice `az maintenance assignment create` para asignar la configuración al conjunto de escalado de máquinas virtuales.


## <a name="enable-automatic-os-upgrade"></a>Habilitación de la actualización automática del sistema operativo

Puede habilitar las actualizaciones automáticas del sistema operativo para cada conjunto de escalado de máquinas virtuales que vaya a usar el control de mantenimiento. Para más información sobre cómo habilitar las actualizaciones automáticas del sistema operativo en el conjunto de escalado de máquinas virtuales, consulte [Actualizaciones automáticas de imágenes del sistema operativo del conjunto de escalado de máquinas virtuales de Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Información sobre el mantenimiento y las actualizaciones de las máquinas virtuales que se ejecutan en Azure](maintenance-and-updates.md)
