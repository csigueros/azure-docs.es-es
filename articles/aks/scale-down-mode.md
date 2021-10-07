---
title: Uso del modo de reducción vertical en el clúster de Azure Kubernetes Service (AKS) (versión preliminar)
titleSuffix: Azure Kubernetes Service
description: Aprenda a usar el modo de reducción vertical en Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/01/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 6bd1f4c70d9c427d7f6487040453e7ecae199717
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800125"
---
# <a name="use-scale-down-mode-to-deletedeallocate-nodes-in-azure-kubernetes-service-aks-preview"></a>Uso del modo de reducción vertical para eliminar o desasignar nodos en Azure Kubernetes Service (AKS) (versión preliminar)

De manera predeterminada, las operaciones de escalado vertical realizadas manualmente o por el escalador automático del clúster exigen la asignación y el aprovisionamiento de nuevos nodos, mientras que las operaciones de reducción vertical eliminan nodos. El modo de reducción vertical permite decidir si se quieren eliminar o desasignar los nodos del clúster de Azure Kubernetes Service (AKS) tras reducir verticalmente. 

Cuando una máquina virtual de Azure está en estado `Stopped` (desasignado), no se cobra por los recursos de proceso de la máquina virtual. Pero se sigue teniendo que pagar por los discos de almacenamiento de datos y del sistema operativo conectados a la máquina virtual. Esto también significa que las imágenes de contenedor se conservan en esos nodos. Para obtener más información, vea [Estados y facturación de Azure Virtual Machines][state-billing-azure-vm]. Este comportamiento permite velocidades de operación más rápidas, ya que la implementación aprovecha las imágenes almacenadas en caché. El modo de reducción vertical permite que ya no haya que aprovisionar previamente nodos ni extraer previamente imágenes de contenedor, lo que ahorra costos de proceso.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de empezar

> [!WARNING]
> Para conservar las máquinas virtuales desasignadas, debe establecer el modo de reducción vertical en Desasignar. Eso incluye las máquinas virtuales que se han desasignado mediante API de IaaS (API de conjunto de escalado de máquinas virtuales). Al establecer el modo de reducción vertical en Eliminar, se quitan las máquinas virtuales desasignadas.

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, consulte el inicio rápido de AKS [mediante la CLI de Azure][aks-quickstart-cli] o [mediante Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Limitaciones

- No se admiten discos de [SO efímero][ephemeral-os]. Asegúrese de especificar discos del sistema operativo administrados mediante `--node-osdisk-type Managed` al crear un clúster o un grupo de nodos.
- No se admiten [grupos de nodos de acceso puntual][spot-node-pool].

### <a name="install-aks-preview-cli-extension"></a>Instalación de la extensión aks-preview de la CLI

También se necesita la versión 0.5.30 o posterior de la extensión *aks-preview* de la CLI de Azure. Instale la extensión de la CLI de Azure *aks-preview* mediante el comando [az extension add][az-extension-add]. También puede instalar las actualizaciones disponibles mediante el comando [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-aks-scaledownmodepreview-preview-feature"></a>Registro de la característica en vista previa (GB) `AKS-ScaleDownModePreview`

Para usar la característica, también debe habilitar la marca de característica `AKS-ScaleDownModePreview` en la suscripción.

Registre la marca de la característica `AKS-ScaleDownModePreview` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ScaleDownModePreview"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ScaleDownModePreview')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="using-scale-down-mode-to-deallocate-nodes-on-scale-down"></a>Uso del modo de reducción vertical para desasignar nodos al reducir verticalmente

Al establecer `--scale-down-mode Deallocate`, los nodos se desasignan durante una reducción vertical del clúster o el grupo de nodos. Todos los nodos desasignados se detienen. Cuando el clúster o el grupo de nodos necesita escalar verticalmente, los nodos desasignados se inician antes de aprovisionar los nodos nuevos.

En este ejemplo se crea un nuevo grupo de nodos con 20 nodos y se especifica que, al reducir verticalmente, estos se desasignan por medio de `--scale-down-mode Deallocate`.

```azurecli-interactive
az aks nodepool add --node-count 20 --scale-down-mode Deallocate --node-osdisk-type Managed --max-pods 10 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

Al escalar el grupo de nodos y cambiar el número de nodos a 5, se desasignan 15 nodos.

```azurecli-interactive
az aks nodepool scale --node-count 5 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="deleting-previously-deallocated-nodes"></a>Eliminación de nodos previamente desasignados

Para eliminar los nodos desasignados, puede cambiar el modo de reducción vertical a `Delete`; para ello, establezca `--scale-down-mode Delete`. Ahora se eliminan los 15 nodos desasignados.

```azurecli-interactive
az aks nodepool update --scale-down-mode Delete --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

> [!NOTE]
> Al cambiar el modo de reducción vertical de `Deallocate` a `Delete` y, después, de nuevo a `Deallocate`, se eliminarán todos los nodos desasignados mientras se mantiene el grupo de nodos en modo de reducción vertical `Deallocate`.

## <a name="using-scale-down-mode-to-delete-nodes-on-scale-down"></a>Uso del modo de reducción vertical para eliminar nodos al reducir verticalmente

El comportamiento predeterminado de AKS sin usar el modo de reducción vertical es eliminar los nodos al reducir verticalmente el clúster. Con el modo de reducción vertical, esto se puede lograr explícitamente si se establece `--scale-down-mode Delete`.

En este ejemplo se crea un nuevo grupo de nodos y se especifica que los nodos se van a eliminar al reducir verticalmente mediante `--scale-down-mode Delete`. Las operaciones de escalado se controlan mediante el escalador automático del clúster.

```azurecli-interactive
az aks nodepool add --enable-cluster-autoscaler --min-count 1 --max-count 10 --max-pods 10 --node-osdisk-type Managed --scale-down-mode Delete --name nodepool3 --cluster-name myAKSCluster --resource-group myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre la actualización del clúster de AKS, vea [Actualización de un clúster de Azure Kubernetes Service (AKS)][aks-upgrade]
- Para obtener más información sobre el escalador automático del clúster, vea [Escalar automáticamente un clúster para satisfacer las necesidades de la aplicación en Azure Kubernetes Service (AKS)][cluster-autoscaler]

<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[cluster-autoscaler]: cluster-autoscaler.md
[ephemeral-os]: cluster-configuration.md#ephemeral-os
[state-billing-azure-vm]: ../virtual-machines/states-billing.md
[spot-node-pool]: spot-node-pool.md