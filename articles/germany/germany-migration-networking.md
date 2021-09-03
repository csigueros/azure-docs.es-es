---
title: Migración de recursos de red de Azure de Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos de red de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1e8cb8dd4d167b1671edb8367beb5514104938ac
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2021
ms.locfileid: "117029240"
---
# <a name="migrate-network-resources-to-global-azure"></a>Migración de recursos de red a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

La mayoría de servicios de red no admiten la migración de Azure Alemania a Azure global. Sin embargo, puede conectar las redes en ambos entornos en la nube mediante una VPN de sitio a sitio. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Los pasos que debe seguir para configurar una VPN de sitio a sitio entre nubes son similares a los pasos que debe seguir para implementar una VPN de sitio a sitio entre su red local y Azure. Defina una puerta de enlace en ambas nubes y, a continuación, indique a las VPN cómo comunicarse entre sí. [Cree una conexión de sitio a sitio en Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md) que describe los pasos que debe seguir para implementar una VPN de sitio a sitio. Este es un resumen de los pasos:

1. Defina una red virtual.
1. Defina un espacio de direcciones.
1. Defina las subredes.
1. Defina una subred de puerta de enlace.
1. Defina una puerta de enlace para la red virtual.
1. Defina una puerta de enlace para la red local (el dispositivo VPN local).
1. Configure un dispositivo VPN local.
1. Cree la conexión.

Para conectar las redes virtuales entre Azure global y Azure Alemania:

1. Complete los pasos 1 a 5 del procedimiento anterior en Azure global.
1. Complete los pasos 1 a 5 en Azure Alemania.
1. Complete el paso 6 en Azure global:
   - Escriba la dirección IP pública de la puerta de enlace VPN en Azure Alemania.
1. Complete el paso 6 en Azure Alemania:
   - Escriba la dirección IP pública de la puerta de enlace VPN en Azure global.
1. Omita el paso 7.
1. Complete el paso 8.

## <a name="virtual-networks"></a>Redes virtuales

En este momento no se admite la migración de las redes virtuales desde Azure Alemania a Azure global. Se recomienda crear nuevas redes virtuales en la región de destino y que migre los recursos a esas redes virtuales.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Azure Virtual Network](../virtual-network/index.yml).
- Revise la [introducción a Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
- Obtenga información sobre cómo [planear redes virtuales](../virtual-network/virtual-network-vnet-plan-design-arm.md).

## <a name="network-security-groups"></a>Grupos de seguridad de red

En este momento no se admite la migración de grupos de seguridad de red desde Azure Alemania a Azure global. Se recomienda crear nuevos grupos de seguridad de red en la región de destino y que aplique las reglas de los grupos de seguridad de red al nuevo entorno de aplicación.

Obtenga la configuración actual de cualquier grupo de seguridad de red desde el portal o mediante la ejecución de los siguientes comandos de PowerShell:

```powershell
$nsg=Get-AzNetworkSecurityGroup -ResourceName <nsg-name> -ResourceGroupName <resourcegroupname>
Get-AzNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg
```

Para obtener más información:

- Actualice sus [conocimientos sobre los grupos de seguridad de red](../virtual-network/network-security-groups-overview.md#network-security-groups).
- Revise la [introducción a la seguridad de red](../virtual-network/network-security-groups-overview.md).
- Aprenda cómo [administrar grupos de seguridad de red](../virtual-network/manage-network-security-group.md).

## <a name="expressroute"></a>ExpressRoute

En este momento no se admite la migración de una instancia de Azure ExpressRoute desde Azure Alemania a Azure global. Se recomienda crear nuevos circuitos de ExpressRoute y una puerta de enlace de ExpressRoute en Azure global.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de ExpressRoute](../expressroute/index.yml).
- Aprenda a [crear una nueva puerta de enlace de ExpressRoute](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
- Aprenda sobre las [ubicaciones de ExpressRoute y proveedores de servicios](../expressroute/expressroute-locations.md).
- Lea sobre las [puertas de enlace de red virtual para ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="vpn-gateway"></a>VPN Gateway

En este momento no se admite la migración de una instancia de Azure VPN Gateway desde Azure Alemania a Azure global. Se recomienda crear y configurar una nueva instancia de VPN Gateway en Azure global.

Puede recopilar la información sobre la configuración actual de VPN Gateway mediante el portal o PowerShell. En PowerShell, use un conjunto de cmdlets que comienzan por `Get-AzVirtualNetworkGateway*`.

Asegúrese de que actualice la configuración local. Además, elimine todas las reglas existentes en los intervalos de direcciones IP antiguas después de actualizar el entorno de red de Azure.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de VPN Gateway](../vpn-gateway/index.yml).
- Aprenda cómo [crear de una conexión de sitio a sitio](../vpn-gateway/tutorial-site-to-site-portal.md).
- Revise los cmdlets [Get AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) de PowerShell.
- Lea la entrada de blob [Create a site-to-site connection](/archive/blogs/ralfwi/connecting-clouds) (Creación de una conexión de sitio a sitio).
 
## <a name="application-gateway"></a>Application Gateway

En este momento no se admite la migración de una instancia de Azure Application Gateway desde Azure Alemania a Azure global. Se recomienda crear y configurar una nueva puerta de enlace en Azure global.

Puede recopilar la información sobre la configuración actual la puerta de enlace mediante el portal o PowerShell. En PowerShell, use un conjunto de cmdlets que comienzan por `Get-AzApplicationGateway*`.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Application Gateway](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md).
- Aprenda a [crear una puerta de enlace de aplicaciones](../application-gateway/quick-create-portal.md).
- Revise los cmdlets [Get AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) de PowerShell.

## <a name="dns"></a>DNS

Para migrar la configuración de Azure DNS desde Azure Germany a Azure global, exporte el archivo de zona DNS y después impórtelo en la nueva suscripción. Actualmente, la única forma de exportar el archivo de zona es mediante la CLI de Azure.

Después de iniciar sesión en la suscripción de origen en Azure Alemania, configure la CLI de Azure para que utilice el modo Azure Resource Manager. Exporte la zona con este comando:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Ejemplo:

```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```

Este comando llama al servicio Azure DNS para exportar la zona `contoso.com` en el grupo de recursos `myresourcegroup`. La salida se almacena como un archivo de zona compatible con BIND en contoso.com.txt en la carpeta actual.

Cuando finaliza la exportación, elimine los registros NS del archivo de zona. Se crean nuevos registros NS para la nueva región y suscripción.

Ahora, inicie sesión en el entorno de destino, cree un nuevo grupo de recursos (o seleccione uno existente) e importe el archivo de zona:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Cuando se ha importado la zona, debe validarla mediante el comando siguiente:

```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```

Una vez finalizada la validación, póngase en contacto con su registrador de dominios y vuelva a delegar los registros NS. Para obtener información de registro NS, ejecute este comando:

```azurecli
az network dns record-set ns list -g <resource group> -z --output json
```

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Azure DNS](../dns/index.yml).
- Revise la [introducción a Azure DNS](../dns/dns-overview.md).
- Obtenga más información sobre la [importación y exportación de Azure DNS](../dns/dns-import-export.md).

## <a name="network-watcher"></a>Network Watcher

En este momento no se admite la migración de una instancia de Azure Network Watcher desde Azure Alemania a Azure global. Se recomienda crear y configurar una nueva instancia de Network Watcher en Azure global. Después, compare los resultados entre los entornos antiguo y nuevo.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Network Watcher](../network-watcher/index.yml).
- Revise la [introducción a Network Watcher](../network-watcher/network-watcher-monitoring-overview.md).
- Más información sobre los [registros de flujo de grupos de seguridad de red](../network-watcher/network-watcher-nsg-flow-logging-portal.md).
- Obtenga información sobre el [monitor de conexión](../network-watcher/connection-monitor.md).

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager puede ayudarle a realizar una migración perfecta. Sin embargo, no puede migrar los perfiles de Traffic Manager que cree en Azure Alemania en Azure global. (Durante una migración, se migran los puntos de conexión de Traffic Manager al entorno de destino, por lo que se necesita actualizar el perfil de Traffic Manager de todas formas).

Puede definir puntos de conexión adicionales en el entorno de destino mediante Traffic Manager mientras se ejecuta en el entorno de origen. Cuando Traffic Manager se ejecuta en el nuevo entorno, todavía puede definir los puntos de conexión que aún no han migrado en el entorno de origen. Este escenario se conoce como [escenario Blue-Green](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/). Este escenario implica los pasos siguientes:

1. Cree un nuevo perfil de Traffic Manager en Azure global.
1. Defina los puntos de conexión en Azure Alemania.
1. Cambie el registro CNAME de DNS en el nuevo perfil de Traffic Manager.
1. Desactive el perfil de Traffic Manager antiguo.
1. Migre y configure los puntos de conexión. Para cada punto de conexión en Azure Alemania:
   1. Migre el punto de conexión a Azure global.
   1. Cambie el perfil de Traffic Manager para usar el nuevo punto de conexión.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Traffic Manager](../traffic-manager/index.yml).
- Revise la [introducción a Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Aprenda a [crear un perfil de Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).

## <a name="load-balancer"></a>Load Balancer

En este momento no se admite la migración de una instancia del equilibrador de carga de Azure desde Azure Alemania a Azure global. Se recomienda crear y configurar un nuevo equilibrador de carga en Azure global.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Load Balancer](../load-balancer/index.yml).
- Revise la [introducción a Load Balancer](../load-balancer/load-balancer-overview.md).
- Obtenga información sobre cómo [crear un nuevo equilibrador de carga](../load-balancer/quickstart-load-balancer-standard-public-portal.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)
