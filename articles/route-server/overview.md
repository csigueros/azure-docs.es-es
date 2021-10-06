---
title: ¿Qué es Azure Route Server?
description: Obtenga información sobre cómo puede simplificar Azure Route Server el enrutamiento entre la aplicación virtual de red (NVA) y la red virtual.
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 09/27/2021
ms.author: duau
ms.openlocfilehash: 34835f8f3e536b875c7eb4afd176ab00fb1f417c
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091433"
---
# <a name="what-is-azure-route-server"></a>¿Qué es Azure Route Server? 

Azure Route Server simplifica el enrutamiento dinámico entre la aplicación virtual de red (NVA) y la red virtual. Permite intercambiar información de enrutamiento directamente mediante el protocolo de enrutamiento BGP (Protocolo de puerta de enlace de borde) entre cualquier NVA que admita el protocolo de enrutamiento BGP y la red definida por software (SDN) de Azure en la red virtual de Azure (VNET) sin necesidad de configurar ni mantener manualmente las tablas de rutas. Azure Route Server es un servicio totalmente administrado y está configurado con alta disponibilidad.

> [!IMPORTANT]
> Si tiene un Azure Route Server creado antes del 1 de septiembre y no tiene una dirección IP pública asociada, deberá volver a crear el Route Server para que pueda obtener una dirección IP con fines de administración.

## <a name="how-does-it-work"></a>¿Cómo funciona?

En el siguiente diagrama se ilustra cómo funciona Azure Route Server con una NVA SDWAN y una NVA de seguridad en una red virtual. Una vez que haya establecido el emparejamiento de BGP, Azure Route Server recibirá una ruta local (10.250.0.0/16) del dispositivo SDWAN y una ruta predeterminada (0.0.0.0/0) del firewall. A continuación, estas rutas se configuran automáticamente en las máquinas virtuales de la red virtual. Como resultado, todo el tráfico destinado a la red local se enviará al dispositivo SDWAN. Por otro lado, todo el tráfico relacionado con Internet se enviará al firewall. En la dirección opuesta, Azure Route Server enviará la dirección de la red virtual (10.1.0.0/16) a ambas NVA. El dispositivo SDWAN puede propagarlo a la red local.

![Diagrama que muestra Azure Route Server configurado en una red virtual.](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>Ventajas principales 

Azure Route Server simplifica la configuración, la administración y la implementación de la NVA en la red virtual.  

* Ya no es necesario actualizar manualmente la tabla de enrutamiento en la NVA cada vez que se actualicen las direcciones de la red virtual. 

* Ya no es necesario actualizar manualmente las [rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md) cada vez que la NVA anuncie nuevas rutas o retire las anteriores. 

* Puede emparejar varias instancias de su NVA con Azure Route Server. Puede configurar los atributos BGP en la NVA y, en función del diseño (por ejemplo, activo-activo para el rendimiento o activo-pasivo para la resistencia), permitir que Azure Route Server sepa qué instancia de NVA es activa o pasiva. 

* La interfaz entre la NVA y Azure Route Server se basa en un protocolo estándar común. Siempre que la NVA admita BGP, puede emparejarla con Azure Route Server. Para más información, consulte [Protocolos de enrutamiento admitidos por Route Server](route-server-faq.md#protocol).

* Puede implementar Azure Route Server en cualquiera de las redes virtuales nuevas o existentes. 

## <a name="faq"></a>Preguntas más frecuentes

Para ver las preguntas más frecuentes sobre Azure Route Server, consulte [Preguntas frecuentes sobre Azure Route Server](route-server-faq.md).

## <a name="next-steps"></a>Pasos siguientes

- [Consultar cómo configurar Azure Route Server](quickstart-configure-route-server-powershell.md)
- [Compatibilidad de Azure Route Server (versión preliminar) con ExpressRoute y VPN de Azure](expressroute-vpn-support.md)
