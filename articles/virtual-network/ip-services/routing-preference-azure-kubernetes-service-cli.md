---
title: 'Tutorial: Configuración de preferencias de enrutamiento para una instancia de Azure Kubernetes Service mediante la CLI de Azure'
titlesuffix: Azure virtual network
description: Use este tutorial para aprender a configurar las preferencias de enrutamiento para una instancia de Azure Kubernetes Service.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 277f861b0816b0c7eaf0267fc80f1b15d12509d5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369689"
---
# <a name="tutorial-configure-routing-preference-for-an-azure-kubernetes-service-using-the-azure-cli"></a>Tutorial: Configuración de preferencias de enrutamiento para una instancia de Azure Kubernetes Service mediante la CLI de Azure

En este artículo se muestra cómo configurar las preferencias de enrutamiento mediante la red del ISP (opción de **Internet**) para un clúster de Kubernetes mediante la CLI de Azure. La preferencia de enrutamiento se establece mediante la creación de una dirección IP pública de tipo preferencia de enrutamiento **Internet** que se utiliza después al crear el clúster de AKS.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una dirección IP pública con la preferencia de enrutamiento de **Internet**.
> * Cree un clúster de Azure Kubernetes con la dirección IP pública de preferencia de enrutamiento de **Internet**.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- En este artículo se necesita la versión 2.0.49 de la CLI de Azure, o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para crear un grupo de recursos, use el comando [az group create](/cli/azure/group#az_group_create). En el siguiente ejemplo se crea un grupo de recursos en la región **Este de EE. UU.** de Azure:

```azurecli-interactive
  az group create \
    --name TutorAKSRP-rg \
    --location eastus

```

## <a name="create-public-ip-with-internet-routing-preference"></a>Creación de una dirección IP pública con preferencia de enrutamiento de Internet

Cree una dirección IP pública con preferencia de enrutamiento de tipo **Internet** mediante el comando [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

El siguiente comando crea una nueva dirección IP pública con preferencia de enrutamiento de **Internet** en la región de Azure del **Este de EE. UU.** .

```azurecli-interactive
  az network public-ip create \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```
> [!NOTE]
>  Actualmente, la preferencia de enrutamiento solo admite direcciones IP públicas IPV4.

## <a name="create-kubernetes-cluster-with-public-ip"></a>Creación de un clúster de Kubernetes con la dirección IP pública

Coloque el identificador de la dirección IP pública creada anteriormente en una variable para su uso posterior. Para recuperar la dirección IP pública, use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show).

El comando siguiente recupera el identificador de IP pública y lo coloca en una variable para usarla en el comando siguiente.

```azurecli-interactive
  export resourceid=$(az network public-ip show \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --query id \
    --output tsv)
```

Use [az aks create](/cli/azure/aks#az_aks_create) para crear el clúster de Kubernetes.

El siguiente comando crea el clúster de Kubernetes y usa la variable para la dirección IP pública creada en el paso anterior.

```azurecli-interactive
  az aks create \
    --resource-group TutorAKSRP-rg \
    --name MyAKSCluster \
    --load-balancer-outbound-ips $resourceid \
    --generate-ssh-key
```

>[!NOTE]
>El clúster de AKS tarda unos minutos en implementarse.

Para realizar la validación, busque la dirección IP pública creada en el paso anterior en Azure Portal. La dirección IP pública está asociada al equilibrador de carga. El equilibrador de carga está asociado al clúster de Kubernetes, como se muestra a continuación:

  :::image type="content" source="./media/routing-preference-azure-kubernetes-service-cli/verify-aks-ip.png" alt-text="Captura de pantalla de la dirección IP pública del clúster de AKS.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, use el comando [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos, la dirección IP pública, el clúster de AKS y todos los recursos relacionados.

```azurecli-interactive
  az group delete \
    --name TutorAKSRP-rg
```

## <a name="next-steps"></a>Pasos siguientes

En el siguiente artículo aprenderá a crear una máquina virtual con preferencia de enrutamiento mixto:
> [!div class="nextstepaction"]
> [Configuración de ambas opciones de preferencias de enrutamiento para una máquina virtual](routing-preference-mixed-network-adapter-portal.md)