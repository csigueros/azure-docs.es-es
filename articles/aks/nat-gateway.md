---
title: NAT Gateway administrado (versión preliminar)
description: Aprenda a crear un clúster de AKS con integración de NAT administrada.
services: container-service
ms.topic: article
ms.date: 10/26/2021
ms.author: juda
ms.openlocfilehash: 87edce54391661fe986365ad45d72bfeee41c761
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179605"
---
# <a name="managed-nat-gateway-preview"></a>NAT Gateway administrado (versión preliminar)

Aunque los clientes de AKS pueden enrutar el tráfico de salida a través de una instancia de Azure Load Balancer, hay limitaciones en la cantidad de flujos de tráfico de salida que se admite. 

Azure NAT Gateway permite hasta 64 000 flujos de tráfico UDP y TCP de salida por dirección IP con un máximo de 16 direcciones IP.

En este artículo se muestra cómo crear un clúster de AKS con NAT Gateway administrado para el tráfico de salida.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de empezar

Para usar NAT Gateway administrado, debe tener lo siguiente:

* La versión más reciente de la CLI de Azure
* La versión 0.5.31 o posterior de la extensión `aks-preview`
* Kubernetes versión 1.20.x o posterior


### <a name="register-the-aks-natgatewaypreview-feature-flag"></a>Registro de la marca de característica `AKS-NATGatewayPreview`

Para usar la característica NAT Gateway, habilite la marca de característica `AKS-NATGatewayPreview` en su suscripción. 

```azurecli
az feature register --namespace "Microsoft.ContainerService" --name "AKS-NATGatewayPreview"
```
Puede comprobar el estado del registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-NATGatewayPreview')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```


## <a name="create-an-aks-cluster-with-a-managed-nat-gateway"></a>Creación de un clúster de AKS con NAT Gateway administrado
Para crear un clúster de AKS con una nueva instancia de NAT Gateway administrado, use `--outbound-type managedNATGateway`, así como `--nat-gateway-managed-outbound-ip-count` y `--nat-gateway-idle-timeout`, al ejecutar `az aks create`. En el ejemplo siguiente se crea un grupo de recursos *myresourcegroup* y, luego, un clúster de AKS *natcluster* en *myresourcegroup* con una instancia de NAT Gateway administrado, dos direcciones IP de salida y un tiempo de espera de inactividad de 30 segundos.


```azurecli-interactive
az group create --name myresourcegroup --location southcentralus
```

```azurecli-interactive
az aks create \
    --resource-group myresourcegroup \
    --name natcluster \
    --node-count 3 \
    --outbound-type managedNATGateway \ 
    --nat-gateway-managed-outbound-ip-count 2 \
    --nat-gateway-idle-timeout 30
```

> [!IMPORTANT]
> Si no se especifica ningún valor para la dirección IP de salida, el valor predeterminado es uno.

### <a name="update-the-number-of-outbound-ip-addresses"></a>Actualización del número de direcciones IP de salida
Para actualizar la dirección IP de salida o el tiempo de espera de inactividad, use `--nat-gateway-managed-outbound-ip-count` o `--nat-gateway-idle-timeout` al ejecutar `az aks update`. Por ejemplo:

```azurecli-interactive
az aks update \ 
    --resource-group myresourcegroup \
    --name natcluster\
    --nat-gateway-managed-outbound-ip-count 5
```

## <a name="create-an-aks-cluster-with-a-user-assigned-nat-gateway"></a>Creación de un clúster de AKS con una instancia de NAT Gateway asignada por el usuario
Para crear un clúster de AKS con una instancia de NAT Gateway asignada por el usuario, use `--outbound-type userAssignedNATGateway` al ejecutar `az aks create`. Esta configuración requiere traer sus propias redes (mediante [Kubenet][byo-vnet-kubenet] o [Azure CNI][byo-vnet-azure-cni]) y que la instancia de NAT Gateway esté preconfigurada en la subred. Los siguientes comandos crean los recursos necesarios para este escenario. Asegúrese de ejecutarlos todos en la misma sesión para que los valores almacenados en las variables estén aún disponibles para el comando `az aks create`.

1. Creación del grupo de recursos:
    ```azurecli-interactive
    az group create --name myresourcegroup \
        --location southcentralus
    ```

2. Cree una identidad administrada para los permisos de red y almacene el identificador en `$IDENTITY_ID` para su uso posterior:
    ```azurecli-interactive
    IDENTITY_ID=$(az identity create \
        --resource-group myresourcegroup \
        --name natclusterid \
        --location southcentralus \
        --query id \
        --output tsv)
    ```

3. Cree una dirección IP pública para la instancia de NAT Gateway:
    ```azurecli-interactive
    az network public-ip create \
        --resource-group myresourcegroup \
        --name mynatgatewaypip \
        --location southcentralus \
        --sku standard
    ```

4. Cree la instancia de NAT Gateway:
    ```azurecli-interactive
    az network nat gateway create \
        --resource-group myresourcegroup \
        --name mynatgateway \
        --location southcentralus \
        --public-ip-addresses mynatgatewaypip
    ```

5. Cree una red virtual:
    ```azurecli-interactive
    az network vnet create \
        --resource-group myresourcegroup \
        --name myvnet \
        --location southcentralus \
        --address-prefixes 172.16.0.0/20 
    ```

6. Cree una subred en la red virtual mediante la instancia de NAT Gateway y almacene el identificador en `$SUBNET_ID` para su uso posterior:
    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet create \
        --resource-group myresourcegroup \
        --vnet-name myvnet \
        --name natcluster \
        --address-prefixes 172.16.0.0/22 \
        --nat-gateway mynatgateway \
        --query id \
        --output tsv)
    ```

7. Cree un clúster de AKS mediante la subred con la instancia de NAT Gateway y la identidad administrada:
    ```azurecli-interactive
    az aks create \
        --resource-group myresourcegroup \
        --name natcluster \
        --location southcentralus \
        --network-plugin azure \
        --vnet-subnet-id $SUBNET_ID \
        --outbound-type userAssignedNATGateway \
        --enable-managed-identity \
        --assign-identity $IDENTITY_ID
    ```

## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información sobre Azure NAT Gateway, consulte [Azure NAT Gateway][nat-docs].

<!-- LINKS - internal -->


<!-- LINKS - external-->
[nat-docs]: ../virtual-network/nat-gateway/nat-overview.md
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[byo-vnet-azure-cni]: configure-azure-cni.md
[byo-vnet-kubenet]: configure-kubenet.md