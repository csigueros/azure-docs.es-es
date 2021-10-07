---
title: Configuración de un tipo de nodo secundario para grandes conjuntos de escalado de máquinas virtuales en un clúster administrado de Service Fabric
description: En este artículo se explica cómo configurar un tipo de nodo secundario como conjunto de escalado de máquinas virtuales de gran tamaño
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 05177b519caa504ac9d931b07b9f6126a3851c0b
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545103"
---
# <a name="service-fabric-managed-cluster-node-type-scaling"></a>Escalado de un tipo de nodo de clúster administrado de Service Fabric

Cada tipo de nodo de un clúster administrado de Service Fabric se encuentra respaldado por un conjunto de escalado de máquinas virtuales. Para permitir que los tipos de nodo de clúster administrado creen [conjuntos de escalado de máquinas virtuales de gran tamaño](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md), se ha agregado una propiedad `multiplePlacementGroups` a la definición de tipo de nodo. De forma predeterminada, los tipos de nodo de clúster administrado establecen esta propiedad en false para mantener la coherencia de los dominios de error y actualización dentro de un grupo de selección de ubicación, pero esta configuración limita el escalado de un tipo de nodo a 100 máquinas virtuales. Para decidir si la aplicación puede hacer un uso eficaz de los conjuntos de escalado grandes, consulte [esta lista de requisitos](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md#checklist-for-using-large-scale-sets).

Dado que el proveedor de recursos del clúster administrado de Azure Service Fabric orquesta el escalado y usa discos administrados para los datos, podemos admitir grandes conjuntos de escalado para tipos de nodos secundarios con estado y sin estado.

> [!NOTE]
> Esta propiedad no se puede modificar después de implementar un tipo de nodo.

## <a name="enable-large-virtual-machine-scale-sets-in-a-service-fabric-managed-cluster"></a>Habilitación de grandes conjuntos de escalado de máquinas virtuales en un clúster administrado de Service Fabric
Para configurar un tipo de nodo secundario como conjunto de escalado grande, establezca la propiedad **multiplePlacementGroups** en **true**.
> [!NOTE]
> Esta propiedad no se puede establecer en el tipo de nodo principal.

* El valor de apiVersion del recurso de clúster administrado de Service Fabric debe ser **2021-05-01** o posterior.

```json
{
  "apiVersion": "[variables('sfApiVersion')]",
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
  "location": "[resourcegroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
  ],
  "properties": {
    "multiplePlacementGroups": true,
    "isPrimary": false,
    "vmImagePublisher": "[parameters('vmImagePublisher')]",
    "vmImageOffer": "[parameters('vmImageOffer')]",
    "vmImageSku": "[parameters('vmImageSku')]",
    "vmImageVersion": "[parameters('vmImageVersion')]",
    "vmSize": "[parameters('nodeTypeSize')]",
    "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
    "dataDiskSizeGB": "[parameters('nodeTypeDataDiskSizeGB')]"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de una aplicación en un clúster administrado de Service Fabric](./tutorial-managed-cluster-deploy-app.md)
