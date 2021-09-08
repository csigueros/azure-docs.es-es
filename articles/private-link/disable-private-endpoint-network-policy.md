---
title: Deshabilitar las directivas de red de puntos de conexión privados
titleSuffix: Azure Private Link
description: Obtenga información sobre cómo deshabilitar las directivas de red de puntos de conexión privados.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 07/14/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 41cdefd340ace93d5a068c9a74543965834d01ca
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221360"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Deshabilitar las directivas de red de puntos de conexión privados

Las directivas de red como, por ejemplo, los grupos de seguridad de red no se admiten para los puntos de conexión privados. Para implementar un punto de conexión privado en una subred dada, es necesario un valor de deshabilitación explícito en dicha subred. Este valor solo se puede aplicar al punto de conexión privado. Para otros recursos de la subred, el acceso se controla en función de las reglas de seguridad del grupos de seguridad de red.
 
Al usar el portal para crear un punto de conexión privado, el valor `PrivateEndpointNetworkPolicies` se deshabilita automáticamente como parte del proceso de creación. La implementación mediante otros clientes requiere un paso adicional para cambiar este valor. 

El valor se puede deshabilitar mediante:

* Cloud Shell, desde Azure Portal.
* Azure PowerShell
* Azure CLI
* Plantillas del Administrador de recursos de Azure
 
En los siguientes ejemplos se describe cómo deshabilitar `PrivateEndpointNetworkPolicies` para una red virtual llamada **myVNet** con una subred **predeterminada** hospedada en un grupo de recursos llamado **myResourceGroup**.

## <a name="azure-powershell"></a>Azure PowerShell

En esta sección se describe cómo deshabilitar directivas de punto de conexión privado de subred mediante Azure PowerShell. Use [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) y [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) para deshabilitar la directiva.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI
En esta sección se describe cómo deshabilitar directivas de punto de conexión privado de subred mediante la CLI de Azure. Use [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) para deshabilitar la directiva.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>Plantilla de Resource Manager
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
## <a name="next-steps"></a>Pasos siguientes
- Más información sobre el [punto de conexión privado de Azure](private-endpoint-overview.md)
 
