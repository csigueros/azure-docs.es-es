---
title: Solución de problemas de Azure Route Server
description: Obtenga información sobre cómo solucionar problemas de Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 08/30/2021
ms.author: duau
ms.openlocfilehash: 96e71dbd8a03e66644f0dc754d448a345978d940
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255672"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Solución de problemas de Azure Route Server

> [!IMPORTANT]
> Azure Route Server (versión preliminar) está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-issues"></a>Problemas de conectividad

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>¿Por qué mi NVA pierde la conectividad a Internet después de anunciar la ruta predeterminada (0.0.0.0/0) a Azure Route Server?
Cuando la aplicación virtual de red (NVA) anuncia la ruta predeterminada, Azure Route Server la programa para todas las máquinas virtuales de la red virtual, incluida la propia NVA. Esta ruta predeterminada establece la NVA como el próximo salto para todo el tráfico de Internet. Si su NVA necesita conectividad a Internet, debe configurar una [ruta definida por el usuario](../virtual-network/virtual-networks-udr-overview.md) para invalidar la ruta predeterminada en la NVA y adjuntar la ruta definida por el usuario a la subred donde se hospeda la NVA (vea el ejemplo siguiente). De lo contrario, la máquina host de la NVA seguirá enviando el tráfico de Internet, incluido el que envía la NVA, a la misma NVA.

| Ruta | Próximo salto |
|-------|----------|
| 0.0.0.0/0 | Internet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>¿Por qué puedo hacer ping desde mi NVA a la dirección IP del emparejamiento de BGP en Azure Route Server, pero después de configurar el emparejamiento de BGP entre ellos, ya no puedo hacer ping a la misma dirección IP? ¿Por qué deja de funcionar el emparejamiento de BGP?

En algunas NVA, debe agregar una ruta estática para la subred de Azure Route Server. Por ejemplo, si Azure Route Server se encuentra en 10.0.255.0/27 y la NVA se encuentra en 10.0.1.0/24, debe agregar la siguiente ruta a la tabla de enrutamiento en la NVA:

| Ruta | Próximo salto |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 es la dirección IP de la puerta de enlace predeterminada de la subred donde se hospeda la NVA (o de forma más precisa, una de las NIC).

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>¿Por qué se pierde la conectividad a la red local con ExpressRoute o con una VPN de Azure cuando se implementa Azure Route Server en una red virtual que ya tiene una puerta de enlace de ExpressRoute o una puerta de enlace de VPN de Azure?
Al implementar Azure Route Server en una red virtual, es necesario actualizar el plano de control entre las puertas de enlace y la red virtual. Durante esta actualización, hay un período de tiempo en el que las máquinas virtuales de la red virtual pierden la conectividad a la red local. Le recomendamos encarecidamente que programe un mantenimiento para implementar Azure Route Server en el entorno de producción.  

## <a name="control-plane-issues"></a>Problemas del plano de control

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>¿Por qué el emparejamiento de BGP entre mi NVA y Azure Route Server se activa y desactiva ("oscilación")?

La causa de la oscilación podría deberse a la configuración del temporizador de BGP. De manera predeterminada, el temporizador para mantener la conexión en Azure Route Server se establece en 60 segundos y el temporizador de retención en 180 segundos.

### <a name="why-does-my-on-premises-network-connected-to-azure-vpn-gateway-not-receive-the-default-route-advertised-by-azure-route-server"></a>¿Por qué mi red local conectada a Azure VPN Gateway no recibe la ruta predeterminada anunciada por Azure Route Server?

Aunque Azure VPN Gateway puede recibir la ruta predeterminada de sus pares BGP, incluido Azure Route Server, [no anuncia la ruta predeterminada](../vpn-gateway/vpn-gateway-vpn-faq.md#what-address-prefixes-will-azure-vpn-gateways-advertise-to-me) a otros elementos del mismo nivel. 

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>¿Por qué la NVA no recibe rutas de Azure Route Server aunque el emparejamiento de BGP esté activo?

Azure Route Server utiliza el ASN 65515. Asegúrese de configurar un ASN diferente para la NVA, de modo que se pueda establecer una sesión de "eBGP" entre la NVA y Azure Route Server para que la propagación de rutas se realice automáticamente. Asegúrese de habilitar "saltos múltiples" en la configuración de BGP porque la NVA y Azure Route Server se encuentran en distintas subredes de la red virtual.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>El emparejamiento de BGP entre la NVA y Azure Route Server está activo. Puedo ver que las rutas se intercambian correctamente entre ambos. ¿Por qué no están las rutas de la NVA en la tabla de enrutamiento efectiva de la máquina virtual? 

* Si la máquina virtual está en la misma red virtual que la NVA y Azure Route Server:

     Azure Route Server expone dos direcciones IP del emparejamiento de BGP que se hospedan en dos máquinas virtuales que comparten la responsabilidad de enviar las rutas a todas las demás máquinas virtuales que se ejecutan en la red virtual. Cada una de las NVA debe configurar dos sesiones de BGP idénticas (por ejemplo, usar el mismo número AS, la misma ruta AS y anunciar el mismo conjunto de rutas) en las dos máquinas virtuales para que las máquinas virtuales de la red virtual puedan obtener información de enrutamiento coherente desde Azure Route Server. Observe el siguiente diagrama.

    ![Diagrama que muestra una aplicación virtual de red con Route Server.](./media/faq/network-virtual-appliances.png)

    Si tiene dos o más instancias de la NVA, *puede* anunciar diferentes rutas AS para la misma ruta desde diferentes instancias de la NVA si quiere designar una instancia de NVA como activa y la otra como pasiva.

* Si la máquina virtual está en una red virtual diferente de la que hospeda la NVA y Azure Route Server. Compruebe si el emparejamiento de red virtual está habilitado entre las dos redes virtuales *y* si está habilitado el uso del servidor de rutas remoto en la red virtual de la máquina virtual.

### <a name="why-is-the-equal-cost-multi-path-ecmp-function-of-my-expressroute-turned-off-after-i-deploy-azure-route-server-to-the-virtual-network"></a>¿Por qué la función de enrutamiento multidireccional de igual coste (ECMP) de mi conexión de ExpressRoute está desactivada después de implementar Azure Route Server en la red virtual?

Cuando anuncia las mismas rutas desde su red local a Azure a través de varias conexiones de ExpressRoute, normalmente ECMP está habilitado de manera predeterminada para el tráfico destinado a dichas rutas desde Azure de vuelta a su entorno local. Sin embargo, una vez implementado el servidor de rutas, la información de varias rutas de acceso se pierde en el intercambio BGP entre ExpressRoute y Azure Route Server, por lo que el tráfico de Azure atravesará solo una de las conexiones de ExpressRoute. Esta limitación se eliminará en la versión futura de Azure Route Server.  

## <a name="next-steps"></a>Pasos siguientes

[Configuración de una instancia de Azure Route Server](quickstart-configure-route-server-powershell.md)
