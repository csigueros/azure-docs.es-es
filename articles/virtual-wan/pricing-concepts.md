---
title: Acerca de los precios de Virtual WAN
titleSuffix: Azure Virtual WAN
description: En este artículo, se responden las preguntas más frecuentes sobre los precios de Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: cherylmc
ms.custom: references_pricing
ms.openlocfilehash: 88a9c96cd2888f738251439df4d5da5343e8bf1d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451344"
---
# <a name="about-virtual-wan-pricing"></a>Acerca de los precios de Virtual WAN

Azure Virtual WAN es un servicio de red que aporta muchas funciones de red, seguridad y enrutamiento para proporcionar una única interfaz operativa. Estas funcionalidades incluyen la conectividad de ramas (mediante la automatización de la conectividad desde dispositivos de asociados de Virtual WAN, como SD-WAN o VPN CPE), la conectividad VPN de sitio a sitio, la conectividad VPN de usuarios remotos (de punto a sitio), la conectividad privada (ExpressRoute), la conectividad interna de la nube (conectividad transitiva para redes virtuales), la interconectividad de VPN ExpressRoute, el enrutamiento, Azure Firewall y el cifrado para la conectividad privada.

En este artículo, se abordan tres escenarios implementados habitualmente con Azure Virtual WAN y estimaciones de precios típicas para las implementaciones en función de los precios enumerados. Además, puede haber muchos otros escenarios en los Virtual WAN puede ser útil.

> [!IMPORTANT]
> Los precios que se muestran en este artículo están pensados para usarse solo con fines de ejemplo. 
>  * Los precios pueden cambiar en cualquier momento. Para más información sobre los precios actuales, consulte la página [Precios de Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/). 
>  * Los cargos entre centros (de centro a centro) no se muestran en la página de precios de Virtual WAN, ya que están sujetos a los cargos interregionales (intra/intercontinentales). Para más información, consulte [Precios de Ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).
>

## <a name="pricing-components"></a><a name="pricing"></a>Componentes de los precios

En el diagrama siguiente, se muestran las rutas de datos típicas de una red que implican a Virtual WAN y los distintos componentes de los precios por hora y por GB.

:::image type="content" source="./media/pricing-concepts/data-routes.png" alt-text="Diagrama de rutas de datos." lightbox="./media/pricing-concepts/data-routes.png":::

| Value | Escenario| Costo por hora | Costo por GB |
| --- | --- | --- | --- |
| 1 | Transferencia de datos desde una red virtual de radio a una rama VPN de sitio a sitio mediante un centro de vWAN Estándar en Este de EE. UU.|Hora de implementación (0,25 USD/h) + Unidad de escalado de VPN S2S (0,261 USD/h) + Unidad de conexión de VPN S2S (0,05 USD/h) = 0,561 USD/h|Emparejamiento de VNet (saliente) (0,01 USD/GB) + Salida estándar Zona 1 (0,087 USD/GB) = 0,097 USD/GB|
| 1'|Transferencia de datos desde una rama VPN de sitio a sitio a una red virtual de radio mediante un centro de vWAN Estándar en Este de EE. UU.|Hora de implementación (0,25 USD/h) + Unidad de escalado de VPN S2S (0,261 USD/h) + Unidad de conexión de VPN S2S (0,05 USD/h) = 0,561 USD/h|Emparejamiento de VNet (entrada) (0,01 USD/GB) = 0,01 USD/GB|
|2|Transferencia de datos desde una rama VPN de sitio a sitio mediante un centro de vWAN Estándar a otra rama VPN de sitio a sitio en Este de EE. UU.|Hora de implementación (0,25 USD/h) + Unidad de escalado de VPN S2S (0,261 USD/h) + Unidad de conexión de VPN S2S (2x0,05 USD/h) = 0,611 USD/h|Salida Estándar de Zona 1 (0,087 USD/GB) = 0,087 USD/GB|
|2'|Transferencia de datos desde una rama VPN de sitio a sitio mediante un centro de vWAN Estándar al centro de datos o la sede central conectados con ExpressRoute en Este de EE. UU.|Hora de implementación (0,25 USD/h) + Unidad de escalado de VPN S2S (0,261 USD/h) + Unidad de conexión de VPN S2S (0,05 USD/h) + Unidad de escalado de ExpressRoute (0,42 USD/h) + Unidad de conexión de ExpressRoute (0,05 USD/h) = 1,03 USD/hora|Salida medida de ExpressRoute de la Zona 1 (0,025 USD/GB) = 0,025 USD/GB|
|3|Transferencia de datos desde una rama VPN de sitio a sitio mediante un centro de vWAN Estándar al centro de datos o la sede central conectados con ExpressRoute en Este de EE. UU.|Hora de implementación (0,25 USD/h) + Unidad de escalado de VPN S2S (0,261 USD/h) + Unidad de conexión de VPN S2S (0,05 USD/h) + Unidad de escalado de ExpressRoute (0,42 USD/h) + Unidad de conexión de ExpressRoute (0,05 USD/h) = 1,03 USD/hora|Salida medida de ExpressRoute de la Zona 1 (0,025 USD/GB) = 0,025 USD/GB|
|4|Transferencia de datos desde una red virtual de radio al centro de datos o la sede central conectados con ExpressRoute mediante el centro de vWAN Estándar en Este de EE. UU.|Hora de implementación (0,25 USD/h) + Unidad de escalado de ExpressRoute (0,42 USD/h) + Unidad de conexión de ExpressRoute (0,05 USD/h) = 0,72 USD/hora|Emparejamiento de VNet (saliente) (0,01 USD/GB) + Salida medida de ExpressRoute de la Zona 1 (0,025 USD/GB) = 0,035 USD/GB|
|4'|Transferencia de datos desde el centro de datos o la sede central conectados con ExpressRoute a una red virtual de radio mediante el centro de vWAN Estándar en Este de EE. UU.|Hora de implementación (0,25 USD/h) + Unidad de escalado de ExpressRoute (0,42 USD/h) + Unidad de conexión de ExpressRoute (0,05 USD/h) = 0,72 USD/hora|Emparejamiento de VNet (entrada) (0,01 USD/GB) = 0,01 USD/GB|
|4"|Transferencia de datos desde el centro de datos o la sede central conectados con ExpressRoute a una red virtual de radio remota mediante el centro de vWAN Estándar en Europa|Hora de implementación (2x0,25 USD/h) + Unidad de escalado de ExpressRoute (0,42 USD/h) + Unidad de conexión de ExpressRoute (0,05 USD/h) = 0,97 USD/hora|Emparejamiento de VNet (entrada) (0,01 USD/GB) + procesamiento de datos del centro (Europa) (0,02 USD/GB) + transferencia de datos entre regiones (Este de EE. UU. a Europa) (0,05 USD/GB) = 0,08 USD/GB|
|5|Transferencia de datos desde una red virtual de radio a otra red virtual de radio mediante un centro de vWAN Estándar en Este de EE. UU.|Hora de implementación (0,25 USD/h) = 0,25 USD/h|Emparejamiento de VNet (saliente + entrante) (2x0,01 USD/GB) + procesamiento de datos del centro (0,02 USD/GB) = 0, 04 USD/GB|
|6|Transferencia de datos desde una red virtual de radio conectada a un centro en Este de EE. UU. a otra red virtual de radio en Europa (otra región) que esté conectada a un centro en Europa|Hora de implementación (2x0,25 USD/h) = 0,50 USD/h|Emparejamiento de VNet (salida + entrada) (2x0,01 USD/GB) + procesamiento de datos del centro (2x0,02 USD/GB) + transferencia de datos entre regiones (Este de EE. UU. a Europa) (0,05 USD/GB) = 0, 11 USD/GB|
|7|Transferencia de datos desde una red virtual de radio a una VPN de usuario (punto a sitio) mediante un centro de vWAN Estándar en Europa|Hora de implementación (0,25 USD/h) + Unidad de escalado de VPN P2S (0,261 USD/h) + Unidad de conexión de VPN P2S (0,0125 USD/h) = 0,524 USD/h|Emparejamiento de VNet (saliente) (0,01 USD/GB) + Salida estándar Zona 1 (0,087 USD/GB) = 0,097 USD/GB|
|7'|Transferencia de datos desde una VPN de usuario (punto a sitio) a una red virtual de radio mediante un centro de vWAN Estándar en Europa|Hora de implementación (0,25 USD/h) + Unidad de escalado de VPN P2S (0,261 USD/h) + Unidad de conexión de VPN P2S (0,0125 USD/h) = 0,524 USD/h|Emparejamiento de VNet (entrada) (0,01 USD/GB) = 0,01 USD/GB|
|8|Transferencia de datos desde una rama SD-WAN mediante una aplicación virtual de red en un centro en Singapur a otra rama SD-WAN en la misma región|Hora de implementación del centro de vWAN Estándar (0,25 USD/h) + Unidad de infraestructura de NVA (0,25 USD/h) = 0,50 USD/hora<br> *Se pueden aplicar cargos adicionales por licencias de terceros|Salida Estándar de Zona 2 (0,12 USD/GB) = 0,12 USD/GB|

## <a name="common-topology-scenarios"></a><a name="topologies"></a>Escenarios comunes de topología

### <a name="microsoft-global-backbone-wan"></a><a name="global"></a>WAN troncal global de Microsoft

En este escenario, creará centros regionales automáticos totalmente mallados globalmente, que sirven como red troncal de Microsoft para la conectividad del tráfico globalmente. La conectividad típica implica puntos de conexión como ramas (VPN de sitio a sitio o SD-WAN), usuarios remotos (VPN de punto a sitio) y circuitos privados (ExpressRoute). Esto se basa en la red troncal de Microsoft para transportar el tráfico globalmente.

**Diagrama del escenario 1: WAN troncal global de Microsoft**

:::image type="content" source="./media/pricing-concepts/global-backbone.png" alt-text="Diagrama de la WAN troncal global de Microsoft." lightbox="./media/pricing-concepts/global-backbone.png":::

#### <a name="alternatives"></a>Alternativas

* También puede optar por tener un centro de vWAN protegido (incluye firewall) para convertirse en un punto central de las necesidades de enrutamiento y seguridad de cada región global.

### <a name="software-defined-wide-area-network-sd-wan"></a><a name="sdwan"></a>Red de área extensa definida por software (SD-WAN)

En este escenario, las tiendas que se mueven a la tecnología SD-WAN usan centros de vWAN para la terminación automatizada del almacén para acceder a los recursos de Azure y de vuelta en el entorno local (Centro de datos). Las tiendas se conectan mediante túneles VPN para enviar tráfico de forma segura a través de Internet mediante el centro al centro de datos local o para acceder a aplicaciones compartidas en Azure.

**Diagrama del escenario 2: red de área extensa definida por software (SD-WAN)**

:::image type="content" source="./media/pricing-concepts/sd-wan.png" alt-text="Diagrama de SD-WAN." lightbox="./media/pricing-concepts/sd-wan.png":::

#### <a name="alternatives"></a>Alternativas

* Puede elegir usar una aplicación virtual de red de terceros en el centro y conectarla a las tiendas y centros comerciales.

* También puede optar por tener un centro de vWAN protegido (incluye firewall) para convertirse en un punto central de las necesidades de enrutamiento y seguridad.

### <a name="remote-user-connectivity"></a><a name="remote"></a>Conectividad de usuarios remotos

En este escenario, las sesiones de usuario remoto finalizan en centros de vWAN. Pueden ser usuarios remotos o sesiones de Azure Virtual Desktop desde redes virtuales. Actualmente, se admiten 100 000 usuarios en cada centro.

En el diagrama siguiente, se muestra la VPN de punto a sitio mediante conexiones de red virtual para el tráfico cifrado a través de túneles entre las redes virtuales de radio y el centro de vWAN. También puede elegir tener conexiones de emparejamiento de red virtual entre diferentes redes virtuales de radio para la conectividad directa. Por ejemplo, entre las redes virtuales de radio compartida y de VDI.

**Diagrama del escenario 3: conectividad de usuarios remotos**

:::image type="content" source="./media/pricing-concepts/remote.png" alt-text="Diagrama de la conectividad de usuarios remotos." lightbox="./media/pricing-concepts/remote.png":::

## <a name="data-flow-calculations"></a>Cálculos de flujo de datos

> [!IMPORTANT]
> Los precios que se muestran en este artículo son solo para fines de ejemplo y están sujetos a cambios. Consulte la página [Precios de Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/) para obtener la información más reciente sobre los precios.
>

### <a name="microsoft-global-backbone-wan-calculation"></a>Cálculo de WAN troncal global de Microsoft

En este escenario, se supone que fluyen un total de 8 TB de datos en la red global mediante los centros de vWAN, como se muestra en el diagrama siguiente. Los costos totales de transferencia de datos son de 600 USD para esto (suma de todos los costos de flujo de datos del diagrama siguiente, suponiendo los cargos por uso de ExpressRoute) y los costos totales del concentrador (3 unidades de escalado + 3 unidades de conexión (ER) + 3 implementaciones de centro) se cifran en 1534 USD.

**Diagrama del escenario 1: cálculo de WAN troncal global de Microsoft**

:::image type="content" source="./media/pricing-concepts/global-backbone-pricing.png" alt-text="Diagrama de precios de la red troncal de Microsoft." lightbox="./media/pricing-concepts/global-backbone-pricing.png":::

| Value | Cálculo |
| --- | --- |
|Centro de la VPN S2S de Singapur |(1 unidad de escalado de VPN S2S (0,361 USD/h) + 1 unidad de conexión (0,05 USD/h)) x 730 horas = 300 USD al mes|
|Centro de ExpressRoute en EE. UU. E |(1 unidad de escalado de ER (0,42 USD/h) + 1 unidad de conexión (0,05 USD/h)) x 730 horas = 343 USD al mes|
|Centro de ExpressRoute en Europa|(1 unidad de escalado de ER (0,42 USD/h) + 1 unidad de conexión (0,05 USD/h)) x 730 horas = 343 USD al mes|
|Costo de implementación del centro Estándar |3 centros x 730 horas x 0,25 USD/h = 548 USD al mes|
|**Total**|**1534 USD** al mes |

### <a name="software-defined-wide-area-network-sd-wan-calculation"></a>Cálculo para la red de área extensa definida por software (SD-WAN)

En este escenario, se supone que fluyen un total de 12 TB de datos en el centro de vWAN, como se muestra en el diagrama siguiente en la región Este de EE. UU. Los costos totales de transferencia de datos se cifran en 434 USD (suma de todos los costos de flujo de datos que se muestran a continuación; incluye los cargos de procesamiento del centro, el emparejamiento, el ancho de banda y los costos de transferencia de datos de ER de uso medido) y los costos totales del centro (2 unidades de escalado + 3 unidades de conexión (2 S2S, 1 ER) + 1 implementación de centro) suman hasta 863 USD.

**Diagrama del escenario 2: cálculo para la red de área extensa definida por software (SD-WAN)**

:::image type="content" source="./media/pricing-concepts/sd-wan-pricing.png" alt-text="Diagrama de precios de SD-WAN." lightbox="./media/pricing-concepts/sd-wan-pricing.png":::

| Value | Cálculo |
| --- | --- |
|VPN S2S (ramas) |(1 unidad de escalado de VPN S2S (0,361 USD/h) + 2 unidades de conexión (2x0,05 USD/h)) x 730 horas = 337 USD al mes|
|Centro de ExpressRoute (sede central) |(1 unidad de escalado de ER (0,42 USD/h) + 1 unidad de conexión (0,05 USD/h)) x 730 horas = 343 USD al mes|
|Costo de implementación del centro Estándar|1 centro x 730 horas x 0,25 USD/h = 183 USD al mes|
|**Total**|**863 USD** al mes|

### <a name="remote-user-connectivity-calculation"></a>Cálculo de la conectividad de usuarios remotos

En este escenario, se supone que hay un total de 15 TB de datos que fluyen en la red en la región Este de EE. UU. Los costos totales de transferencia de datos se cifran en 405 USD (incluidos los cargos de procesamiento del centro, el emparejamiento, el ancho de banda y los cargos de transferencia de datos de ER de uso medido) y los costos totales del centro (2 unidades de escalado + 2 unidades de conexión + 2 implementaciones de centro) se cifran en 708 USD.

**Diagrama del escenario 3: cálculo de la conectividad de usuarios remotos**
    
:::image type="content" source="./media/pricing-concepts/remote-pricing.png" alt-text="Diagrama de precios para usuarios remotos." lightbox="./media/pricing-concepts/remote-pricing.png":::

| Value | Cálculo |
| --- | --- |
|Centro de ExpressRoute (sede central) |(1 unidad de escalado de ER (0,42 USD/h) + 1 unidad de conexión (0,05 USD/h)) x 730 horas = 343 USD al mes |
|Costo de implementación del centro Estándar| 2 centros x 730 horas x 0,25 USD/h = 365 USD al mes |
|**Total** |**708 USD** al mes |

## <a name="pricing-faq"></a>P+F de precios

> [!IMPORTANT]
> Los precios que se muestran en este artículo son solo para fines de ejemplo y están sujetos a cambios. Consulte la página [Precios de Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/) para obtener la información más reciente sobre los precios.
>

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>¿Qué es una unidad de escalado?

Una **unidad de escalado** proporciona la unidad de la capacidad agregada de sitio a sitio (S2S), punto a sitio (P2S) y ExpressRoute (ER) en un centro virtual. Por ejemplo:

* **Una unidad de escalado de VPN S2S** implica una capacidad total de la puerta de enlace de VPN de 500 Mbps (se implementan dos instancias para lograr resistencia) en un centro virtual que cuesta 0,361 USD por hora.

* **Una unidad de escalado de ER** implica un total de puerta de enlace de ER de 2 Gbps en un centro virtual que cuesta 0,42 USD por hora.

* **Cinco unidades de escalado de ER** implicarían un total de puerta de enlace de ER de 10 Gbps en una red virtual del centro virtual con un precio de 0,42 USD*5 por hora. ExpressRoute se incrementa en 0,25 USD por hora de la unidad de escalado 6 a la 10.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit">¿Qué es una unidad de conexión?</a>

Una **unidad de conexión** se aplica a cualquier punto de conexión local o que no sea de Microsoft que se conecte a las puertas de enlace de Azure. Para la VPN de sitio a sitio, el valor implica ramas. Para la VPN de usuario (de punto a sitio), el valor implica usuarios remotos. Para ExpressRoute, el valor implica conexiones del circuito ExpressRoute.<br>Por ejemplo:

* Una conexión de rama que se conecta a la VPN de Azure en un centro virtual cuesta de 0,05 USD por hora. Por lo tanto, 100 conexiones de rama que se conectan a un centro virtual de Azure costarían 0,05 USD*100 por hora.

* Dos conexiones del circuito ExpressRoute que se conectan a un centro virtual costarían 0,05 USD*2 por hora.

* Tres conexiones de usuario remoto que se conectan a la puerta de enlace P2S del centro virtual de Azure costarían 0,03 USD*3 por hora.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>¿Cómo se calculan los cargos de transferencia de datos?

* No se cobra ningún tráfico entrante de Azure. El tráfico que sale de Azure (mediante VPN, ExpressRoute o conexiones VPN de usuario de punto a sitio) está sujeto a los [cargos de transferencia de datos estándar de Azure](https://azure.microsoft.com/pricing/details/bandwidth/) o, en el caso de ExpressRoute, a los [Precios de ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

* Los cargos de emparejamiento son aplicables cuando una red virtual conectada a un centro de vWAN envía o recibe datos. Para más información, consulte [Precios de redes virtuales](https://azure.microsoft.com/pricing/details/virtual-network/).

* En el caso de los cargos de transferencia de datos entre un centro de Virtual WAN y un centro de Virtual WAN remoto o una red virtual en una región distinta a la del centro de origen, se aplican cargos por transferencia de datos para el tráfico que sale de un centro. Ejemplo: Al tráfico que sale de un centro de Este de EE. UU. se le cobrarán 0,02 USD por GB al centro de Oeste de EE. UU. No se aplica ningún cargo por el tráfico que entra en el centro de Oeste de EE. UU. Todo el tráfico de centro a centro está sujeto a cargos interregionales (intra/intercontinentales), [Cargos de transferencia de datos de Azure](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>¿Cuál es la diferencia entre una tarifa de centro estándar y una tarifa de procesamiento de centro estándar?

Virtual WAN se ofrece en dos variedades:

* Una **WAN virtual básica**, donde los usuarios pueden implementar varios centros y usar la conectividad VPN de sitio a sitio. La WAN virtual básica no tiene funcionalidades avanzadas, como los centros totalmente en malla, la conectividad de ExpressRoute, la conectividad de VPN de punto a sitio y VPN de usuario, la conectividad transitiva de red virtual a red virtual, la conectividad de tránsito de VPN y ExpressRoute, o Azure Firewall. No hay ninguna cuota de procesamiento de datos o cuota base para los centros en una WAN virtual básica.

* Una **WAN virtual estándar** proporciona funcionalidades avanzadas, como centros totalmente en malla, conectividad de ExpressRoute, VPN de usuario y conectividad VPN de punto a sitio, conectividad transitiva de red virtual a red virtual, conectividad de tránsito de ExpressRoute y VPN, y Azure Firewall. Todo el enrutamiento del centro virtual se proporciona mediante un enrutador que permite varios servicios en un centro virtual. Hay una cuota base para el centro, con un precio de 0,25 USD por hora. También hay un cargo por el procesamiento de datos en el enrutador del centro virtual para la conectividad de tránsito de red virtual a red virtual. El cargo por procesamiento de datos en el enrutador del centro virtual no es aplicable a las transferencias de rama a rama (escenarios 2 y 3) ni a las transferencias de red virtual a rama mediante el mismo centro de vWAN (escenario 1) de este artículo. 

## <a name="next-steps"></a>Pasos siguientes

* Para obtener los precios actuales, consulte [Precios de Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Para más información sobre Virtual WAN, consulte las [preguntas más frecuentes](virtual-wan-faq.md).


