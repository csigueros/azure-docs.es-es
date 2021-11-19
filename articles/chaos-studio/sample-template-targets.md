---
title: Ejemplos de plantillas de Azure Resource Manager para destinos y funcionalidades en Azure Chaos Studio
description: Plantillas de Azure Resource Manager de ejemplo para incorporar recursos a Azure Chaos Studio mediante destinos y funcionalidades
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: 6e5c6b0610d2a2d48523a5d2a7a95c28cd8bae59
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404311"
---
# <a name="resource-manager-template-samples-for-targets-and-capabilities-in-azure-chaos-studio"></a>Ejemplos de plantillas de Resource Manager para destinos y funcionalidades en Azure Chaos Studio
En este artículo se incluyen [plantillas de Azure Resource Manager de ejemplo](../azure-resource-manager/templates/syntax.md) para crear [destinos y funcionalidades](chaos-studio-targets-capabilities.md), con el fin de incorporar recursos a Azure Chaos Studio. Cada ejemplo incluye un archivo de plantilla y un archivo de parámetros con valores de ejemplo para la plantilla.

## <a name="onboard-service-direct-target-and-capabilities-single-capability"></a>Incorporación de destino y funcionalidades y directos del servicio (funcionalidad única)

En este ejemplo, se incorpora una instancia de Azure Cosmos DB mediante [destinos y funcionalidades](chaos-studio-targets-capabilities.md). La plantilla se puede modificar para todas las funcionalidades y destino directos del servicio haciendo referencia a la [biblioteca de errores](chaos-studio-fault-library.md).

### <a name="template-file"></a>Archivo de plantilla

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource being enabled."
      }
    },
    "resourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group where the resource being enabled is located."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/providers/targets",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-CosmosDB')]",
      "location": "[parameters('location')]",
      "properties": {}
    },
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-CosmosDB/Failover-1.0')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.DocumentDB/databaseAccounts', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-CosmosDB')]"
      ],
      "properties": {}
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
      "resourceName": {
        "value": "my-cosmos-db"
      },
      "resourceGroup": {
        "value": "my-rg"
      }
  }
}
```

## <a name="onboard-service-direct-target-and-capabilities-multiple-capabilities"></a>Incorporación de destino y funcionalidades directos del servicio (varias funcionalidades)

En este ejemplo, se incorpora un clúster de Azure Kubernetes Service mediante [destinos y funcionalidades](chaos-studio-targets-capabilities.md).

### <a name="template-file"></a>Archivo de plantilla

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource being enabled."
      }
    },
    "resourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group where the resource being enabled is located."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh')]",
      "location": "[parameters('location')]",
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/NetworkChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/PodChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/StressChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/IOChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/TimeChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/KernelChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/DNSChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/HTTPChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
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
      "resourceName": {
        "value": "my-aks-cluster"
      },
      "resourceGroup": {
        "value": "my-rg"
      }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre Chaos Studio](chaos-studio-overview.md).
* [Más información sobre destinos y funcionalidades](chaos-studio-targets-capabilities.md).
