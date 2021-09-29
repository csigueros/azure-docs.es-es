---
title: ¿Qué es Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Información general sobre las características, la arquitectura y la implementación del Equilibrador de carga de Azure Aprenda cómo funciona Load Balancer y cómo usarlo en la nube.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 3d3c1d9937382080ea5a735e3f67e9767919366f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740772"
---
# <a name="what-is-azure-load-balancer"></a>¿Qué es Azure Load Balancer?

El *equilibrio de carga* hace referencia a la distribución uniforme de la carga (el tráfico de red entrante) en un grupo de recursos o servidores de back-end. 

Azure Load Balancer opera en la capa 4 del modelo Interconexión de sistemas abiertos (OSI). Es el único punto de contacto de los clientes. Load Balancer distribuye flujos de entrada que llegan al front-end del equilibrador de carga a las instancias del grupo de back-end. Estos flujos están de acuerdo con las reglas de equilibrio de carga y los sondeos de estado configurados. Las instancias del grupo de back-end pueden ser instancias de Azure Virtual Machines o de un conjunto de escalado de máquinas virtuales.

Un **[equilibrador de carga público](./components.md#frontend-ip-configurations)** puede proporcionar conexiones de salida para máquinas virtuales dentro de la red virtual. Estas conexiones se realizan mediante la traducción de sus direcciones IP privadas a direcciones IP públicas. Las instancias públicas de Load Balancer se usan para equilibrar la carga del tráfico de Internet en las máquinas virtuales.

Un **[equilibrador de carga interno (o privado)](./components.md#frontend-ip-configurations)** se usa cuando se necesitan direcciones IP privadas solo en el front-end. Los equilibradores de carga internos se usan para equilibrar la carga del tráfico dentro de una red virtual. También se puede acceder a un servidor front-end del equilibrador de carga desde una red local en un escenario híbrido.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Ilustración: Equilibrar las aplicaciones de niveles múltiples mediante Load Balancer público e interno*

Para más información sobre los componentes individuales de Load Balancer, consulte [Componentes de Azure Load Balancer](./components.md).

>[!NOTE]
> Azure ofrece un conjunto de soluciones de equilibrio de carga completamente administradas para sus escenarios. 
> * Si busca un enrutamiento global basado en DNS y **no** tiene requisitos de finalización con el protocolo de seguridad de la capa de transporte (TLS) ("descarga SSL"), de procesamiento de niveles de aplicación o por solicitud HTTP/HTTPS, revise [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Si quiere equilibrar la carga entre los servidores de una región en la capa de aplicación, consulte [Application Gateway](../application-gateway/overview.md).
> * Si necesita optimizar el enrutamiento global del tráfico web y optimizar el rendimiento y la confiabilidad de los usuarios finales de nivel superior mediante una conmutación por error global rápida, consulte [Front Door](../frontdoor/front-door-overview.md).
> 
> Sus escenarios pueden beneficiarse de extremo a extremo de la combinación de estas soluciones según sea necesario.
> Si desea ver una comparación de las distintas opciones de equilibrio de carga de Azure, consulte [Información general sobre las opciones de equilibrio de carga en Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="why-use-azure-load-balancer"></a>Uso de Azure Load Balancer
Con Azure Load Balancer puede escalar las aplicaciones y crear servicios con alta disponibilidad. Load Balancer admite escenarios de entrada y salida. Una instancia de Load Balancer proporciona baja latencia y alto rendimiento, y puede escalar hasta millones de flujos para todas las aplicaciones TCP y UDP.

Entre los escenarios principales que puede llevar a cabo con Azure Standard Load Balancer se incluyen:

- Equilibrio de carga del tráfico **[interno](./quickstart-load-balancer-standard-internal-portal.md)** y **[externo](./quickstart-load-balancer-standard-public-portal.md)** a las máquinas virtuales de Azure.

- Aumento de la disponibilidad mediante la distribución de recursos **[en zonas](./tutorial-load-balancer-standard-public-zonal-portal.md)** y **[a través de ellas](./quickstart-load-balancer-standard-public-portal.md)** .

- Configuración de la **[conectividad de salida](./load-balancer-outbound-connections.md)** para máquinas virtuales de Azure.

- Uso de **[sondeos de estado](./load-balancer-custom-probe-overview.md)** para supervisar los recursos con equilibrio de carga.

- Empleo del **[desvío de puertos](./tutorial-load-balancer-port-forwarding-portal.md)** para acceder a las máquinas virtuales de una red virtual mediante la dirección IP pública y el puerto.

- Habilitación de la compatibilidad con el **[equilibrio de carga](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** de **[IPv6](../virtual-network/ipv6-overview.md)** .

- Standard Load Balancer proporciona métricas multidimensionales mediante [Azure Monitor](../azure-monitor/overview.md).  Estas métricas se pueden filtrar, agrupar y desglosar para una dimensión determinada.  Proporcionan una perspectiva actual e histórica del rendimiento y el mantenimiento del servicio. [Información para Azure Load Balancer](./load-balancer-insights.md) ofrece un panel preconfigurado con visualizaciones útiles para estas métricas.  También se admite Resource Health. Consulte **[Diagnósticos de Standard Load Balancer](load-balancer-standard-diagnostics.md)** para más información.

- Servicios de equilibrio de carga en **[varios puertos, varias direcciones IP, o en ambos](./load-balancer-multivip-overview.md)** .

- Desplazamiento de los recursos **[internos](./move-across-regions-internal-load-balancer-portal.md)** y **[externos](./move-across-regions-external-load-balancer-portal.md)** del equilibrador de carga por las regiones de Azure.

- Equilibrio de carga del flujo de TCP y UDP en todos los puertos simultáneamente mediante los **[puertos de alta disponibilidad](./load-balancer-ha-ports-overview.md)** .

### <a name="secure-by-default"></a><a name="securebydefault"></a>Seguro de forma predeterminada

* Standard Load Balancer se basa en el modelo de seguridad de red de confianza cero.

* Standard Load Balancer es seguro de forma predeterminada y forma parte de la red virtual. La red virtual es una red privada y aislada.  

* Las instancias de Standard Load Balancer y las direcciones IP públicas estándar están cerradas para las conexiones de entrada a menos que las abran los grupos de seguridad de red. Los grupos de seguridad de red se usan para permitir explícitamente el tráfico admitido.  Si no tiene ningún grupo de seguridad de red en una subred o NIC del recurso de máquina virtual, no se permitirá que el tráfico llegue a este recurso. Para más información sobre los NSG y cómo aplicarlos en su escenario, consulte [Grupos de seguridad de red](../virtual-network/network-security-groups-overview.md).

* Load Balancer Básico está abierto a Internet de forma predeterminada. 

* Load Balancer no almacena datos de clientes.

## <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio

Para más información sobre los precios de Standard Load Balancer, consulte [Precios de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
Load Balancer Básico se ofrece sin cargo.
Consulte [Acuerdo de Nivel de Servicio para Load Balancer](https://aka.ms/lbsla). Load Balancer Básico no tiene Acuerdo de Nivel de Servicio.

## <a name="whats-new"></a>Novedades

Suscríbase a la fuente RSS y vea las actualizaciones más recientes de las características de Azure Load Balancer en la página [Actualizaciones de Azure](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="next-steps"></a>Pasos siguientes

Consulte el artículo sobre cómo [crear una instancia de Standard Load Balancer pública](quickstart-load-balancer-standard-public-portal.md) para empezar a usar un equilibrador de carga.

Para más información acerca de las limitaciones y los componentes de Azure Load Balancer, consulte [Componentes de Azure Load Balancer](./components.md) y [Conceptos de Azure Load Balancer](./concepts.md).