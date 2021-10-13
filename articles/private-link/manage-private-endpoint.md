---
title: Administración de una conexión de punto de conexión privado en Azure
titleSuffix: Azure Private Link
description: Aprenda a administrar conexiones de punto de conexión privado en Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 10/04/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7923e35165f756878ed52e807a1e0106e1660e59
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535955"
---
# <a name="manage-a-private-endpoint-connection"></a>Administrar una conexión de punto de conexión privado

Azure Private Link trabaja en un modelo de aprobación en el que el consumidor del servicio Private Link puede solicitar una conexión al proveedor de servicios para consumir el servicio. 

A partir de ese momento, el proveedor de servicios puede decidir si va a permitir al consumidor conectarse o no. Azure Private Link permite a los proveedores de servicios administrar la conexión de punto de conexión privado en sus recursos. 

En este artículo se proporcionan instrucciones sobre cómo administrar las conexiones de punto de conexión privado.

![Administración de puntos de conexión privados](media/manage-private-endpoint/manage-private-endpoint.png)

Hay dos métodos de aprobación de conexión entre los que puede elegir un consumidor del servicio Private Link:

- **Automático**: si el consumidor del servicio tiene permisos de control de acceso basado en rol de Azure en el recurso del proveedor de servicios, el consumidor puede elegir el método de aprobación automático. Cuando el recurso del proveedor de servicios recibe la solicitud, el proveedor de servicios no tiene que hacer nada y la conexión se aprueba automáticamente. 

- **Manual**: si el consumidor del servicio no tiene permisos de control de acceso basado en rol de Azure en el recurso del proveedor de servicios, el consumidor puede elegir el método de aprobación manual. La solicitud de conexión aparece en los recursos del servicio como **Pendiente**. El proveedor de servicios debe aprobar manualmente la solicitud antes de que puedan establecerse las conexiones. En los casos manuales, el consumidor del servicio también puede especificar un mensaje con la solicitud para proporcionar más contexto al proveedor de servicios. El proveedor de servicios tiene las siguientes opciones entre las que elegir para todas las conexiones de punto de conexión privado: **Aprobar**, **Rechazar**, **Quitar**.

En la siguiente tabla se muestran las distintas acciones del proveedor de servicios y los estados de conexión resultantes para los puntos de conexión privados. El proveedor de servicios puede cambiar el estado de la conexión más adelante sin intervención del consumidor. La acción actualizará el estado del punto de conexión en el lado del consumidor. 


| Acción del proveedor de servicios  | Estado de punto de conexión privado del consumidor del servicio | Descripción |
|---------|---------|---------|
| None    |    Pending     |    La conexión se crea manualmente y está pendiente de aprobación por parte del propietario del recurso de Private Link.       |
| Aprobación    |  Aprobado       |  La conexión se aprobó de forma automática o manual y está lista para usarse.     |
| Reject     | Rechazada        | El propietario del recurso de vínculo privado rechazó la conexión.        |
| Remove    |  Escenario desconectado       | El propietario del recurso de Private Link ha quitado la conexión, el punto de conexión privado se vuelve informativo y debe eliminarse para la limpieza.        |

## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Administración de conexiones de punto de conexión privado en recursos de PaaS de Azure

Azure Portal es el método preferido para administrar conexiones de punto de conexión privado en recursos de PaaS de Azure. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda de la parte superior del portal, escriba **Private Link**. En los resultados de la búsqueda, seleccione **Private Link**.

3. En **Private Link Center**, seleccione **Puntos de conexión privados** o **Servicios de Private Link**.

4. Puede ver el número de conexiones de punto de conexión privado asociadas a cada uno de los puntos de conexión. Puede filtrar los recursos según sea necesario.

5. Seleccione el punto de conexión privado.  En las conexiones mostradas, seleccione la conexión que desea administrar. 

6. Puede cambiar el estado de la conexión seleccionando entre las opciones de la parte superior.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Administración de conexiones de punto de conexión privado en un servicio Private Link propiedad del cliente o asociado

Azure PowerShell y la CLI de Azure son los métodos preferidos para administrar conexiones de punto de conexión privado en servicios de asociados de Microsoft o servicios propiedad del cliente. 
 
### <a name="powershell"></a>PowerShell 
  
Use los siguientes comandos de PowerShell para administrar conexiones de punto de conexión privado.  

#### <a name="get-private-link-connection-states"></a>Obtención de estados de conexión de Private Link 

Use [Get-AzPrivateEndpointConnection](/powershell/module/az.network/get-azprivateendpointconnection) para obtener las conexiones de punto de conexión privado y sus estados.  

```azurepowershell
Get-AzPrivateEndpointConnection -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
```
 
#### <a name="approve-a-private-endpoint-connection"></a>Aprobación de una conexión de punto de conexión privado 
 
Use el cmdlet [Approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) para aprobar una conexión de punto de conexión privado. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Denegación de la conexión de punto de conexión privado 
 
Use el cmdlet [Deny-AzPrivateEndpointConnection](/powershell/module/az.network/deny-azprivateendpointconnection) para rechazar una conexión de punto de conexión privado. 

```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```

#### <a name="remove-private-endpoint-connection"></a>Eliminación de una conexión de punto de conexión privado 
 
Use el cmdlet [Remove-AzPrivateEndpointConnection](/powershell/module/az.network/remove-azprivateendpointconnection) para quitar una conexión de punto de conexión privado. 

```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
### <a name="azure-cli"></a>Azure CLI 
 
#### <a name="get-private-link-connection-states"></a>Obtención de estados de conexión de Private Link 

Use [az network private-endpoint-connection show](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_show) para obtener las conexiones de punto de conexión privado y sus estados.  

```azurecli
  az network private-endpoint-connection show \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```
 
#### <a name="approve-a-private-endpoint-connection"></a>Aprobación de una conexión de punto de conexión privado 
 
Use el cmdlet [az network private-endpoint-connection approve](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_approve) para aprobar una conexión de punto de conexión privado. 
 
```azurecli
  az network private-endpoint-connection approve \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```
 
#### <a name="deny-private-endpoint-connection"></a>Denegación de la conexión de punto de conexión privado 
 
Use el cmdlet [az network private-endpoint-connection reject](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_reject) para rechazar una conexión de punto de conexión privado. 

```azurecli
  az network private-endpoint-connection reject \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```

#### <a name="remove-private-endpoint-connection"></a>Eliminación de una conexión de punto de conexión privado 
 
Use el cmdlet [az network private-endpoint-connection delete](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_delete) para eliminar una conexión de punto de conexión privado. 

```azurecli
  az network private-endpoint-connection delete \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes
- [Más información sobre los puntos de conexión privados](private-endpoint-overview.md)
 
