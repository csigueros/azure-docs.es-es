---
title: Instantánea de grupos de nodos de Azure Kubernetes Service (AKS) (versión preliminar)
description: Aprenda a crear instantáneas de grupos de nodos de clúster de AKS, y a crear clústeres y grupos de nodos a partir de una instantánea.
ms.service: container-service
ms.topic: article
ms.date: 09/11/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: b547b65fc3f3086e90da073fb898de3980938147
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858397"
---
# <a name="azure-kubernetes-service-aks-node-pool-snapshot-preview"></a>Instantánea de grupos de nodos de Azure Kubernetes Service (AKS) (versión preliminar)

AKS publica una nueva imagen de nodo semanalmente y cada nuevo clúster, nuevo grupo de nodos o clúster de actualización siempre recibirá la imagen más reciente que pueda hacer difícil mantener los entornos coherentes y tener entornos repetibles.

Las instantáneas de grupos de nodos permiten tomar una instantánea de configuración del grupo de nodos y, a continuación, crear nuevos grupos de nodos o nuevos clústeres basados en esa instantánea, siempre que se admitan esa configuración y la versión de Kubernetes. Para obtener más información sobre las ventanas de compatibilidad, consulte [Versiones compatibles de Kubernetes en AKS][supported-versions].

La instantánea es un recurso de Azure que contendrá la información de configuración del grupo de nodos de origen, como la versión de la imagen de nodo, la versión de Kubernetes, el tipo de sistema operativo y la SKU del sistema operativo. A continuación, puede hacer referencia a este recurso de instantánea y a los valores respectivos de su configuración para crear cualquier nuevo grupo de nodos o clúster basado en este.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, consulte el inicio rápido de AKS [mediante la CLI de Azure][aks-quickstart-cli] o [mediante Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Limitaciones

- Cualquier grupo de nodos o clúster creado a partir de una instantánea debe usar una VM de la misma familia de máquinas virtuales que la instantánea; por ejemplo, no puede crear un nuevo grupo de nodos de la serie N basado en una instantánea capturada de un grupo de nodos de la serie D porque las imágenes de nodo en esos casos son estructuralmente diferentes.
- Durante la versión preliminar, las instantáneas se deben crear y usar en la misma región que el grupo de nodos de origen.

### <a name="install-aks-preview-cli-extension"></a>Instalación de la extensión aks-preview de la CLI

También se necesita la versión 0.5.40 o posterior de la extensión *aks-preview* de CLI de Azure. Instale la extensión de la CLI de Azure *aks-preview* mediante el comando [az extension add][az-extension-add]. También puede instalar las actualizaciones disponibles mediante el comando [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-snapshotpreview-preview-feature"></a>Registro de la característica en vista previa (GB) `SnapshotPreview`

Para usar la característica, también debe habilitar la marca de característica `SnapshotPreview` en la suscripción.

Registre la marca de la característica `SnapshotPreview` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "SnapshotPreview"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.ContainerService/SnapshotPreview')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="take-a-node-pool-snapshot"></a>Tomar una instantánea de un grupo de nodos

Para tomar una instantánea de un grupo de nodos, primero necesitará el identificador de recurso del grupo de nodos, que puede obtener con el siguiente comando:

```azurecli-interactive
NODEPOOL_ID=$(az aks nodepool show --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --query id -o tsv)
```

> [!IMPORTANT]
> El grupo de nodos de AKS debe crearse o actualizarse después del 10 de noviembre de 2021 para que se pueda tomar una instantánea de este.

Ahora, para tomar una instantánea del grupo de nodos anterior, usará el comando `az aks snapshot` de la CLI.

```azurecli-interactive
az aks snapshot create --name MySnapshot --resource-group MyResourceGroup --nodepool-id $NODEPOOL_ID --location eastus
```

## <a name="create-a-node-pool-from-a-snapshot"></a>Creación de un grupo de nodos a partir de una instantánea

En primer lugar, necesitará el identificador de recurso de la instantánea que se creó anteriormente, que puede obtener con el siguiente comando:

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Ahora, podemos usar el comando siguiente para agregar un nuevo grupo de nodos basado en esta instantánea.

```azurecli-interactive
az aks nodepool add --name np2 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="upgrading-a-node-pool-to-a-snapshot"></a>Actualización de un grupo de nodos a una instantánea

Puede actualizar un grupo de nodos a una configuración de instantánea siempre que la versión de Kubernetes de la instantánea y la versión de la imagen del nodo sean más recientes que las versiones del grupo de nodos actual.

En primer lugar, necesitará el identificador de recurso de la instantánea que se creó anteriormente, que puede obtener con el siguiente comando:

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Ahora, podemos usar este comando para actualizar este grupo de nodos a esta configuración de instantánea.

```azurecli-interactive
az aks nodepool upgrade --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

> [!NOTE]
> La versión de la imagen del grupo de nodos será la misma que incluye la instantánea y seguirá siendo la misma en todas las operaciones de escalado. Sin embargo, si este grupo de nodos se actualiza o se realiza una actualización de la imagen del nodo sin proporcionar un identificador de instantánea, la imagen del nodo se actualizará a la versión más reciente.

## <a name="create-a-cluster-from-a-snapshot"></a>Creación de un clúster a partir de una instantánea

Al crear un clúster a partir de una instantánea, el grupo de sistemas original del clúster se creará a partir de la configuración de la instantánea.

En primer lugar, necesitará el identificador de recurso de la instantánea que se creó anteriormente, que puede obtener con el siguiente comando:

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Ahora, podemos usar este comando para crear este clúster fuera de la configuración de la instantánea.

```azurecli-interactive
az aks cluster create --name myAKSCluster2 --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="next-steps"></a>Pasos siguientes

- Consulte las [notas de la versión de AKS](https://github.com/Azure/AKS/releases) para más información sobre las imágenes de nodo más recientes.
- Obtenga información sobre cómo actualizar la versión de Kubernetes con [Actualización de un clúster de AKS][upgrade-cluster].
- Aprenda a actualizar la versión de la imagen del nodo con la [Actualización de la imagen de nodo][node-image-upgrade].
- Obtenga más información sobre varios grupos de nodos y cómo actualizar los grupos de nodos con [Creación y administración de varios grupos de nodos][use-multiple-node-pools].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[supported-versions]: supported-kubernetes-versions.md
[upgrade-cluster]: upgrade-cluster.md
[node-image-upgrade]: node-image-upgrade.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register