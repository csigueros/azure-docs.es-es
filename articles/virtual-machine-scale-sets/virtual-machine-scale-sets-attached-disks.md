---
title: Discos de datos conectados a Azure Virtual Machine Scale Sets
description: Aprenda a usar discos de datos conectados a conjuntos de escalado de máquinas virtuales mediante esquemas de casos de uso específicos.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 13bdbe7a1f6c328a2797d6f8869c05af9c5cb7f5
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690560"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Conjuntos de escalado de máquinas virtuales de Azure y discos de datos conectados

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado uniformes

Para expandir el almacenamiento disponible, los [conjuntos de escalado de máquinas virtuales](./index.yml) de Azure admiten instancias de máquina virtual con discos de datos asociados. Puede asociar discos de datos cuando se crea el conjunto de escalado, o a un conjunto de escalado existente.

> [!NOTE]
> Cuando se crea un conjunto de escalado con discos de datos asociados, debe montar y dar formato a los discos desde una máquina virtual para usarlos (al igual que sucede con las máquinas virtuales de Azure independientes). Una manera práctica de llevar cabo este proceso es usar una extensión de script personalizado que llama a un script para crear una partición y dar formato a todos los discos de datos en una máquina virtual. Para ver ejemplos, consulte la [CLI de Azure y ](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Creación y administración de discos de un conjunto de escalado
Para más información sobre cómo crear un conjunto de escalado con discos de datos asociados, prepararlos y aplicarles formato, o agregarlos y quitarlos, consulte uno de los siguientes tutoriales:

- [CLI de Azure](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

En el resto de este artículo se describen casos de uso específicos, como clústeres de Service Fabric que requieren discos de datos, o la asociación de discos de datos existentes con contenido a un conjunto de escalado.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Creación de un clúster de Service Fabric con discos de datos conectados
Cada [tipo de nodo](../service-fabric/service-fabric-cluster-nodetypes.md) de un clúster de [Service Fabric](../service-fabric/index.yml) en ejecución en Azure se encuentra respaldado por un conjunto de escalado de máquinas virtuales. Mediante una plantilla de Azure Resource Manager, puede asociar discos de datos a los conjuntos de escalado que componen el clúster de Service Fabric. Puede usar una [plantilla existente](https://github.com/Azure-Samples/service-fabric-cluster-templates) como punto de partida. En la plantilla, incluya una sección _dataDisks_ en el atributo _storageProfile_ de los recursos _Microsoft.Compute/virtualMachineScaleSets_ e implemente la plantilla. En el ejemplo siguiente se conecta un disco de datos de 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Puede crear particiones, formatear y montar los discos de datos automáticamente al implementar el clúster. Añada una extensión de script personalizada al atributo _extensionProfile_ de _virtualMachineProfile_ de los conjuntos de escalado.

Para preparar automáticamente los discos de datos en un clúster de Windows, añada lo siguiente:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Para preparar automáticamente los discos de datos en un clúster de Linux, añada lo siguiente:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Adición de discos de datos previamente rellenados a un conjunto de escalado existente
Los discos de datos especificados en el modelo de conjunto de escalado siempre están vacíos. Sin embargo, puede asociar un disco de datos existente a una máquina virtual determinada de un conjunto de escalado. Si quiere propagar los datos entre todas las máquinas virtuales del conjunto de escalado, puede duplicar el disco de datos y asociarlo a cada una de ellas; también puede crear una imagen personalizada que contenga los datos y aprovisionar el conjunto de escalado a partir de esta, o usar Azure Files o una oferta de almacenamiento de datos similar.


## <a name="additional-notes"></a>Notas adicionales
Hay compatibilidad disponible con Azure Managed Disks y con discos de datos asociados al conjunto de escalado en la versión de API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) o posterior de Microsoft.Compute API.

La compatibilidad de Azure Portal con discos de datos conectados en los conjuntos de escalado es limitada. Dependiendo de los requisitos, puede usar plantillas de Azure, CLI, PowerShell, SDK y API de REST para administrar los discos conectados.
