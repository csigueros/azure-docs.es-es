---
title: Habilitar los controladores de interfaz de almacenamiento de contenedores (CSI) en Azure Kubernetes Service (AKS)
description: Aprenda a habilitar los controladores de interfaz de almacenamiento de contenedores (CSI) para discos de Azure y Azure Files en un clúster de Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 7aad80816a7bf8f6a1c55c8a4f1de1d4761fc523
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733597"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks"></a>Habilitación de los controladores de Container Storage Interface (CSI) para discos de Azure y Azure Files en Azure Kubernetes Service (AKS)

La interfaz de almacenamiento de contenedores (CSI) es un estándar para exponer sistemas de almacenamiento de archivos y bloques arbitrarios a cargas de trabajo en contenedores en Kubernetes. Gracias a la adopción y al uso de CSI, Azure Kubernetes Service (AKS) puede escribir, implementar e iterar complementos para exponer nuevos sistemas de almacenamiento o mejorar los existentes en Kubernetes sin tener que tocar el código principal de Kubernetes ni esperar a sus ciclos de versión.

La compatibilidad con el controlador de almacenamiento CSI en AKS permite el uso nativo de:
- [*Discos de Azure*](azure-disk-csi.md), que se pueden usar para crear un recurso *DataDisk* de Kubernetes. Los discos de Azure pueden usar Azure Premium Storage, respaldado por SSD de alto rendimiento, o bien Azure Standard Storage, respaldado por HHD normales o SSD estándar. Para la mayoría de las cargas de trabajo de producción y desarrollo, utilice Premium Storage. Los discos de Azure se montan como *ReadWriteOnce*, por lo que solo están disponibles para un único pod. Para los volúmenes de almacenamiento a los que pueden acceder varios pods simultáneamente, use Azure Files.
- [*Azure Files*](azure-files-csi.md), que se puede usar para montar un recurso compartido de SMB 3.0 respaldado por una cuenta de Azure Storage en los pods. Con Azure Files, puede compartir datos entre varios nodos y pods. Azure Files puede usar Azure Standard Storage respaldado por HDD normales, o bien Azure Premium Storage respaldado por SSD de alto rendimiento.

> [!IMPORTANT]
> A partir de la versión 1.21 de Kubernetes, Kubernetes solo usará controladores CSI de forma predeterminada. Estos controladores son el futuro de la compatibilidad con el almacenamiento en Kubernetes.
>
> Los *controladores en árbol* hacen referencia a los controladores de almacenamiento actuales que forman parte del código principal de Kubernetes frente a los nuevos controladores CSI, que son complementos.

## <a name="limitations"></a>Limitaciones

- Esta característica solo se puede establecer durante el momento de la creación del clúster.
- La versión mínima secundaria de Kubernetes compatible con los controladores CSI es la 1.17.
- La clase de almacenamiento predeterminada será `managed-csi`.

## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Creación de un clúster que pueda usar controladores de almacenamiento CSI

Cree un clúster que pueda usar controladores de almacenamiento CSI para discos de Azure y Azure Files mediante los siguientes comandos de la CLI. Use la marca `--aks-custom-headers` para establecer la característica `EnableAzureDiskFileCSIDriver`.

Crear un grupo de recursos de Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Cree el clúster de AKS con compatibilidad con controladores de almacenamiento CSI:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Si desea crear clústeres en los controladores de almacenamiento en árbol en lugar de los controladores de almacenamiento CSI, puede hacerlo omitiendo el parámetro personalizado `--aks-custom-headers`.


Compruebe cuántos volúmenes basados en disco de Azure puede conectar a este nodo mediante la ejecución de:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Pasos siguientes

- Para usar el controlador CSI para discos de Azure, vea [Uso de discos de Azure con controladores CSI](azure-disk-csi.md).
- Para usar el controlador CSI para Azure Files, vea [Uso de Azure Files con controladores CSI](azure-files-csi.md).
- Para más información sobre los procedimientos recomendados, consulte [Procedimientos recomendados para el almacenamiento y las copias de seguridad en Azure Kubernetes Service][operator-best-practices-storage].

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register