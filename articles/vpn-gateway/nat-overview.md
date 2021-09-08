---
title: Acerca de NAT (traducción de direcciones de red) en VPN Gateway
titleSuffix: Azure VPN Gateway
description: Obtenga información sobre NAT (traducción de direcciones de red) en VPN de Azure para conectar redes con espacios de direcciones superpuestos.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/22/2021
ms.author: yushwang
ms.openlocfilehash: 2bcf33466b3cbf57a67307961bb1e21e3a810a72
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112759674"
---
# <a name="about-nat-on-azure-vpn-gateway-preview"></a>Acerca de NAT en Azure VPN Gateway (versión preliminar)

En este artículo se proporciona información general sobre la compatibilidad con NAT (traducción de direcciones de red) en Azure VPN Gateway. NAT define los mecanismos para traducir una dirección IP a otra en un paquete IP. Hay varios escenarios para NAT:

* Conexión de varias redes con direcciones IP superpuestas
* Conexión de redes con direcciones IP privadas (RFC1918) a Internet (interrupción de Internet)
* Conexión de Redes IPv6 a redes IPv4 (NAT64)

> [!IMPORTANT]
> NAT de Azure VPN Gateway admite el primer escenario para conectar redes locales o sucursales a una red virtual de Azure con direcciones IP superpuestas. **No** se admiten la interrupción de Internet ni NAT64.

## <a name="overlapping-address-spaces"></a><a name="why"></a>Espacios de direcciones superpuestos

Las organizaciones usan normalmente direcciones IP privadas definidas en RFC1918 para la comunicación interna en sus redes privadas. Cuando estas redes se conectan mediante VPN por Internet o una WAN privada, los espacios de direcciones **no deben** superponerse; de lo contrario, se produciría un error en la comunicación. Para conectar dos o más redes con direcciones IP superpuestas, se implementa NAT en los dispositivos de puerta de enlace que conectan las redes. 

## <a name="nat-type-static--dynamic"></a><a name="type"></a>Tipo de NAT: estática y dinámica

NAT en un dispositivo de puerta de enlace traduce las direcciones IP de origen o destino, en función de las reglas o directivas NAT para evitar conflictos de direcciones. Hay diferentes tipos de reglas de traducción NAT:

* **NAT estática**: las reglas estáticas definen una relación de asignación de direcciones fija. Para una dirección IP concreta, se asignará a la misma dirección del grupo de destino. Las asignaciones de reglas estáticas no tienen estado porque la asignación es fija.

* **NAT dinámica**: para NAT dinámica, una dirección IP se puede traducir a distintas direcciones IP de destino en función de la disponibilidad, o bien con una combinación diferente de dirección IP y puerto TCP/UDP. Esto último también se denomina NAPT, dirección de red y traducción de puertos. Las reglas dinámicas darán como resultado asignaciones de traducción con estado en función de los flujos de tráfico en un momento dado.

Otra consideración es el tamaño del grupo de direcciones para la traducción. Si el tamaño del grupo de direcciones de destino es el mismo que el grupo de direcciones original, use la regla NAT estática para definir una asignación 1:1 en un orden secuencial. Si el grupo de direcciones de destino es menor que el grupo de direcciones original, use la regla NAT dinámica para adaptarse a las diferencias.

> [!IMPORTANT]
> * NAT de Azure VPN Gateway solo admite reglas NAT estáticas 1:1. No se admiten reglas NAT dinámicas.
> * NAT se admite en las SKU siguientes: VpnGw2~5, VpnGw2AZ~5AZ.
> * NAT solo se admite en conexiones entre entornos locales de IPsec. No se admiten conexiones de red virtual a red virtual ni conexiones de punto a sitio.

## <a name="nat-mode-ingress--egress"></a><a name="mode"></a>Modo de NAT: entrada y salida

Cada regla NAT define una relación de asignación o traducción de direcciones para el espacio de direcciones de red correspondiente:

* Entrada: una regla **IngressSNAT** asigna un espacio de direcciones de red local a un espacio de direcciones traducido para evitar la superposición de direcciones.

* Salida: una regla **EgressSNAT** asigna el espacio de direcciones de la red virtual de Azure a otro espacio de direcciones traducido. 

Para cada regla NAT, los dos campos siguientes especifican los espacios de direcciones antes y después de la traducción:

* **Asignaciones internas**: es el espacio de direcciones **antes** de la traducción. Para una regla de entrada, este campo se corresponde al espacio de direcciones original de la red local. Para una regla de salida, es el espacio de direcciones de la red virtual original.

* **Asignaciones externas**: es el espacio de direcciones **después** de la traducción para redes locales (entrada) o VNet (salida). En el caso de distintas redes conectadas a una puerta de enlace de VPN de Azure, los espacios de direcciones de todas las **asignaciones externas** no deben superponerse entre sí y con las redes conectadas sin NAT.

## <a name="nat-and-routing"></a><a name="routing"></a>NAT y el enrutamiento

Una vez que se haya definido una regla NAT para una conexión, el espacio de direcciones efectivo para la conexión cambiará con la regla. Si BGP está habilitado en Azure VPN Gateway, seleccione "Habilitar traducción de rutas BGP" para convertir automáticamente las rutas aprendidas y anunciadas en las conexiones con reglas NAT:

* Rutas aprendidas: los prefijos de destino de las rutas aprendidas sobre una conexión con las reglas IngressSNAT se traducirán de los prefijos de asignación interna (anteriores a NAT) a los prefijos de asignación externa (posteriores a NAT) de esas reglas.

* Rutas anunciadas: Azure VPN Gateway anunciará los prefijos de asignación externa (posteriores a NAT) de las reglas EgressSNAT para el espacio de direcciones de la red virtual y las rutas aprendidas con prefijos de direcciones posteriores a NAT de otras conexiones.

* Consideración de la dirección IP emparejada mediante BGP para una red local con NAT:
   * Dirección APIPA (169.254.0.1 a 169.254.255.254): no se necesita ninguna regla NAT; especifique la dirección APIPA directamente en la puerta de enlace de red local.
   * Dirección que no es de APIPA: especifique la dirección IP **traducida** o **posterior a NAT** en la puerta de enlace de red local. Use las direcciones IP con BGP de Azure **traducidas** o **posteriores a NAT** para configurar los enrutadores VPN locales. Asegúrese de que las reglas NAT están definidas para la traducción prevista.

> [!NOTE]
> Las rutas aprendidas en las conexiones sin reglas IngressSNAT no se convertirán. Las rutas de red virtual anunciadas a las conexiones sin reglas EgressSNAT tampoco se convertirán.
>

## <a name="nat-example"></a><a name="example"></a>Ejemplo de NAT

En el diagrama siguiente se muestra un ejemplo de las configuraciones NAT de VPN de Azure:

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="Diagrama de NAT." lightbox="./media/nat-overview/vpn-nat.png" border="false":::

En el diagrama se muestra una red virtual de Azure y dos redes locales, todas con el espacio de direcciones de 10.0.1.0/24. Para conectar estas dos redes a la red virtual de Azure y la puerta de enlace de VPN, cree las reglas siguientes:

* Regla IngressSNAT 1: esta regla traduce el espacio de direcciones local 10.0.1.0/24 a 100.0.2.0/24.

* Regla IngressSNAT 2: esta regla traduce el espacio de direcciones local 10.0.1.0/24 a 100.0.3.0/24.

* Regla EgressSNAT 1: esta regla traduce el espacio de direcciones de VNet 10.0.1.0/24 a 100.0.1.0/24.

En el diagrama, cada recurso de conexión tiene las reglas siguientes:

* Conexión 1 (VNet-Branch1):
    * Regla 1 de IngressSNAT
    * Regla 1 de EgressSNAT

* Conexión 2 (VNet-Branch2)
    * Regla 2 de IngressSNAT
    * Regla 1 de EgressSNAT

En función de las reglas asociadas a las conexiones, estos son los espacios de direcciones para cada red:

| Red  | Original    | Traducidos   |
| ---      | ---         | ---          |
| VNet     | 10.0.1.0/24 | 100.0.1.0/24 |
| Rama 1 | 10.0.1.0/24 | 100.0.2.0/24 |
| Rama 2 | 10.0.1.0/24 | 100.0.3.0/24 |

En el diagrama siguiente se muestra un paquete IP desde Branch 1 a la red virtual, antes y después de la traducción NAT:

:::image type="content" source="./media/nat-overview/nat-packet.png" alt-text="Diagrama en el que se muestra el antes y el después de la traducción NAT." lightbox="./media/nat-overview/nat-packet.png" border="false":::

> [!IMPORTANT]
> Una sola regla SNAT define la traducción para **ambas** direcciones de una red concreta:
>
> * Una regla IngressSNAT define la traducción de las direcciones IP de origen que llegan **a** la puerta de enlace de VPN de Azure desde la red local. También controla la traducción de las direcciones IP de destino que salen de la red virtual hacia la misma red local.
> * Una regla EgressSNAT define la traducción de las direcciones IP de origen que salen de la puerta de enlace de VPN de Azure a redes locales. También controla la traducción de las direcciones IP de destino para los paquetes que llegan a la red virtual por medio de esas conexiones con la regla EgressSNAT.
> * En cualquier caso, no se necesita **ninguna regla DNAT**.

## <a name="nat-configuration"></a><a name="config"></a>Configuración de NAT

Para implementar la configuración de NAT como se ha mostrado antes, cree primero las reglas NAT en la puerta de enlace de VPN de Azure y, después, cree las conexiones con las reglas NAT correspondientes asociadas. Vea [Configuración de NAT en puertas de enlace de VPN de Azure](nat-howto.md) para obtener los pasos a fin de configurar NAT para las conexiones entre entornos locales.

## <a name="nat-faq"></a><a name="faq"></a>Preguntas más frecuentes sobre NAT

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Vea [Configuración de NAT en puertas de enlace de VPN de Azure](nat-howto.md) para obtener los pasos a fin de configurar NAT para las conexiones entre entornos locales.