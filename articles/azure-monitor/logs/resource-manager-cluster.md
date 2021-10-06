---
title: Ejemplos de plantillas de Resource Manager para clústeres de Log Analytics
description: Plantillas de ejemplo de Azure Resource Manager para implementar clústeres de Log Analytics.
ms.topic: sample
author: yossiy
ms.author: yossiy
ms.date: 09/12/2021
ms.openlocfilehash: 1e585db0e7e0cec05319c74419bb840200d1d86a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659237"
---
# <a name="resource-manager-template-samples-for-log-analytics-clusters-in-azure-monitor"></a>Ejemplos de plantillas de Resource Manager para clústeres de Log Analytics en Azure Monitor
Este artículo incluye [plantillas de Azure Resource Manager](../../azure-resource-manager/templates/syntax.md) de ejemplo para crear y configurar clústeres de Log Analytics en Azure Monitor. Cada ejemplo incluye un archivo de plantilla y un archivo de parámetros con valores de ejemplo para la plantilla.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>Referencias de plantilla

- [Clústeres de Microsoft.OperationalInsights](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/clusters) 

## <a name="create-a-log-analytics-cluster"></a>Creación de un clúster de Log Analytics
En el ejemplo siguiente, se crea un nuevo clúster de Log Analytics vacío.

### <a name="template-file"></a>Archivo de plantilla

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "CommitmentTier": {
      "type": "int",
      "allowedValues": [
        500,
        1000,
        2000,
        5000
      ],
      "defaultValue": 500,
      "metadata": {
        "description": "The Capacity Reservation value."
      }
  },
  "billingType": {
      "type": "string",
      "allowedValues": [
        "Cluster",
        "Workspaces"
      ],
      "defaultValue": "Cluster",
      "metadata": {
          "description": "The billing type settings. Can be 'Cluster' (default) or 'Workspaces' for proportional billing on workspaces."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "CapacityReservation",
        "capacity": "[parameters('CommitmentTier')]"
      },
      "properties":  {
        "billingType": "[parameters('billingType')]"
      }
    }
  ]
}
```

### <a name="parameter-file"></a>Archivo de parámetros

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "CommitmentTier": {
      "value": 500
    },
    "billingType": {
      "value": "Cluster"
    }
  }
}
```

## <a name="update-a-log-analytics-cluster"></a>Actualización de un clúster de Log Analytics
En el ejemplo siguiente, se actualiza un clúster de Log Analytics para usar la clave administrada por el cliente.

### <a name="template-file"></a>Archivo de plantilla

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "keyVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The key identifier URI."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The key name."
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "The key version. When empty, latest key version is used."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties":  {
        "keyVaultProperties": {
        "keyVaultUri": "https://key-vault-name.vault.azure.net",
        "keyName": "key-name",
        "keyVersion": "current-version"
        }
      }  
    }
  ]
}
```

### <a name="parameter-file"></a>Archivo de parámetros

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "keyVaultUri": {
      "value": "https://key-vault-name.vault.azure.net"
    },
    "keyName": {
      "value": "MyKeyName"
    },
    "keyVersion": {
      "value": ""
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Otras plantillas de ejemplo para Azure Monitor](../resource-manager-samples.md).
* [Más información sobre los clústeres dedicados de Log Analytics](./logs-dedicated-clusters.md).
* [Más información sobre los orígenes de datos de agente](../agents/agent-data-sources.md).
