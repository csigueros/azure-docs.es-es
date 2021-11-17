---
title: Configuración de integración con red virtual con el enrutamiento de aplicaciones
description: Este artículo de procedimientos lo guiará en el proceso de configuración de un enrutamiento de aplicaciones en una integración con red virtual regional.
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: d286b8c0bb82c1501cdc77e8097ba6b7d5b2008f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269849"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Administración del enrutamiento de integración con red virtual de Azure App Service

Al configurar el enrutamiento de aplicaciones, puede enrutar todo el tráfico o solo el tráfico privado (también conocido como tráfico [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)) a la red virtual (VNet) de Azure. En este artículo de procedimientos se describe cómo configurar el enrutamiento de aplicaciones.

## <a name="prerequisites"></a>Prerrequisitos

La aplicación ya está integrada mediante la característica de integración con red virtual regional.

## <a name="configure-in-the-azure-portal"></a>Configuración en Azure Portal

Puede usar los pasos siguientes para deshabilitar la opción Enrutar todo en la aplicación a través del portal: 

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="Opción Enrutar todo habilitada":::

1. Vaya a la interfaz de usuario **Redes** > **Integración con red virtual** en el portal de la aplicación.
1. Establezca **Enrutar todo** en deshabilitado.
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="Deshabilitar Enrutar todo":::

1. Seleccione **Sí** para confirmar la acción.

## <a name="configure-with-azure-cli"></a>Configuración con la CLI de Azure

También puede configurar Enrutar todo con la CLI de Azure (la `az version` mínima requerida es 2.27.0):

```azurecli-interactive
az webapp config set --resource-group <group-name> --name <app-name> --vnet-route-all-enabled [true|false]
```

## <a name="configure-with-azure-powershell"></a>Configuración con Azure PowerShell

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'

# Configure VNet Integration
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
Set-AzResource -ResourceId ($webApp.Id + "/config/web") -Properties @{ vnetRouteAllEnabled = $true } -Force
```

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación de la integración con red virtual](./configure-vnet-integration-enable.md)
- [Introducción general a las redes](./networking-features.md)