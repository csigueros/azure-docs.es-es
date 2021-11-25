---
title: 'Creación de una máquina virtual de Azure con una red de doble pila: CLI de Azure'
titleSuffix: Azure Virtual Network
description: En este artículo va a aprender a usar la CLI de Azure para crear una máquina virtual con una red virtual de doble pila de Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 11/11/2021
ms.custom: template-how-to
ms.openlocfilehash: ae8f7fef465b8ba42a058b43ca95fd2334f437c8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495287"
---
# <a name="create-an-azure-virtual-machine-with-a-dual-stack-network-using-the-azure-cli"></a>Creación de una máquina virtual de Azure con una red de doble pila mediante la CLI de Azure

En este artículo va a crear una máquina virtual de Azure con la CLI de Azure. La máquina virtual se crea junto con la red de doble pila como parte de los procedimientos.  Una vez terminada, la máquina virtual admite la comunicación IPv4 e IPv6.  

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este tutorial requiere la versión 2.0.28 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create) llamado **myResourceGroup** en la ubicación **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-virtual-network"></a>Creación de una red virtual

En esta sección va a crear una red virtual de doble pila para la máquina virtual.

Use [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) para crear una red virtual.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 2404:f800:8000:122::/63 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24 2404:f800:8000:122::/64
```

## <a name="create-public-ip-addresses"></a>Creación de direcciones IP públicas

En esta sección va a crear dos direcciones IP públicas, IPv4 e IPv6. 

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear las direcciones IP públicas.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv4 \
    --sku Standard \
    --version IPv4

  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv6 \
    --sku Standard \
    --version IPv6

```
## <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

En esta sección va a crear un grupo de seguridad de red para la máquina virtual y la red virtual.

Use [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) para crear el grupo de seguridad de red.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroup \
    --name myNSG
```

### <a name="create-network-security-group-rules"></a>Creación de reglas de grupo de seguridad de red

Va a crear una regla para permitir conexiones a la máquina virtual en el puerto 22 para SSH. Se crea una regla adicional para permitir todos los puertos para las conexiones salientes.

Use [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) para crear las reglas del grupo de seguridad de red.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleSSH \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 200

  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleAllOUT \
    --protocol '*' \
    --direction outbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range '*' \
    --access allow \
    --priority 200
```

## <a name="create-virtual-machine"></a>Crear máquina virtual

En esta sección va a crear la máquina virtual y sus recursos auxiliares.

### <a name="create-network-interface"></a>Creación de la interfaz de red

Va a usar [az network nic create](/cli/azure/network/nic#az_network_nic_create) para crear la interfaz de red para la máquina virtual. Las direcciones IP públicas y el NSG creados anteriormente están asociados a la NIC. La interfaz de red está conectada a la red virtual creada anteriormente.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroup \
    --name myNIC1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --public-ip-address myPublicIP-IPv4
```

### <a name="create-ipv6-ip-configuration"></a>Creación de una configuración de IP IPv6

Use [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) para crear la configuración IPv6 para la NIC.

```azurecli-interactive
  az network nic ip-config create \
    --resource-group myResourceGroup \
    --name myIPv6config \
    --nic-name myNIC1 \
    --private-ip-address-version IPv6 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --public-ip-address myPublicIP-IPv6
```

### <a name="create-vm"></a>Creación de una máquina virtual

Use [az vm create](/cli/azure/vm#az_vm_create) para crear la máquina virtual.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNIC1 \
    --image UbuntuLTS \
    --admin-username azureuser \
    --authentication-type ssh \
    --generate-ssh-keys
```

## <a name="test-ssh-connection"></a>Prueba de la conexión SSH

Use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) para mostrar las direcciones IP de la máquina virtual.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv4 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv4 \
>     --query ipAddress \
>     --output tsv
20.119.201.208
```

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv6 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv6 \
>     --query ipAddress \
>     --output tsv
2603:1030:408:6::9d
```

Abra una conexión SSH a la máquina virtual mediante el comando siguiente. Reemplace la dirección IP por la dirección IP de la máquina virtual.

```bash
  ssh azureuser@20.119.201.208
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, use el comando [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive
  az group delete \
    --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido a crear una máquina virtual de Azure con una red de doble pila.

Para obtener más información sobre IPv6 y direcciones IP de Azure, vea:

- [¿Qué es IPv6 para Azure Virtual Network?](ipv6-overview.md)

- [¿Qué es Azure Virtual Network IP Services?](ip-services-overview.md)


