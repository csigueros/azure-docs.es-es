---
title: Incorporación de una extensión de conjunto de escalado de máquinas virtuales a un tipo de nodo de clúster administrado de Service Fabric
description: Aquí le mostramos cómo agregar una extensión de conjunto de escalado de máquinas virtuales a un tipo de nodo de clúster administrado de Service Fabric
ms.topic: article
ms.date: 8/02/2021
ms.openlocfilehash: 3b0f15f2f83ddad0b8251642fc63db79581a5a97
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547064"
---
# <a name="virtual-machine-scale-set-extension-support-on-service-fabric-managed-cluster-node-types"></a>Compatibilidad de extensiones de conjunto de escalado de máquinas virtuales con tipos de nodo de clúster administrado de Service Fabric

Cada tipo de nodo de un clúster administrado de Service Fabric se encuentra respaldado por un conjunto de escalado de máquinas virtuales. De este modo, puede agregar [extensiones de conjunto de escalado de máquinas virtuales](../virtual-machines/extensions/overview.md) a los tipos de nodo de clúster administrado de Service Fabric. Las extensiones son aplicaciones pequeñas que proporcionan automatización y configuración posterior a la implementación en VM de Azure. La plataforma de Azure hospeda numerosas extensiones, que abarcan aplicaciones de configuración, supervisión, seguridad y utilidad de VM. Los editores toman una aplicación, la encapsulan en una extensión y simplifican la instalación. Solo debe proporcionar parámetros obligatorios. 

## <a name="add-a-virtual-machine-scale-set-extension"></a>Incorporación de una extensión de conjunto de escalado de máquinas virtuales
Puede agregar una extensión de conjunto de escalado de máquinas virtuales a un tipo de nodo de clúster administrado de Service Fabric mediante el comando de PowerShell [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension).

También puede agregar una extensión de conjunto de escalado de máquinas virtuales en un tipo de nodo de clúster administrado de Service Fabric en su plantilla de Azure Resource Manager, por ejemplo:

```json
{
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "apiVersion": "[variables('sfApiVersion')]",
  "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
  ],
  "location": "[resourceGroup().location]",
  "properties": {
    "isPrimary": true,
    "vmInstanceCount": 3,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2",
    "vmImagePublisher": "MicrosoftWindowsServer",
    "vmImageOffer": "WindowsServer",
    "vmImageSku": "2019-Datacenter",
    "vmImageVersion": "latest",
    "vmExtensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "type": "KeyVaultForWindows",
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "settings": {
          }
        }
      }
    ]
  }
}
```

Para obtener más información sobre la configuración de los tipos de nodo de clúster administrados por Service Fabric, consulte [Tipo de nodo de clúster administrado](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de los clústeres administrados de Service Fabric, consulte:

> [!div class="nextstepaction"]
> [Preguntas más frecuentes sobre los clústeres administrados de Service Fabric](./faq-managed-cluster.yml)
