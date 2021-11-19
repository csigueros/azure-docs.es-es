---
title: Plantillas de ejemplo de Azure Resource Manager para experimentos de caos
description: Plantillas Azure Resource Manager ejemplo para crear experimentos en Azure Chaos Studio
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: dc0777380cf34465fc89a9e2e61d63aec0ca9e8c
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404320"
---
# <a name="resource-manager-template-samples-for-experiments-in-azure-chaos-studio"></a>Ejemplos de plantillas de Resource Manager para experimentos en Azure Chaos Studio
Este artículo incluye ejemplos de [plantillas de Azure Resource Manager](../azure-resource-manager/templates/syntax.md) para crear [experimentos de caos](chaos-studio-chaos-experiments.md) en Azure Chaos Studio. Cada ejemplo incluye un archivo de plantilla y un archivo de parámetros con valores de ejemplo para la plantilla.

## <a name="create-experiment-sample"></a>Creación de un experimento (ejemplo)

En este ejemplo, se crea un experimento de caos con un único recurso de destino y un único error de presión de CPU. Para modificar el experimento, haga referencia a nuestra [API REST](/rest/api/chaosstudio/experiments/create-or-update) y a la [biblioteca de errores](chaos-studio-fault-library.md).

### <a name="template-file"></a>Archivo de plantilla

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "experimentName": {
      "type": "string",
      "defaultValue": "simple-experiment",
      "metadata": {
        "description": "A name for the experiment."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "A region where the experiment will be deployed."
      }
    },
    "chaosTargetResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the chaos target. This is the resource ID of the resource being targeted plus the target proxy resource."
      }
    }
  },
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Chaos/experiments",
      "apiVersion": "2021-09-15-preview",
      "name": "parameters('experimentName')",
      "location": "parameters('location')",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "identity": {
          "properties": {
            "type": "SystemAssigned"
          }
        },
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "parameters('chaosTargetResourceId')"
              }
            ]
          }
        ],
        "startOnCreation": "false",
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ]
              }
            ]
          }
        ]
      }
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>Archivo de parámetros

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "experimentName": {
        "value": "my-first-experiment"
      },
      "location": {
        "value": "eastus"
      },
      "chaosTargetResourceId": {
        "value": "eastus"
      }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre Chaos Studio](chaos-studio-overview.md).
* [Más información sobre experimentos de caos](chaos-studio-chaos-experiments.md).
