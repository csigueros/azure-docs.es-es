---
title: NAT Gateway administrado (versión preliminar)
description: Aprenda a crear un clúster de AKS con integración de NAT administrada.
services: container-service
ms.topic: article
ms.date: 10/26/2021
ms.author: juda
ms.openlocfilehash: fa93f4d0f72e2c8060a934f177db2dd53b96c7c5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478218"
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
az aks create --resource-group myresourcegroup 
    --name natcluster  \
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


## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información sobre Azure NAT Gateway, consulte [Azure NAT Gateway][nat-docs].

<!-- LINKS - internal -->


<!-- LINKS - external-->
[nat-docs]: ../virtual-network/nat-gateway/nat-overview.md
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
