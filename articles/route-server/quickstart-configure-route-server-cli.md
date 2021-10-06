---
title: 'Inicio rápido: Creación y configuración de una instancia de Route Server mediante la CLI de Azure'
description: En esta guía de inicio rápido, aprenderá a crear y configurar una instancia de Route Server mediante la CLI de Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: ca21e367752451f01c7ee8d1fc9596cfcb74029b
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094450"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Inicio rápido: Creación y configuración de una instancia de Route Server mediante la CLI de Azure 

Este artículo le ayuda a configurar Azure Route Server para su emparejamiento con una aplicación virtual de red (NVA) en su red virtual mediante Azure PowerShell. Route Server aprenderá las rutas de la NVA y las programará en las máquinas virtuales de la red virtual. Azure Route Server también anunciará las rutas de la red virtual a la NVA. Para más información, consulte [Azure Route Server](overview.md).

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Diagrama del entorno de implementación de Route Server mediante la CLI de Azure." border="false":::

> [!IMPORTANT]
> Si tiene un Azure Route Server creado antes del 1 de septiembre y no tiene una dirección IP pública asociada, deberá volver a crear el Route Server para que pueda obtener una dirección IP con fines de administración.

##  <a name="prerequisites"></a>Requisitos previos 

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Asegúrese de que tiene la versión de la CLI de Azure más reciente, o bien, puede usar Azure Cloud Shell en el portal. 
* Consulte [Límites de servicio de Azure Route Server](route-server-faq.md#limitations). 

##  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sesión en la cuenta de Azure y seleccione su suscripción. 

Para empezar la configuración, inicie sesión en la cuenta de Azure. Si usa la opción "Pruébelo" de CloudShell, iniciará sesión de manera automática. Use los siguientes ejemplos para conectarse:

```azurecli-interactive
az login
```

Compruebe las suscripciones para la cuenta.

```azurecli-interactive
az account list
```

Seleccione la suscripción para la que desea crear un circuito ExpressRoute.

```azurecli-interactive
az account set \
    --subscription "<subscription ID>"
```

## <a name="create-a-resource-group-and-a-virtual-network"></a>Creación de un grupo de recursos y una red virtual 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para poder crear una instancia de Azure Route Server, debe crear un grupo de recursos para hospedarla. Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En este ejemplo se crea un grupo de recursos denominado **myRouteServerRG** en la ubicación **westus**:

```azurecli-interactive
az group create \
    --name myRouteServerRG \
    --location westus
```

### <a name="create-a-virtual-network"></a>Creación de una red virtual

Cree la red virtual con el comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). En este ejemplo se crea una red virtual predeterminada denominada **myVirtualNetwork**. Si ya tiene una red virtual, puede ir directamente a la sección siguiente.

```azurecli-interactive
az network vnet create \
    --name myVirtualNetwork \
    --resource-group myRouteServerRG \
    --address-prefix 10.0.0.0/16 
``` 

### <a name="add-a-dedicated-subnet"></a>Adición de una subred dedicada 

Azure Route Server requiere una subred dedicada denominada *RouteServerSubnet*. El tamaño de subred debe ser de al menos /27 o un prefijo corto (como /26 o /25) o recibirá un mensaje de error al implementar Route Server. Cree una configuración de subred denominada **RouteServerSubnet** con [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create):

1. Ejecute el comando siguiente para agregar *RouteServerSubnet* a la red virtual.

    ```azurecli-interactive 
    az network vnet subnet create \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --address-prefix 10.0.0.0/24)
    ``` 

1. Tome nota del identificador de la subred RouteServerSubnet. Para obtener y almacenar el identificador de recurso de *RouteServerSubnet* en la variable `subnet_id`, use [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show):

    ```azurecli-interactive 
    subnet_id=$(az network vnet subnet show \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --query id -o tsv) 

    echo $subnet_id
    ```

## <a name="create-the-route-server"></a>Creación de la instancia de Route Server 

1. Para garantizar la conectividad con el servicio back-end que administra la configuración de Route Server, es necesario asignar una dirección IP pública. Cree una dirección IP pública estándar denominada **RouteServerIP** con [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create):

    ```azurecli-interactive
    az network public-ip create \
        --name RouteServerIP \
        --resource-group myRouteServerRG \
        --version IPv4 \
        --sku Standard
    ```

2. Cree la instancia de Azure Route Server con [az network routeserver create](/cli/azure/network/routeserver#az_network_routeserver_create). En este ejemplo se crea una instancia de Azure Route Server denominada **myRouteServer**. *hosted-subnet* es el identificador de recurso de la instancia de RouteServerSubnet creada en la sección anterior.

    ```azurecli-interactive
    az network routeserver create \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --hosted-subnet $subnet_id \
        --public-ip-address RouteServerIP
    ``` 

## <a name="create-bgp-peering-with-an-nva"></a>Creación del emparejamiento de BGP con una NVA 

Use [az network routeserver peering create](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_create) para establecer el emparejamiento de BGP entre la instancia de Route Server y la NVA: 

`peer-ip` es la dirección IP de la red virtual asignada a la NVA. `peer-asn` es el número de sistema autónomo (ASN) configurado en la NVA. El ASN puede ser cualquier número de 16 bits que no se encuentre en el intervalo del 65515 al 65520. Este intervalo de ASN está reservado por Microsoft.

```azurecli-interactive 
az network routeserver peering create \
    --name myNVA \
    --peer-ip 192.168.0.1 \
    --peer-asn 65501 \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
``` 

Para configurar el emparejamiento con una NVA diferente u otra instancia de la misma NVA con fines de redundancia, use el mismo comando que antes con valores diferentes de *PeerName*, *PeerIp* y *PeerAsn*.

## <a name="complete-the-configuration-on-the-nva"></a>Finalización de la configuración en la NVA 

Para completar la configuración en la NVA y habilitar las sesiones de BGP, necesita la dirección IP y el ASN de la instancia de Azure Route Server. Puede obtener esta información mediante [az network routeserver show](/cli/azure/network/routeserver#az_network_routeserver_show):

```azurecli-interactive 
az network routeserver show \
    --name myRouteServer \
    --resource-group myRouteServerRG 
``` 

El resultado tendrá un aspecto similar al siguiente:

``` 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Configuración del intercambio de rutas 

Si tiene una instancia de ExpressRoute y otra de Azure VPN Gateway en la misma red virtual y quiere que intercambien rutas, puede habilitar el intercambio de rutas en Azure Route Server.

> [!IMPORTANT]
> En el caso de las implementaciones green field, asegúrese de crear la instancia de Azure VPN Gateway antes de crear la instancia de Azure Route Server; en caso contrario, se producirá un error en la implementación de Azure VPN Gateway.
> 

1. Para habilitar el intercambio de rutas entre Azure Route Server y las puertas de enlace, use [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update) con la marca "--allow-b2b-traffic" establecida en **true**:

    ```azurecli-interactive 
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic true 
    ``` 

2. Para deshabilitar el intercambio de rutas entre Azure Route Server y las puertas de enlace, use [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update) con la marca "--allow-b2b-traffic" establecida en **false**:

    ```azurecli-interactive
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic false 
    ``` 

## <a name="troubleshooting"></a>Solución de problemas 

Use [az network routeserver peering list-advertised-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_advertised_routes) para ver las rutas anunciadas por Azure Route Server:

```azurecli-interactive 
az network routeserver peering list-advertised-routes \
    --name myNVA \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
```

Use [az network routeserver peering list-learned-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_learned_routes) para ver las rutas aprendidas por Azure Route Server:

```azurecli-interactive
az network routeserver peering list-learned-routes \
    --name myNVA \
    --routeserver myRouteServer
    --resource-group myRouteServerRG \
``` 

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita la instancia de Azure Route Server, use el primer comando para quitar el emparejamiento de BGP y, a continuación, el segundo para quitar la instancia de Route Server. 

1. Quite el emparejamiento de BGP entre Azure Route Server y una NVA con [az network routeserver peering delete](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_delete):

    ```azurecli-interactive
    az network routeserver peering delete \
        --name myNVA \
        --routeserver myRouteServer \
        --resource-group myRouteServerRG
    ``` 

2. Quite la instancia de Azure Route Server con [az network routeserver delete](/cli/azure/network/routeserver#az_network_routeserver_delete): 

    ```azurecli-interactive 
    az network routeserver delete \
        --name myRouteServer \
        --resource-group myRouteServerRG
    ``` 

## <a name="next-steps"></a>Pasos siguientes

Después de crear la instancia de Azure Route Server, continúe para obtener información sobre cómo interactúa Azure Route Server con las puertas de enlace de ExpressRoute y VPN Gateway: 

> [!div class="nextstepaction"]
> [Compatibilidad de Azure Route Server (versión preliminar) con ExpressRoute y VPN de Azure](expressroute-vpn-support.md)
 
