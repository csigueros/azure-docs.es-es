---
title: Azure Kubernetes Service (AKS) con acuerdo de nivel de servicio de tiempo de actividad
description: Obtenga información sobre la oferta opcional de acuerdo de nivel de servicio de tiempo de actividad para el servidor de API de Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 77bdfb4ed75b9287b911a6b0d2742c235b37e297
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122325072"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Acuerdo de nivel de servicio de tiempo de actividad de Azure Kubernetes Service (AKS)

El Acuerdo de Nivel de Servicio del tiempo de actividad es una nivel para habilitar un mejor Acuerdo de Nivel de Servicio con respaldo financiero para un clúster AKS. Los clústeres con Acuerdo de Nivel de Servicio del tiempo de actividad, también se consideran de nivel de pago en las API REST de AKS, incluyen una mayor cantidad de recursos del plano de control y se escalan automáticamente para satisfacer la carga del clúster. El acuerdo de nivel de servicio de tiempo de actividad garantiza una disponibilidad del 99,95 % del punto de conexión del servidor de API de Kubernetes para los clústeres que usan [zonas de disponibilidad][availability-zones], y una disponibilidad del 99,9 % de los clústeres que no utilizan zonas de disponibilidad. AKS usa réplicas de nodo maestro en los dominios de actualización y de error para garantizar que se cumplan los requisitos del acuerdo de nivel de servicio.

AKS recomienda el uso del Acuerdo de Nivel de Servicio del tiempo de actividad en las cargas de trabajo de producción para garantizar la disponibilidad de los componentes del plano de control. Por el contrario, los clústeres del nivel gratis incluyen menos réplicas y un número limitado de recursos para el plano de control y no son adecuados para cargas de trabajo de producción.

Aun así, los clientes pueden crear un número ilimitado de clústeres gratuitos con un objetivo de nivel de servicio (SLO) del 99,5 %, y elegir el objetivo de nivel de servicio que prefieran. 

> [!IMPORTANT]
> En el caso de los clústeres con bloqueo de salida, consulte la [limitación del tráfico de salida](limit-egress-traffic.md) para abrir los puertos adecuados.

## <a name="region-availability"></a>Disponibilidad en regiones

* El Acuerdo de Nivel de Servicio de tiempo de actividad está disponible en las regiones públicas y de Azure Government en las que [se admite AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).
* El Acuerdo de Nivel de Servicio de tiempo de actividad está disponible para [clústeres AKS privados][private-clusters] en todas las regiones públicas en las que se admite AKS.

## <a name="sla-terms-and-conditions"></a>Términos y condiciones del acuerdo de nivel de servicio

El Acuerdo de Nivel de Servicio del tiempo de actividad es una característica de pago y se habilita en cada clúster. El precio del acuerdo de nivel de servicio de tiempo de actividad viene determinado por el número de clústeres discretos, no por el tamaño de los clústeres individuales. Puede ver los [Detalles de precios de los acuerdos de nivel de servicio de tiempo de actividad](https://azure.microsoft.com/pricing/details/kubernetes-service/) para obtener más información.

## <a name="before-you-begin"></a>Antes de empezar

* Instale la versión 2.8.0 de la [CLI de Azure](/cli/azure/install-azure-cli), o cualquier versión posterior.

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Creación de un clúster con un Acuerdo de Nivel de Servicio de tiempo de actividad

Para crear un nuevo clúster con el acuerdo de nivel de servicio de tiempo de actividad, use la CLI de Azure.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Use el comando [`az aks create`][az-aks-create] para crear un clúster de AKS. En el siguiente ejemplo se crea un clúster denominado *myAKSCluster* con un nodo. La operación tarda varios minutos en completarse:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```

Transcurridos unos minutos, el comando se completa y devuelve información en formato JSON sobre el clúster. El siguiente fragmento JSON muestra el nivel de pago de la SKU, que indica que el clúster tiene habilitado el Acuerdo de Nivel de Servicio de tiempo de actividad.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Modificación de un clúster existente para usar un Acuerdo de Nivel de Servicio de tiempo de actividad

Opcionalmente, puede actualizar los clústeres existentes para usar el Acuerdo de Nivel de Servicio de tiempo de actividad.

Si creó un clúster de AKS con los pasos anteriores, elimine el grupo de recursos:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Cree un nuevo grupo de recursos:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Cree un nuevo clúster y no use el Acuerdo de Nivel de Servicio de tiempo de actividad:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Use el comando [`az aks update`][az-aks-update] para actualizar el clúster existente:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
```

El siguiente fragmento JSON muestra el nivel de pago de la SKU, que indica que el clúster tiene habilitado el Acuerdo de Nivel de Servicio de tiempo de actividad.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="opt-out-of-uptime-sla"></a>Exclusión del Acuerdo de Nivel de Servicio de tiempo de actividad

Puede actualizar el clúster para cambiar al nivel gratis y excluirse del Acuerdo de Nivel de Servicio de tiempo de actividad.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
```

## <a name="clean-up"></a>Limpieza

Para evitar cargos, limpie los recursos que haya creado. Para eliminar el clúster, use el comando [`az group delete`][az-group-delete] para eliminar el grupo de recursos de AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Pasos siguientes

Use [Availability Zones][availability-zones] para aumentar la alta disponibilidad con las cargas de trabajo del clúster de AKS.

Configure el clúster para [limitar el tráfico de salida](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
