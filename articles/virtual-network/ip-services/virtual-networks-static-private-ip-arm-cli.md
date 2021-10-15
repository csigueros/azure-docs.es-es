---
title: 'Creación de una VM con una dirección IP privada estática: CLI de Azure'
description: Obtenga información sobre cómo crear una máquina virtual con una dirección IP privada estática mediante la CLI de Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 900010fd454e356b43eb7cb5b2ee0379bdf90beb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367575"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-cli"></a>Creación de una máquina virtual con una dirección IP privada estática mediante la CLI de Azure

A una máquina virtual (VM) se le asigna automáticamente una dirección IP privada de un intervalo que el usuario especifique. Este intervalo se basa en la subred en la que se implementa la máquina virtual. La VM conserva la dirección hasta que se elimina. Azure asigna dinámicamente la siguiente dirección IP privada disponible desde la subred en la que se crea una máquina virtual. Asigne una dirección IP estática a la máquina virtual si quiere disponer de una dirección IP específica de la subred.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Este tutorial requiere la versión 2.0.28 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create) llamado **myResourceGroup** en la ubicación **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Cree la máquina virtual con [az vm create](/cli/azure/vm#az_vm_create). 

Con el comando siguiente, se crea una máquina virtual con Windows Server. Cuando se le solicite, proporcione un nombre de usuario y una contraseña que se usarán como credenciales para la máquina virtual:

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group myResourceGroup \
    --public-ip-address myPublicIP \
    --public-ip-sku Standard \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

## <a name="change-private-ip-address-to-static"></a>Cambio de la dirección IP privada a estática

En esta sección, cambiará la dirección IP privada de **dinámica** a **estática** para la máquina virtual que creó anteriormente. 

Use [az network nic ip-config update](/cli/azure/network/nic/ipconfig#az_network_nic_ip_config_update) para actualizar la configuración de la interfaz de red.

El comando siguiente cambia la dirección IP privada de la máquina virtual a estática:

```azurecli-interactive
  az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --private-ip-address 10.0.0.4
```

> [!WARNING]
> Aunque puede agregar la configuración de dirección IP privada al sistema operativo, se recomienda no hacerlo hasta después de haber leído el tema sobre la [incorporación de una dirección IP privada a un sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede utilizar [az group delete](/cli/azure/group#az_group_delete) para eliminar el grupo de recursos y todos los recursos que contenga:

```azurecli-interactive
  az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de las [direcciones IP públicas](public-ip-addresses.md#public-ip-addresses) en Azure.
- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Obtener más información acerca de las [direcciones IP privadas](private-ip-addresses.md) y la asignación de una [dirección IP privada estática](virtual-network-network-interface-addresses.md#add-ip-addresses) a una máquina virtual de Azure.
- Obtener más información acerca de cómo crear máquinas virtuales [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).