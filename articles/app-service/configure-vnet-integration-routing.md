---
title: Configuración de integración con red virtual con el enrutamiento de aplicaciones
description: En este artículo de procedimientos se explica cómo configurar el enrutamiento de aplicaciones en una integración de red virtual regional.
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: b62a8c4bcef5e5556820f6f4e816f01566d44f72
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892047"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Administración del enrutamiento de integración con red virtual de Azure App Service

Al configurar el enrutamiento de aplicaciones, puede enrutar todo el tráfico o solo el tráfico privado (también conocido como tráfico [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)) a la red virtual de Azure. En este artículo se describe cómo configurar el enrutamiento de aplicaciones.

## <a name="prerequisites"></a>Requisitos previos

La aplicación ya está integrada mediante la característica de integración con red virtual regional.

## <a name="configure-in-the-azure-portal"></a>Configuración en Azure Portal

Siga estos pasos para deshabilitar la opción **Enrutar todo** en la aplicación a través del portal.

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="Captura de pantalla que muestra Enrutar todo habilitado":::

1. Vaya a **Redes** > **Integración con red virtual** en el portal de aplicaciones.
1. Establezca **Enrutar todo** en **Deshabilitado**.
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="Captura de pantalla que muestra Enrutar todo deshabilitado":::

1. Seleccione **Sí** para confirmar la acción.

## <a name="configure-with-the-azure-cli"></a>Configuración con la CLI de Azure

También puede configurar **Enrutar todo** con la CLI de Azure. La versión mínima de az necesaria es la 2.27.0.

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

- [Habilitación de la integración de red virtual](./configure-vnet-integration-enable.md)
- [Introducción general a las redes](./networking-features.md)