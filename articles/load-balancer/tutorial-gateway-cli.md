---
title: 'Tutorial: Creación de un equilibrador de carga de puerta de enlace: CLI de Azure'
titleSuffix: Azure Load Balancer
description: Use este tutorial para aprender a crear un equilibrador de carga de puerta de enlace mediante la CLI de Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 13435307985f3471800a6bfc3697c7bc0fc58ee1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093310"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-cli"></a>Tutorial: Creación de un equilibrador de carga de puerta de enlace mediante la CLI de Azure

Azure Load Balancer consta de SKU estándar, básicas y de puerta de enlace. La instancia de Load Balancer de puerta de enlace se usa para la inserción transparente de aplicaciones virtuales de red (NVA). Use la instancia de Load Balancer de puerta de enlace en escenarios que requieran alto rendimiento y alta escalabilidad de NVA.

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Registre la característica en vista previa.
> * Cree una red virtual.
> * Cree un grupo de seguridad de red.
> * Cree un equilibrador de carga de puerta de enlace.
> * Encadene un front-end de equilibrador de carga a un equilibrador de carga de puerta de enlace.

> [!IMPORTANT]
> La instancia de Azure Gateway Load Balancer se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requiere la versión 2.0.28 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

- Una cuenta de Azure con una suscripción activa: [cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Una SKU estándar pública existente en Azure Load Balancer. Para obtener más información sobre cómo crear un equilibrador de carga, consulte **[Creación de un equilibrador de carga público mediante la CLI de Azure](quickstart-load-balancer-standard-public-cli.md)** .
    - Para los fines detallados en este tutorial, el equilibrador de carga existente en los ejemplos se denomina **myLoadBalancer**.

## <a name="register-preview-feature"></a>Registrar la característica en vista previa

Como parte de la versión preliminar pública del equilibrador de carga de puerta de enlace, el proveedor debe estar registrado en su suscripción de Azure.

Para registrar la característica de proveedor **AllowGatewayLoadBalancer**, use [az feature register](/cli/azure/feature#az_feature_register):

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

Para registrar el proveedor de recursos **Microsoft.Network**, use [az provider register](/cli/azure/provider#az_provider_register):

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
  az group create \
    --name TutorGwLB-rg \
    --location eastus

```

## <a name="configure-virtual-network"></a>Configurar la red virtual

Se necesita una red virtual para los recursos que se encuentran en el grupo de back-end del equilibrador de carga de puerta de enlace.  

### <a name="create-virtual-network"></a>Creación de una red virtual

Cree la red virtual mediante [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
  az network vnet create \
    --resource-group TutorGwLB-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-bastion-public-ip-address"></a>Creación de una dirección IP pública de tipo bastión

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear una dirección IP pública para el host de Azure Bastion.

```azurecli-interactive
az network public-ip create \
    --resource-group TutorGwLB-rg \
    --name myBastionIP \
    --sku Standard \
    --zone 1 2 3
```

### <a name="create-bastion-subnet"></a>Creación de una subred bastión

Use [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) para crear una subred bastión.

```azurecli-interactive
az network vnet subnet create \
    --resource-group TutorGwLB-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/27
```

### <a name="create-bastion-host"></a>Creación de un host bastión

Use [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para implementar un host bastión para la administración segura de recursos en la red virtual.

```azurecli-interactive
az network bastion create \
    --resource-group TutorGwLB-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

El host de Azure Bastion tarda unos minutos en implementarse.

## <a name="configure-nsg"></a>Configuración de NSG

Use el ejemplo siguiente para crear un grupo de seguridad de red. Configurará las reglas de NSG necesarias para el tráfico de red en la red virtual creada anteriormente.

### <a name="create-nsg"></a>Creación de NSG

Use [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) para crear el grupo de seguridad de red.

```azurecli-interactive
  az network nsg create \
    --resource-group TutorGwLB-rg \
    --name myNSG
```

### <a name="create-nsg-rules"></a>Creación de reglas de NSG

Use [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) para crear las reglas para el grupo de seguridad de red.

```azurecli-interactive
  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100

  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll-TCP-Out \
    --protocol 'TCP' \
    --direction outbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100
```

## <a name="configure-gateway-load-balancer"></a>Configuración de un equilibrador de carga de puerta de enlace

En esta sección, creará la configuración e implementará el equilibrador de carga de puerta de enlace.  

### <a name="create-gateway-load-balancer"></a>Creación de un equilibrador de carga de puerta de enlace

Cree el equilibrador de carga mediante [az network lb create](/cli/azure/network/lb#az_network_lb_create).

```azurecli-interactive
  az network lb create \
    --resource-group TutorGwLB-rg \
    --name myLoadBalancer-gw \
    --sku Gateway \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --backend-pool-name myBackendPool \
    --frontend-ip-name myFrontEnd
```

### <a name="create-tunnel-interface"></a>Creación de una interfaz de túnel

Se crea automáticamente una interfaz interna con la CLI de Azure con **`--identifier`** de **900** y **`--port`** de **10800**.

Usará [az network lb address-pool tunnel-interface add](/cli/azure/network/lb/address-pool/tunnel-interface#az_network_lb_address_pool_tunnel_interface_add) para crear una interfaz de túnel externa para el equilibrador de carga. 

```azurecli-interactive
  az network lb address-pool tunnel-interface add \
    --address-pool myBackEndPool \
    --identifier '901' \
    --lb-name myLoadBalancer-gw \
    --protocol VXLAN \
    --resource-group TutorGwLB-rg \
    --type External \
    --port '10801'
```

### <a name="create-health-probe"></a>Creación de un sondeo de estado
Es necesario realizar un sondeo de estado para supervisar el estado de las instancias de back-end en el equilibrador de carga. Para crear un sondeo de estado, debe usar el comando [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create).

```azurecli-interactive
  az network lb probe create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myHealthProbe \
    --protocol http \
    --port 80 \
    --path '/' \
    --interval '5' \
    --threshold '2'
    
```

### <a name="create-load-balancing-rule"></a>Creación de una regla de equilibrio de carga

El tráfico destinado a las instancias de back-end se enruta con una regla de equilibrio de carga. Para crear una regla de equilibrio de carga, debe usar el comando [az network lb rule create](/cli/azure/network/lb/probe#az_network_lb_rule_create).

```azurecli-interactive
  az network lb rule create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myLBRule \
    --protocol All \
    --frontend-port 0 \
    --backend-port 0 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Adición de aplicaciones virtuales de red al grupo de back-end del equilibrar la carga de la puerta de enlace
Implemente las NVA a través de Azure Marketplace. Una vez implementadas, agregue las máquinas virtuales al grupo de back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add).

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Encadene un front-end del equilibrador de carga a un equilibrador de carga de puerta de enlace.

En este ejemplo, encadenará el front-end de un equilibrador de carga estándar al equilibrador de carga de puerta de enlace. 

Asimismo, agregará el front-end a la dirección IP de front-end de un equilibrador de carga existente en la suscripción.

Use [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_az_network_lb_frontend_ip_show) para colocar el id. de recurso del front-end del equilibrador de carga de puerta de enlace en una variable.

Use [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) para encadenar el front-end del equilibrador de carga de puerta de enlace al equilibrador de carga existente.

```azurecli-interactive
  feid=$(az network lb frontend-ip show \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myFrontend \
    --query id \
    --output tsv)

  az network lb frontend-ip update \
    --resource-group CreatePubLBQS-rg \
    --name myFrontendIP \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIP \
    --gateway-lb $feid

```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede usar el comando [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos, el equilibrador de carga y todos los recursos restantes.

```azurecli-interactive
  az group delete \
    --name TutorGwLB-rg
```

## <a name="next-steps"></a>Pasos siguientes

Cree aplicaciones virtuales de red en Azure. 

Al crear las aplicaciones virtuales de red, elija los recursos creados en este tutorial:

* Virtual network

* Subnet

* Grupo de seguridad de red

* Equilibrador de carga de puerta de enlace

Pase al siguiente artículo para aprender a crear una instancia de Azure Load Balancer entre regiones.
> [!div class="nextstepaction"]
> [Equilibrador de carga entre regiones](tutorial-cross-region-powershell.md)
