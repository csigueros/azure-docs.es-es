---
title: Solución de problemas de emparejamiento de redes virtuales
description: Pasos para ayudar a resolver la mayoría de los problemas de emparejamiento de redes virtuales.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: cfdb79885563cbc0791ae3f3b2effd54ea3123a7
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867494"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Solución de problemas de emparejamiento de redes virtuales

En esta guía de solución de problemas se proporcionan los pasos para ayudarle a resolver la mayoría de los problemas de [emparejamiento de redes virtuales](virtual-network-peering-overview.md).

![Diagrama del emparejamiento de red virtual](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configuración de emparejamiento de redes virtuales entre dos redes virtuales

¿Las redes virtuales pueden estar en la misma suscripción o en suscripciones distintas?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Las redes virtuales deben estar en la misma suscripción

Para configurar el emparejamiento de red virtual para las redes virtuales que están en la misma suscripción, use los métodos que se indican en los artículos siguientes:

* Si las redes virtuales están en la *misma región*, consulte [Creación de un emparejamiento](./virtual-network-manage-peering.md#create-a-peering).
* Si las redes virtuales están en *diferentes regiones*, consulte [Emparejamiento de red virtual](./virtual-network-peering-overview.md). 

> [!Note]
> La conectividad no funciona a través del emparejamiento de red virtual global para los siguientes recursos: 
>
> * Máquinas virtuales (VM) detrás de la SKU básica del equilibrador de carga interno (ILB)
> * Redis Cache (usa la SKU básica del ILB)
> * Application Gateway v1 (usa la SKU básica de ILB)
> * Conjuntos de escalado de máquinas virtuales (usa la SKU básica de ILB)
> * Clústeres de Azure Service Fabric (usa la SKU básica de ILB)
> * Grupos de disponibilidad AlwaysOn de SQL Server (usa la SKU básica de ILB)
> * Azure App Service Environment para Power Apps (usa la SKU básica de ILB)
> * Azure API Management (usa la SKU básica de ILB)
> * Azure Active Directory Domain Services (Azure AD DS) (usa la SKU básica de ILB)

Para más información, consulte los [requisitos y restricciones](./virtual-network-peering-overview.md#requirements-and-constraints) del emparejamiento global.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Las redes virtuales se encuentran en suscripciones o inquilinos de Active Directory diferentes

Para configurar el emparejamiento de redes virtuales que se encuentren en diferentes suscripciones o inquilinos de Active Directory, consulte [Creación de emparejamiento: CLI de Azure](./create-peering-different-subscriptions.md#cli).

> [!Note]
> Para configurar el emparejamiento de red, debe tener permisos de **colaborador de red** en ambas suscripciones. Para obtener más información, consulte [Permisos de emparejamiento](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configuración del emparejamiento de redes virtuales con una topología de red en estrella tipo hub-and-spoke que use recursos locales

![Diagrama del emparejamiento de redes virtuales con radios locales](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Para una conexión de sitio a sitio o una conexión ExpressRoute

Siga los pasos descritos en: [Configuración del tránsito de la puerta de enlace de VPN para el emparejamiento de red virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Para conexiones de punto a sitio

1. Siga los pasos descritos en: [Configuración del tránsito de la puerta de enlace de VPN para el emparejamiento de red virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Después de que se establezca o se cambie el emparejamiento de red virtual, descargue y reinstale el paquete de punto a sitio para que los clientes de punto a sitio obtengan las rutas actualizadas en la red virtual en los radios.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configuración del emparejamiento de red virtual con una topología de red en estrella tipo hub-and-spoke

![Diagrama del emparejamiento de red virtual con un radio de red virtual](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Las redes virtuales están en la misma región


1. En la red virtual del centro, configure un dispositivo virtual de red (NVA).
1. En las redes virtuales en los radios, aplique el siguiente tipo de salto "aplicación virtual de red" a las rutas definidas por el usuario.

Para más información, consulte [Encadenamiento de servicios](./virtual-network-peering-overview.md#service-chaining).

> [!Note]
> Si necesita ayuda para configurar un NVA, [póngase en contacto con el proveedor de NVA](https://mskb.pkisolutions.com/kb/2984655).

Para obtener ayuda con la solución de problemas de configuración y enrutamiento del dispositivo NVA, consulte [Problemas de aplicaciones virtuales de red en Azure](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>Las redes virtuales están en regiones diferentes

Ya se admite el tránsito sobre el emparejamiento de red virtual global. La conectividad no funciona a través del emparejamiento de red virtual global para los siguientes recursos:

* VM tras la SKU de ILB básica
* Redis Cache (usa la SKU básica del ILB)
* Application Gateway (usa la SKU básica de ILB)
* Conjuntos de escalado (usa la SKU de ILB básica)
* Clústeres de Service Fabric (usa la SKU de ILB básica)
* Grupos de disponibilidad AlwaysOn de SQL Server (usa la SKU básica de ILB)
* App Service Environment (usa la SKU de ILB básica)
* API Management (usa la SKU de ILB básica)
* Azure AD DS (usa la SKU de ILB básica)

Para más información acerca de los requisitos y restricciones del emparejamiento global, consulte [Emparejamiento de red virtual](./virtual-network-peering-overview.md#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Solución de un problema de conectividad entre dos redes virtuales emparejadas

Inicie sesión en [Azure Portal](https://portal.azure.com/) con una cuenta que disponga de los [roles y permisos](virtual-network-manage-peering.md#permissions) necesarios. Seleccione la red virtual, seleccione **Emparejamiento** y, después, compruebe el campo **Estado**. ¿Cuál es el estado?

### <a name="the-peering-status-is-connected"></a>El estado de emparejamiento es "Conectado"

Para solucionar esta incidencia:

1. Compruebe los flujos de tráfico de red:

   Use [Solución de problemas de conexiones](../network-watcher/network-watcher-connectivity-overview.md) y la [Comprobación de flujo de IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) de la VM de origen a la VM de destino para determinar si hay un grupo de seguridad de red (NSG) o una ruta definida por el usuario (UDR) que cause interferencias en los flujos de tráfico.

   Si utiliza un firewall o NVA: 
   1. Documente los parámetros de UDR, con el fin de que pueda restaurarlos una vez completado este paso.
   2. Quite el UDR de la subred de la VM de origen o el NIC que apunta a NVA como el próximo salto. Compruebe la conectividad de la máquina virtual de origen directamente con el destino que está omitiendo el NVA. Si este paso no funciona, consulte el [solucionador de problemas de NVA](./virtual-network-troubleshoot-nva.md).

2. Tome un seguimiento de red: 
   1. Inicie un seguimiento de red en la VM de destino. En Windows, puede usar **Netsh**. Para Linux, use **TCPDump**.
   2. Ejecute **TcpPing** o **PsPing** de la IP de origen a la de destino.

      Este es un ejemplo de un comando **TcpPing**: `tcping64.exe -t <destination VM address> 3389`.

   3. Una vez se haya completado **TcpPing**, detenga el seguimiento de red en el destino.
   4. Si los paquetes llegan desde el origen, no hay ningún problema de red. Examine el firewall de la VM y la aplicación que escucha en ese puerto para localizar el problema de configuración.

   > [!Note]
   > No se puede conectar a los siguientes tipos de recursos a través del emparejamiento de redes virtuales globales (redes virtuales en regiones diferentes):
   >
   > * VM tras la SKU de ILB básica
   > * Redis Cache (usa la SKU básica del ILB)
   > * Application Gateway (usa la SKU básica de ILB)
   > * Conjuntos de escalado (usa la SKU de ILB básica)
   > * Clústeres de Service Fabric (usa la SKU de ILB básica)
   > * Grupos de disponibilidad AlwaysOn de SQL Server (usa la SKU básica de ILB)
   > * App Service Environment (usa la SKU de ILB básica)
   > * API Management (usa la SKU de ILB básica)
   > * Azure AD DS (usa la SKU de ILB básica)

Para más información, consulte los [requisitos y restricciones](./virtual-network-peering-overview.md#requirements-and-constraints) del emparejamiento global.

### <a name="the-peering-status-is-disconnected"></a>El estado de emparejamiento es "Desconectado"

Para resolver este problema, elimine el emparejamiento de ambas redes virtuales y, después, vuelva a crearlas.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Solución de una incidencia de seguridad entre una red virtual de tipo hub-and-spoke y un recurso local

¿Usa su red una instancia de VPN Gateway o un NVA de terceros?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Mi red usa una instancia de VPN Gateway o un NVA de terceros

Para solucionar problemas de conectividad que afectan a una instancia de VPN Gateway o un NVA de terceros, consulte los siguientes artículos:

* [Solucionador de problemas de NVA](./virtual-network-troubleshoot-nva.md)
* [Encadenamiento de servicios](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Mi red no usa una instancia de VPN Gateway ni un NVA de terceros

¿Tienen la red virtual del centro y la red virtual en los radios una puerta de enlace de VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Tanto la red virtual del centro como la red virtual en los radios tienen una puerta de enlace de VPN

No se admite el uso de una puerta de enlace remota.

Si la red virtual en los radios ya tiene una puerta de enlace de VPN, la opción **Usar puerta de enlace remota** no se admite debido a la limitación del emparejamiento de red virtual.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Ni la red virtual del centro ni la red virtual en los radios tienen una puerta de enlace de VPN

En el caso de las conexiones de sitio a sitio o de Azure ExpressRoute, compruebe las siguientes causas principales de las incidencias de conectividad con la red virtual remota desde el entorno local:

* En la red virtual que tiene una puerta de enlace, compruebe que la casilla **Permitir tráfico reenviado** está seleccionada.
* En la red virtual que no tiene una puerta de enlace, compruebe que la casilla **Usar puerta de enlace remota** está seleccionada.
* Haga que el administrador de red compruebe los dispositivos locales para comprobar que todos tienen el espacio de direcciones de red virtual remoto agregado.

Para conexiones de punto a sitio:

* En la red virtual que tiene una puerta de enlace, compruebe que la casilla **Permitir tráfico reenviado** está seleccionada.
* En la red virtual que no tiene una puerta de enlace, compruebe que la casilla **Usar puerta de enlace remota** está seleccionada.
* Descargue y vuelva a instalar el paquete de cliente de punto a sitio. Las rutas de red virtual recién emparejadas no agregan automáticamente rutas a los clientes de punto a sitio.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Solución de una incidencia de conectividad de red de tipo hub-and-spoke entre redes virtuales de radio de la misma región

Las redes del centro deben incluir un NVA. Configure UDR en los radios que tengan un NVA establecido como próximo salto y habilite **Permitir tráfico reenviado** en la red virtual del centro.

Para obtener más información, consulte [Encadenamiento de servicios](./virtual-network-peering-overview.md#service-chaining) y analice estos requisitos con el [proveedor de NVA](https://mskb.pkisolutions.com/kb/2984655) de su elección.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Solución de incidencias de conectividad de red de tipo hub-and-spoke entre redes virtuales de radio de diferentes regiones

Ya se admite el tránsito sobre el emparejamiento de red virtual global. La conectividad no funciona a través del emparejamiento de red virtual global para los siguientes recursos:

* VM tras la SKU de ILB básica
* Redis Cache (usa la SKU básica del ILB)
* Application Gateway (usa la SKU básica de ILB)
* Conjuntos de escalado (usa la SKU de ILB básica)
* Clústeres de Service Fabric (usa la SKU de ILB básica)
* Grupos de disponibilidad AlwaysOn de SQL Server (usa la SKU básica de ILB)
* App Service Environment (usa la SKU de ILB básica)
* API Management (usa la SKU de ILB básica)
* Azure AD DS (usa la SKU de ILB básica)

Para más información, consulte los [requisitos y restricciones](./virtual-network-peering-overview.md#requirements-and-constraints) del emparejamiento global y las [diferentes topologías de VPN](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Solución de incidencias de conectividad de red de tipo hub-and-spoke entre una aplicación web y la red virtual de radio

Para solucionar esta incidencia:

1. Inicie sesión en Azure Portal. 
1. En la aplicación web, seleccione **Redes** y, después, seleccione **Integración de VNET**.
1. Compruebe si puede ver la red virtual remota. Escriba manualmente el espacio de direcciones de la red virtual remota (**Sincronizar red** y **Agregar rutas**).

Para más información, consulte los siguientes artículos.

* [Integración de su aplicación con una instancia de Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md)
* [Información sobre el enrutamiento de VPN de punto a sitio](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Solución de problemas de un mensaje de error de configuración de emparejamiento de red virtual 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>El inquilino `<TENANT ID>` actual no está autorizado para acceder a la suscripción vinculada

Para resolver esta incidencia, consulte [Creación de emparejamiento: CLI de Azure](./create-peering-different-subscriptions.md#cli).

### <a name="not-connected"></a>No conectado

Para resolver esta incidencia, elimine el emparejamiento de ambas redes virtuales y, después, vuelva a crearlas.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>No se pudo emparejar una red virtual de Databricks

Para resolver esta incidencia, configure el emparejamiento de red virtual en **Azure Databricks** y, después, especifique la red virtual de destino mediante el **Id. de recurso**. Para obtener más información, consulte [Peer a Databricks virtual network to a remote virtual network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2) (Emparejamiento de una red virtual de Databricks con una red virtual remota).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>La red virtual remota no tiene una puerta de enlace

Este problema se produce cuando se emparejan redes virtuales de distintos inquilinos y posteriormente se quiere configurar `Use Remote Gateways`. Una limitación de Azure Portal es que no puede validar la presencia de una puerta de enlace de red virtual en la red virtual de otro inquilino.

Hay dos maneras de resolver este problema:

 * Elimine los emparejamientos y active la opción `Use Remote Gateways` cuando cree uno nuevo.
 * Use PowerShell o la CLI, en lugar de Azure Portal, para habilitar `Use Remote Gateways`.

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas de conectividad entre máquinas virtuales de Azure](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
