---
title: Administración de directivas de red de puntos de conexión privados
titleSuffix: Azure Private Link
description: Información sobre cómo deshabilitar las directivas de red de puntos de conexión privados
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 08/26/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6d4671823f5eb3f186007aea46983860269d916b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361753"
---
# <a name="manage-network-policies-for-private-endpoints"></a>Administración de directivas de red de puntos de conexión privados

Las directivas de red, como los grupos de seguridad de red (NSG), no eran compatibles con los puntos de conexión privados. Para implementar un punto de conexión privado en una determinada subred, se necesitaba un valor de desactivación explícito en dicha subred. Este valor solo se puede aplicar al punto de conexión privado. Para otros recursos de la subred, el acceso se controla en función de las reglas de seguridad del grupos de seguridad de red.

> [!IMPORTANT]
> La compatibilidad de NSG y UDR con puntos de conexión privados se encuentra en versión preliminar pública. Para obtener más información, vea [Versión preliminar pública de la compatibilidad UDR de Private Link](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/) y [Versión preliminar pública de la compatibilidad del grupo de seguridad de red de Private Link](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/).
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Al usar el portal para crear un punto de conexión privado, el valor `PrivateEndpointNetworkPolicies` se deshabilita automáticamente como parte del proceso de creación. La implementación mediante otros clientes requiere un paso adicional para cambiar este valor. 

El valor se puede deshabilitar y habilitar mediante los siguientes recursos:

* Cloud Shell, desde Azure Portal.
* Azure PowerShell
* Azure CLI
* Plantillas del Administrador de recursos de Azure
 
En los siguientes ejemplos se describe cómo deshabilitar y habilitar `PrivateEndpointNetworkPolicies` para una red virtual llamada **myVNet** con una subred **predeterminada** hospedada en un grupo de recursos llamado **myResourceGroup**.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="disable-network-policy"></a>Deshabilitar la directiva de red

En esta sección se describe cómo deshabilitar directivas de punto de conexión privado de subred mediante Azure PowerShell. Use [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) y [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) para deshabilitar la directiva.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```

### <a name="enable-network-policy"></a>Habilitar la directiva de red

En esta sección se describe cómo habilitar directivas de punto de conexión privado de subred mediante Azure PowerShell. Use [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) y [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) para habilitar la directiva.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Enabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI

### <a name="disable-network-policy"></a>Deshabilitar la directiva de red

En esta sección se describe cómo deshabilitar directivas de punto de conexión privado de subred mediante la CLI de Azure. Use [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) para deshabilitar la directiva.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```

### <a name="enable-network-policy"></a>Habilitar la directiva de red

En esta sección se describe cómo habilitar directivas de punto de conexión privado de subred mediante la CLI de Azure. Use [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) para habilitar la directiva.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies false \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>Plantilla de Resource Manager

### <a name="disable-network-policy"></a>Deshabilitar la directiva de red

En esta sección se describe cómo deshabilitar directivas de punto de conexión privado de subred mediante una plantilla de Azure Resource Manager.

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```

### <a name="enable-network-policy"></a>Habilitar la directiva de red

En esta sección se describe cómo habilitar directivas de punto de conexión privado de subred mediante una plantilla de Azure Resource Manager.

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Enabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Pasos siguientes
- Más información sobre el [punto de conexión privado de Azure](private-endpoint-overview.md)
 
