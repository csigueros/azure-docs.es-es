---
title: Habilitación de la integración con la red virtual de Azure
description: Este artículo de procedimientos lo guiará en el proceso de habilitación de la integración con la red virtual en una aplicación web de App Service.
keywords: Integración con red virtual
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 549c92b932e0d589aec2490512fc18713fa71546
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269920"
---
# <a name="enable-virtual-network-integration-in-azure-app-service"></a>Habilitación de la integración con red virtual en Azure App Service

Mediante la integración con una red virtual (VNet) de Azure desde la [aplicación de App Service](./overview.md), puede acceder a recursos privados desde la aplicación dentro de la red virtual. Esta característica tiene dos variantes:

* Integración con red virtual regional: conéctese a redes virtuales de Azure en la misma región. Debe tener una subred dedicada en la red virtual con la que se va a integrar.
* Integración con red virtual con requisito de puerta de enlace: cuando se conecta directamente a una red virtual en otras regiones o a una red virtual clásica en la misma región, debe usar la integración con red virtual con requisito de puerta de enlace.

En este artículo de procedimientos se describe cómo configurar la integración con red virtual regional.

## <a name="prerequisites"></a>Prerrequisitos

La integración con red virtual necesita:
- Un plan de tarifa de App Service que [admita la integración con VNet](./overview-vnet-integration.md).
- Una red virtual con una subred vacía en la misma región.

La subred se debe delegar a Microsoft.Web/serverFarms. Si la delegación no se realiza antes de la integración, el proceso de aprovisionamiento configurará esta delegación. Se debe asignar un bloque IPv4 `/28` (16 direcciones) a la subred. En realidad, se recomienda tener un mínimo de 64 direcciones (bloque IPv4 `/26`) para permitir la escala horizontal máxima.

## <a name="configure-in-the-azure-portal"></a>Configuración en Azure Portal

1. Vaya a la interfaz de usuario de **Redes** en el portal de App Service. En **Tráfico saliente**, seleccione **Integración con red virtual**.

1. Seleccione **Agregar VNET**.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-app.png" alt-text="Selección de Integración con red virtual":::

1. La lista desplegable contiene todas las redes virtuales de la suscripción en la misma región.

    :::image type="content" source="./media/configure-vnet-integration-enable/vnetint-add-vnet.png" alt-text="Seleccione la red virtual.":::

    * Seleccione una subred existente vacía o cree una nueva.

Durante la integración, la aplicación se reinicia. Una vez finalizada la integración, verá los detalles de la VNet con la que está integrada.

## <a name="configure-with-azure-cli"></a>Configuración con la CLI de Azure

También puede utilizar la CLI de Azure para configurar la integración con red virtual:

```azurecli-interactive
az webapp vnet-integration add --resource-group <group-name> --name <app-name> --vnet <vnet-name> --subnet <subnet-name>
```

> [!NOTE]
> El comando comprobará si la subred se delega a Microsoft.Web/serverFarms y aplicará la delegación necesaria si no está configurada. Si ya se configuró y no tiene permisos para comprobarlo o si la red virtual se encuentra en otra suscripción, puede utilizar el parámetro `--skip-delegation-check` para omitir la validación.

## <a name="configure-with-azure-powershell"></a>Configuración con Azure PowerShell

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'
$vNetName = '<vnet-name>'
$integrationSubnetName = '<subnet-name>'
$subscriptionId = '<subscription-guid>'

# Configure VNet Integration
$subnetResourceId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Network/virtualNetworks/$vNetName/subnets/$integrationSubnetName"
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
$webApp.Properties.virtualNetworkSubnetId = $subnetResourceId
$webApp | Set-AzResource -Force
```

## <a name="next-steps"></a>Pasos siguientes

- [Configuración del enrutamiento de la integración con red virtual](./configure-vnet-integration-routing.md)
- [Introducción general a las redes](./networking-features.md)