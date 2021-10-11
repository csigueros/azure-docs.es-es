---
title: 'Tutorial: Configuración de la preferencia de enrutamiento de una máquina virtual mediante la CLI de Azure'
description: En este tutorial, aprenderá a crear una máquina virtual con una dirección IP pública con preferencia de enrutamiento mediante la CLI de Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9428d28eabde40dec7ed2805217fb655d8b0f693
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369740"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-cli"></a>Tutorial: Configuración de la preferencia de enrutamiento de una VM mediante la CLI de Azure
En este tutorial se muestra cómo configurar las preferencias de enrutamiento para una máquina virtual. El tráfico de Internet enlazado desde la máquina virtual se enrutará a través de la red del ISP si elige **Internet** como opción de preferencia de enrutamiento. El enrutamiento predeterminado se hará a través de la red global de Microsoft.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una dirección IP pública con la preferencia de enrutamiento de **Internet**.
> * Cree una máquina virtual.
> * Comprobar que la dirección IP pública está establecida en la preferencia de enrutamiento de **Internet**.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Este tutorial requiere la versión 2.0.28 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create) con el nombre **TutorVMRoutePref-rg** en la ubicación **westus2**.

```azurecli-interactive
  az group create \
    --name TutorVMRoutePref-rg \
    --location westus2
```

## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear una dirección IPv4 pública estándar y con redundancia de zona denominada **myPublicIP** en **TutorVMRoutePref-rg**. La **etiqueta** de **Internet** se aplica a la dirección IP pública como un parámetro en el comando de la CLI que habilita la preferencia de enrutamiento de **Internet**.

```azurecli-interactive
az network public-ip create \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

## <a name="create-virtual-machine"></a>Crear máquina virtual

Use [az vm create](/cli/azure/vm#az_vm_create) para crear una máquina virtual. La dirección IP pública creada en la sección anterior se agrega como parte del comando de la CLI y se adjunta a la máquina virtual durante la creación.

```azurecli-interactive
az vm create \
--name myVM \
--resource-group TutorVMRoutePref-rg \
--public-ip-address myPublicIP \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username azureuser
```

## <a name="verify-internet-routing-preference"></a>Comprobación de la preferencia de enrutamiento de Internet

Use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) para comprobar que la preferencia de enrutamiento de **Internet** está configurada para la dirección IP pública.

```azurecli-interactive
az network public-ip show \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --query ipTags \
    --output tsv
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con la máquina virtual y la dirección IP pública, elimine el grupo de recursos y todos los recursos que contiene con [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
  az group delete \
    --name TutorVMRoutePref-rg
```

## <a name="next-steps"></a>Pasos siguientes

En el siguiente artículo aprenderá a crear una máquina virtual con preferencia de enrutamiento mixto:
> [!div class="nextstepaction"]
> [Configuración de ambas opciones de preferencias de enrutamiento para una máquina virtual](routing-preference-mixed-network-adapter-portal.md)

