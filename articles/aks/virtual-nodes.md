---
title: Uso de nodos virtuales
titleSuffix: Azure Kubernetes Service
description: Información general sobre cómo usar el nodo virtual con Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 78be2edacdcf72a6274755413810fe1308510a79
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669130"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Creación y configuración de un clúster de Azure Kubernetes Service (AKS) para usar nodos virtuales

Para escalar rápidamente las cargas de trabajo de aplicaciones en un clúster de AKS, puede usar nodos virtuales. Con los nodos virtuales, tiene un aprovisionamiento rápido de pods y solo paga por segundo para el tiempo de ejecución. No es necesario que espere a que el escalador automático del clúster de Kubernetes implemente nodos de proceso de máquina virtual para ejecutar los pods adicionales. Los nodos virtuales solo son compatibles con los nodos y pods de Linux.

El complemento de nodos virtuales para AKS se basa en el proyecto de código abierto [Virtual Kubelet][virtual-kubelet-repo].

En este artículo se ofrece información general sobre la disponibilidad regional y los requisitos de red para usar nodos virtuales, así como las limitaciones conocidas.

## <a name="regional-availability"></a>Disponibilidad regional

Todas las regiones en las que ACI admita SKU de red virtual, se admiten para implementaciones de nodos virtuales. Para más detalles, consulte [Disponibilidad de recursos para Azure Container Instances en las regiones de Azure](../container-instances/container-instances-region-availability.md).

Para conocer las SKU de CPU y memoria disponibles en cada región, consulte [Disponibilidad de recursos para Azure Container Instances en las regiones de Azure: Grupos de contenedores de Linux](../container-instances/container-instances-region-availability.md#linux-container-groups).

## <a name="network-requirements"></a>Requisitos de red

Los nodos virtuales permiten la comunicación de red entre los pods que se ejecutan en Azure Container Instances (ACI) y el clúster de AKS. Para proporcionar esta comunicación, se crea una subred de red virtual y se asignan permisos delegados. Los nodos virtuales solo funcionan con clústeres de AKS creados mediante redes *avanzadas* (Azure CNI). De forma predeterminada, los clústeres de AKS se crean con redes *básicas* (kubenet).

Los pods que se ejecutan en Azure Container Instances (ACI) necesitan acceder al punto de conexión del servidor de API de AKS para configurar las funciones de red.

## <a name="known-limitations"></a>Restricciones conocidas

La funcionalidad de nodos virtuales es muy dependiente del conjunto de características de ACI. Además de las [cuotas y los límites de Azure Container Instances](../container-instances/container-instances-quotas.md), los siguientes escenarios no se admiten aún con los nodos virtuales:

* Uso de entidad de servicio para extraer imágenes de ACR. La [solución alternativa](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) consiste en usar [secretos de Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitaciones de la red virtual](../container-instances/container-instances-vnet.md) entre las que se incluyen el emparejamiento de redes virtuales, las directivas de red de Kubernetes y el tráfico saliente a Internet con grupos de seguridad de red.
* Iniciar contenedores
* [Hospedaje de alias](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumentos](../container-instances/container-instances-exec.md#restrictions) para la ejecución en ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) no implementará pods en los nodos virtuales.
* Los nodos virtuales admiten la programación de pods de Linux. Puede instalar manualmente el proveedor de código abierto [ACI de Kubelet virtual](https://github.com/virtual-kubelet/azure-aci) para programar contenedores de Windows Server en ACI.
* Los nodos virtuales necesitan clústeres de AKS con redes de Azure CNI.
* Uso de intervalos IP autorizados de servidor de API para AKS.
* El montaje de volúmenes con recursos compartidos de Azure Files es compatible con las [cuentas de uso general V1](../storage/common/storage-account-overview.md#types-of-storage-accounts). Siga las instrucciones para montar [un volumen con un recurso compartido de Azure Files](azure-files-volume.md).
* El uso de IPv6 no se admite.

## <a name="next-steps"></a>Pasos siguientes

Configure nodos virtuales para los clústeres:

- [Creación de nodos virtuales mediante la CLI de Azure](virtual-nodes-cli.md)
- [Creación de nodos virtuales mediante el portal en Azure Kubernetes Service (AKS)](virtual-nodes-portal.md)

Los nodos virtuales suelen ser un componente de una solución de escalado en AKS. Para más información sobre soluciones de escalado, consulte los siguientes artículos:

- [Escalador horizontal automático de pods de Kubernetes][aks-hpa]
- [Escalador automático de clústeres de Kubernetes][aks-cluster-autoscaler]
- [Ejemplo de escalador automático para nodos virtuales][virtual-node-autoscale]
- [Más información sobre la biblioteca virtual de código abierto de Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
