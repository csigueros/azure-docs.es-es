---
title: Tipos de recursos y asignaciones de roles admitidos para Chaos Studio
description: Comprenda la lista de tipos de recursos admitidos y qué asignación de roles es necesaria para permitir que un experimento ejecute un error en ese tipo de recurso.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10bb9b826d5c749b283dd38caa77ae03b7695b8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091689"
---
# <a name="supported-resource-types-and-role-assignments-for-chaos-studio"></a>Tipos de recursos y asignaciones de roles admitidos para Chaos Studio

A continuación se enumeran los tipos de recursos admitidos para errores, los tipos de destino y los roles sugeridos que se usarán al conceder un permiso de experimento a un recurso de ese tipo.

| Tipo de recurso | Nombre de destino | Asignación de roles sugerida |
| - | - | - |
| Microsoft.Compute/virtualMachines (basada en agente) | Microsoft-Agent | Lector |
| Microsoft.Compute/virtualMachineScaleSets (basada en agente) | Microsoft-Agent | Lector |
| Microsoft.Compute/virtualMachines (directo de servicio) | Microsoft-VirtualMachine | Colaborador de la máquina virtual |
| Microsoft.Compute/virtualMachineScaleSets (directo de servicio) | Microsoft-VirtualMachineScaleSet | Colaborador de la máquina virtual |
| Microsoft.DocumentDb/databaseAccounts (CosmosDB, directo de servicio) | Microsoft-CosmosDB | Operador de Cosmos DB |
| Microsoft.ContainerService/managedClusters (directo de servicio) | Microsoft-AzureKubernetesServiceChaosMesh | Rol de usuario de clúster de Azure Kubernetes Service |
| Microsoft.Network/networkSecurityGroups (directo de servicio) | Microsoft-NetworkSecurityGroup | Colaborador de la red |
| Microsoft.Cache/Redis (directo de servicio) | Microsoft-AzureCacheForRedis | Colaborador de la memoria caché de Redis |
