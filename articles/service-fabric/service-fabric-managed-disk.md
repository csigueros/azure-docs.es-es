---
title: Implementación de discos de datos administrados de tipos de nodo de Service Fabric
description: Aprenda a crear e implementar tipos de nodos de Service Fabric con discos de datos administrados conectados.
author: craftyhouse
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: micraft
ms.openlocfilehash: 11660a3bb10e7004ce78d2788f93ac6fc2fc821d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090957"
---
# <a name="deploy-an-azure-service-fabric-cluster-node-type-with-managed-data-diskspreview"></a>Implementación de un tipo de nodo de clúster de Azure Service Fabric con discos de datos administrados (versión preliminar)

Los tipos de nodo de Service Fabric usan de forma predeterminada el disco temporal en cada máquina virtual del conjunto de escalado de máquinas virtuales subyacente para el almacenamiento de datos. Sin embargo, dado que el disco temporal no es persistente y el tamaño del disco temporal está enlazado a una SKU de máquina virtual determinada, esto puede ser demasiado restrictivo para algunos escenarios. Con Azure Managed Disks los clientes tienen un disco de datos persistente en el que pueden especificar el tamaño y el rendimiento que se usarán para un tipo de nodo, independientemente de una SKU de máquina virtual. En el documento siguiente se proporcionan los pasos para usar la compatibilidad nativa de Service Fabric para configurar y usar Azure Managed Disks como ruta de acceso de datos predeterminada. Service Fabric configurará automáticamente Azure Managed Disks en la creación de tipos de nodo y controlará las situaciones en las que se vuelve a crear la imagen inicial de las máquinas virtuales o del conjunto de escalado de máquinas virtuales.

* El tamaño mínimo de disco necesario para el disco de datos administrado es de 50 GB.
* En escenarios en los que hay más de un disco de datos administrado conectado, el cliente debe administrar los discos de datos por su cuenta.

## <a name="configuring-virtual-machine-scale-set-to-use-managed-data-disks-in-service-fabric"></a>Configuración del conjunto de escalado de máquinas virtuales para usar discos de datos administrados en Service Fabric
Para usar Azure Managed Disks en un tipo de nodo, configure el recurso del conjunto de escalado de máquinas virtuales subyacente con lo siguiente:

* Agregue un disco administrado en la sección de discos de datos de la plantilla para el conjunto de escalado de máquinas virtuales. 
* Actualice la extensión de Service Fabric con la siguiente configuración: 
    * Para Windows: **useManagedDataDisk: true** y **dataPath: "K:\\\\SvcFab"** .  Tenga en cuenta que la letra de unidad "K" es simplemente una representación, puede ser cualquier letra de unidad lexicográficamente mayor que toda la letra de unidad presente en la SKU del conjunto de escalado de máquinas virtuales.
    * Para Linux: **useManagedDataDisk:true** y **dataPath: "\mnt\sfdataroot"** .

>[!NOTE]
> Actualmente, la compatibilidad con discos de datos administrados para clústeres de Service Fabric de Linux no está disponible para producción, pero los clientes pueden probarlo mediante la extensión Test Service Fabric para Linux.

Plantilla de Azure Resource Manager para la extensión de Service Fabric
```json
{
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": false,
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
                            "dataPath": "K:\\\\SvcFab",
                            "useManagedDataDisk": true,
                            "durabilityLevel": "Bronze",
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            },
                            "systemLogUploadSettings": {
                                "Enabled": true
                            },
                        },
                        "typeHandlerVersion": "1.1"
                    }
                },
            ]
        },
        "storageProfile": 
        {
            "datadisks": [
                {
                    "lun": "1",
                    "createOption": "empty",
                    "diskSizeGB": "100",
                    "managedDisk": { "storageAccountType": "Standard_LRS" }
                }
            ]
        }
    }
}
```

## <a name="migrate-to-using-managed-data-disks-for-service-fabric-node-types"></a>Migración al uso de discos de datos administrados para tipos de nodo de Service Fabric
* En todos los escenarios de migración, es necesario agregar un nuevo tipo de nodo que use discos de datos administrados, tal como se especificó anteriormente.
* Una vez agregados los nuevos tipos de nodo, migre las cargas de trabajo a los nuevos tipos de nodo.
* Una vez haya terminado la implementación de los recursos, puede empezar a deshabilitar los nodos en el tipo de nodo que quiera quitar del clúster original.

## <a name="next-steps"></a>Pasos siguientes 
* [Introducción a Service Fabric](service-fabric-reliable-services-introduction.md)
* [Tipos de nodo y conjuntos de escalado de máquinas virtuales](service-fabric-cluster-nodetypes.md)
* [Planeamiento de capacidad de Service Fabric](service-fabric-best-practices-capacity-scaling.md)
