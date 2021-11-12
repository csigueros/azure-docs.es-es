---
title: Configuración del clúster administrado de Service Fabric
description: Aprenda a configurar el clúster administrado de Service Fabric para las actualizaciones automáticas del sistema operativo, las reglas de grupo de seguridad de red y mucho más.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aad982def0a1dd2e4b6f847a72e270337abcd460
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286039"
---
# <a name="service-fabric-managed-cluster-configuration-options"></a>Opciones de configuración del clúster administrado de Service Fabric

Además de seleccionar el [SKU de clúster administrado de Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus) al crear el clúster, hay otras formas de configurarlo, entre las que se incluyen:

* Adición de una [extensión del conjunto de escalado de máquinas virtuales](how-to-managed-cluster-vmss-extension.md) a un tipo de nodo
* Configuración de la [expansión de la zona de disponibilidad](how-to-managed-cluster-availability-zones.md) de un clúster
* Configuración de las [opciones de red](how-to-managed-cluster-networking.md) del clúster
* Configuración de un tipo de nodo para [grandes conjuntos de escalado de máquinas virtuales](how-to-managed-cluster-large-virtual-machine-scale-sets.md)
* Configuración de la [identidad administrada](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) en los tipos de nodo de clúster
* Habilitación del [cifrado de discos de datos y sistema operativo](how-to-managed-cluster-enable-disk-encryption.md) en los nodos de clúster
* Configuración del [escalado automático](how-to-managed-cluster-autoscale.md) en un tipo de nodo secundario
* [Escalado manual de un tipo de nodo](how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-portal)
* Habilitación de las [actualizaciones automáticas de las imágenes del sistema operativo](how-to-managed-cluster-modify-node-type.md#enable-automatic-os-image-upgrades) en los tipos de nodo de clúster
* Modificación de la [imagen del sistema operativo](how-to-managed-cluster-modify-node-type.md#modify-the-os-image-for-a-node-type-with-portal) usada para un tipo de nodo
* Configuración de las [propiedades de selección de ubicación](how-to-managed-cluster-modify-node-type.md#configure-placement-properties-for-a-node-type-with-portal) para un tipo de nodo
* Selección del SKU de [tipo de disco administrado](how-to-managed-cluster-managed-disk.md) del clúster
* Configuración de las [opciones de actualización](how-to-managed-cluster-upgrades.md) del clúster para las actualizaciones del runtime


## <a name="next-steps"></a>Pasos siguientes

[Información general de los clústeres administrados de Service Fabric](overview-managed-cluster.md)

[Plantillas de clúster de Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)
