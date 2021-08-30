---
title: Archivo de inclusión
description: Archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a237122aaeba57d8c3aa99274a0cc41232729eca
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676943"
---
### <a name="is-nat-supported-on-all-azure-vpn-gateway-skus"></a>¿Se admite NAT en todas las SKU de Azure VPN Gateway?

NAT se admite en VpnGw2~5 y VpnGw2AZ~5AZ.

### <a name="can-i-use-nat-on-vnet-to-vnet-or-p2s-connections"></a>¿Puedo usar NAT en conexiones de red virtual a red virtual o P2S?

No, NAT solo se admite en conexiones entre locales de **IPsec**.

### <a name="how-many-nat-rules-can-i-use-on-a-vpn-gateway"></a>¿Cuántas reglas NAT puedo usar en una puerta de enlace de VPN?

Puede crear hasta 100 reglas NAT (reglas Ingress y Egress combinadas) en una puerta de enlace de VPN.

### <a name="is-nat-applied-to-all-connections-on-a-vpn-gateway"></a>¿Se aplica NAT a todas las conexiones en una puerta de enlace de VPN?

NAT se aplica a las conexiones con reglas NAT. Si una conexión no tiene ninguna regla NAT, no se le aplica NAT. En la misma puerta de enlace de VPN, puede tener algunas conexiones con NAT y otras sin NAT que funcionen conjuntamente.

### <a name="what-types-of-nat-is-supported-on-azure-vpn-gateways"></a>¿Qué tipos de NAT se admiten en las puertas de enlace de VPN de Azure?

Solo NAT 1:1 estática. NO se admite NAT dinámica ni NAT64.

### <a name="does-nat-work-on-active-active-vpn-gateways"></a>¿Funciona NAT en puertas de enlace VPN activo-activo?

Sí. NAT funciona en puertas de enlace VPN activo-activo y en espera activo.

### <a name="does-nat-work-with-bgp-connections"></a>¿Funciona NAT con las conexiones BGP?

Sí, se puede usar BGP con NAT. A continuación se indican algunas consideraciones importantes:

* Seleccione **Enable BGP Route Translation** (Habilitar traducción de rutas BGP) en la página de configuración de reglas NAT para asegurarse de que las rutas aprendidas y las rutas anunciadas se traducen a prefijos de dirección posteriores a NAT (asignaciones externas) en función de las reglas NAT asociadas a las conexiones. Debe asegurarse de que los enrutadores BGP locales anuncien los mismos prefijos, tal y como se define en las reglas IngressSNAT.

* Si el enrutador VPN local usa APIPA (169.254.x.x) como IP del par o del altavoz BGP, use la dirección APIPA directamente en el campo **Dirección IP del par BGP** de la puerta de enlace de red local. Si el enrutador VPN local usa una dirección normal que no es de APIPA y se intercala con el espacio de direcciones de la red virtual o con otros espacios de red locales, asegúrese de que la regla IngressSNAT traducirá la dirección IP del par BGP a una dirección única no superpuesta y colocará la dirección posterior a NAT en el campo **Dirección IP del par BGP** de la puerta de enlace de red local.

### <a name="do-i-need-to-create-the-matching-dnat-rules-for-the-snat-rule"></a>¿Debo crear las reglas DNAT correspondientes para la regla SNAT?

No. Una sola regla SNAT define la traducción para **ambas** direcciones de una red determinada:

* Una regla IngressSNAT define la traducción de las direcciones IP de origen que llegan **a** la puerta de enlace de VPN de Azure desde la red local. También controla la traducción de las direcciones IP de destino que salen de la red virtual hacia la misma red local.

* Una regla EgressSNAT define la traducción de las direcciones IP de origen de la red virtual que salen de la puerta de enlace de VPN de Azure a redes locales. También controla la traducción de las direcciones IP de destino para los paquetes que llegan a la red virtual a través de esas conexiones con la regla EgressSNAT.

* En cualquier caso, no se necesita **ninguna regla DNAT**.

### <a name="what-do-i-do-if-my-vnet-or-local-network-gateway-address-space-has-two-or-more-prefixes-can-i-apply-nat-to-all-of-them-or-just-a-subset"></a>¿Qué debo hacer si el espacio de direcciones de la puerta de enlace de red local o de red virtual tiene dos o más prefijos? ¿Puedo aplicarles NAT a todos? ¿O solo a un subconjunto?

Debe crear una regla NAT para cada prefijo que necesite en NAT, ya que cada regla NAT solo puede incluir un prefijo de dirección para NAT. Por ejemplo, si el espacio de direcciones de la puerta de enlace de red local se compone de 10.0.1.0/24 y 10.0.2.0/25, puede crear dos reglas tal y como se muestra a continuación:

* Regla IngressSNAT 1: asignar 10.0.1.0/24 a 100.0.1.0/24
* Regla IngressSNAT 2: asignar 10.0.2.0/25 a 100.0.2.0/25

Las dos reglas deben hacer coincidir las longitudes de los prefijos de dirección correspondientes. Lo mismo se aplica a las reglas EgressSNAT para un espacio de direcciones de red virtual.

> [!IMPORTANT]
> Si solo vincula una regla a la conexión anterior, el otro espacio de direcciones **NO** se traducirá.

### <a name="can-i-use-different-egresssnat-rules-to-translate-my-vnet-address-space-to-different-prefixes-to-different-on-premises-networks"></a>¿Puedo usar reglas EgressSNAT distintas para traducir el espacio de direcciones de red virtual a distintos prefijos para redes locales diferentes?

Sí, puede crear varias reglas EgressSNAT para el mismo espacio de direcciones de red virtual y aplicar dichas reglas a distintas conexiones. Para las conexiones sin ninguna regla EgressSNAT,

### <a name="can-i-use-the-same-ingresssnat-rule-on-different-connections"></a>¿Puedo usar la misma regla IngressSNAT en conexiones distintas?

Sí, esto suele hacerse cuando son conexiones para la misma red local a fin de proporcionar redundancia. No se puede usar la misma regla Ingress si las conexiones son para redes locales distintas.

### <a name="do-i-need-both-ingress-and-egress-rules-on-a-nat-connection"></a>¿Necesito tanto reglas Ingress como Egress en una conexión NAT?

Necesita tanto reglas Ingress como Egress en la misma conexión cuando el espacio de direcciones de red local se superpone con el espacio de direcciones de la red virtual. Si el espacio de direcciones de la red virtual es único entre todas las redes conectadas, no se necesitará la regla EgressSNAT en esas conexiones. Puede usar las reglas Ingress para evitar la superposición de direcciones entre las redes locales.