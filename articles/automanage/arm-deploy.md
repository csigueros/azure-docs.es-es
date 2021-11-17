---
title: Incorporación de una máquina a Azure Automanage con una plantilla de ARM
description: Aprenda a incorporar una máquina a Azure Automanage con una plantilla de Azure Resource Manager.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: cdb10c265ddae4aaf83450c1951ef6cb5c2219df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443858"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Incorporación de una máquina a Automanage con una plantilla de Azure Resource Manager (ARM)


## <a name="overview"></a>Información general
Siga los pasos que se indican a continuación para incorporar una máquina a los procedimientos recomendados de Automanage con una plantilla de ARM.

## <a name="prerequisites"></a>Prerrequisitos
* Debe tener los [permisos de RBAC](./automanage-virtual-machines.md#required-rbac-permissions) necesarios.
* Debe encontrarse en una región y una imagen de VM compatibles, destacadas en estos [requisitos previos](./automanage-virtual-machines.md#prerequisites).


## <a name="arm-template-overview"></a>Introducción a la plantilla de ARM
La plantilla de ARM siguiente incorporará la máquina especificada a los procedimientos recomendados de Azure Automanage. Los detalles sobre la plantilla de ARM y los pasos sobre cómo implementarla se encuentran en la sección de implementación de la plantilla de ARM que aparece [a continuación](#arm-template-deployment).
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "configurationProfile": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2021-04-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfile')]",
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>Implementación de la plantilla de ARM
La plantilla de ARM anterior creará una asignación de perfil de configuración para la máquina especificada. 

El valor `configurationProfile` puede ser uno de los siguientes:
* "/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesProduction"
* "/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesDevTest"

Siga estos pasos para implementar la plantilla de ARM:
1. Guarde la plantilla de ARM anterior como `azuredeploy.json`.
1. Ejecute la implementación de la plantilla de ARM con `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`.
1. Proporcione los valores para machineName, automanageAccountName y configurationProfileAssignment cuando se le solicite.
1. Ya está todo listo.

Al igual que con cualquier plantilla de ARM, se pueden simplificar los parámetros en un archivo `azuredeploy.parameters.json` independiente y usarlo como argumento al implementar.

## <a name="next-steps"></a>Pasos siguientes
Obtener más información acerca de Automanage para [Linux](./automanage-linux.md) y [Windows](./automanage-windows-server.md)