---
title: Configuración de reglas NAT de VPN para una puerta de enlace
titleSuffix: Azure Virtual WAN
description: Aprenda a configurar las reglas NAT para una puerta de enlace de VPN VWAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6613952b5d7811c187123963a6e611602aabb5dc
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113127697"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway"></a>Configuración de reglas NAT para la puerta de enlace de VPN de Virtual WAN

Puede configurar una puerta de enlace de VPN de Virtual WAN con reglas NAT estáticas de uno a uno. Una regla NAT proporciona un mecanismo para configurar la traducción uno a uno de las direcciones IP. NAT se puede usar para interconectar dos redes IP que tienen direcciones IP incompatibles o superpuestas. Un escenario típico son las ramas con direcciones IP superpuestas que quieren acceder a los recursos de la red virtual de Azure.

Esta configuración usa una tabla de flujos para enrutar el tráfico de una dirección IP externa (host) a una dirección IP interna asociada a un punto de conexión dentro de una red virtual (máquina virtual, equipo, contenedor, etc.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagrama que muestra la arquitectura.":::
   
Para usar NAT, los dispositivos VPN deben usar selectores de tráfico universales (comodín). Los selectores de tráfico basados en directivas (estrechas) no se admiten junto con la configuración de NAT.

## <a name="configure-nat-rules"></a><a name="rules"></a>Configuración de reglas NAT

Puede configurar y ver las reglas NAT en la configuración de la puerta de enlace de VPN en cualquier momento.

> [!NOTE]
> No se admite NAT de sitio a sitio con conexiones VPN de sitio a sitio donde se usan selectores de tráfico basados en directivas.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Captura de pantalla en la que se muestra cómo editar reglas."lightbox="./media/nat-rules-vpn-gateway/edit-rules.png":::
1. Vaya al centro virtual.
1. Seleccione **VPN (de sitio a sitio)**
1. Seleccione **NAT rules (Edit)** (Reglas NAT [editar]).
1. En la página **Edit NAT Rule** (Editar regla NAT), puede **Agregar/Editar/Eliminar** una regla NAT con los siguientes valores:

   * **Nombre:** Nombre único para la regla NAT.
   * **Tipo:** Estático. Una regla NAT estática de uno a uno establece una relación de uno a uno entre una dirección interna y una dirección externa.
   * **Modo:** IngressSnat o EgressSnat.  
      * El modo IngressSnat (también conocido como NAT de origen de entrada) es aplicable al tráfico que entra en la puerta de enlace de VPN de sitio a sitio del centro de Azure. 
      * El modo EgressSnat (también conocido como NAT de origen de salida) es aplicable al tráfico que sale de la puerta de enlace de VPN de sitio a sitio del centro de Azure. 
   * **InternalMapping:** Intervalo de prefijos de dirección de las direcciones IP de origen en la red interna que se asignará a un conjunto de direcciones IP externas. En otras palabras, el intervalo de prefijos de dirección antes de NAT.
   * **ExternalMapping:** Intervalo de prefijos de dirección de las direcciones IP de destino en la red externa a la que se asignarán las direcciones IP de origen. En otras palabras, el intervalo de prefijos de dirección después de NAT.
   * **Link Connection** (Conexión de vínculo): recurso de conexión que conecta virtualmente un sitio VPN a la puerta de enlace de VPN de sitio a sitio del centro de conectividad de Azure Virtual WAN.
 
> [!NOTE]
> Si desea que la puerta de enlace de VPN de sitio a sitio anuncie los prefijos de direcciones traducidos (**ExternalMapping**) a través de BGP, haga clic en el botón **Enable BGP Translation** (Habilitar traducción BGP), con lo cual el entorno local aprenderá automáticamente el intervalo posterior a NAT de las reglas de salida y Azure (el centro de conectividad de Virtual WAN, las redes virtuales conectadas y las ramas de VPN y ExpressRoute) aprenderá automáticamente el intervalo posterior a NAT de las reglas de entrada. Tenga en cuenta que la opción **Enable Bgp Translation** (Habilitar traducción BGP) se aplica a todas las reglas NAT de la puerta de enlace de VPN de sitio a sitio del centro de conectividad de Virtual WAN. 

## <a name="example-configurations"></a><a name="examples"></a>Configuraciones de ejemplo

### <a name="ingress-snat-bgp-enabled-vpn-site"></a>SNAT de entrada (sitio VPN habilitado para BGP)

**Las reglas NAT de entrada** se aplican a los paquetes que entran en Azure a través de la puerta de enlace de VPN Gateway de sitio a sitio de Virtual WAN. En este escenario, puede conectar dos ramas de VPN de sitio a sitio a Azure. VPN Site 1 se conecta a través del vínculo A y VPN Site 2 se conecta a través del vínculo B. Cada sitio tiene el mismo espacio de direcciones 10.30.0.0/24.

En este ejemplo, se traducirá la dirección de red de site1 a 127.30.0.0.0/24. Las redes virtuales de radios y las ramas de Virtual WAN aprenden automáticamente este espacio de direcciones posterior a NAT. 

En el diagrama siguiente se muestra el resultado final previsto:

:::image type="content" source="./media/nat-rules-vpn-gateway/diagram-bgp.png" alt-text="Diagrama que muestra NAT en modo de entrada para sitios habilitados para BGP.":::

1. Especifique una regla NAT.

   Especifique una regla NAT para asegurarse de que la instancia de VPN Gateway de sitio a sitio puede distinguir entre las dos ramas con espacios de direcciones superpuestos (por ejemplo, 10.30.0.0/24). En este ejemplo, nos centramos en el vínculo Link A para VPN Site 1.

   La siguiente regla NAT se puede configurar y asociar al vínculo Link A. Dado que se trata de una regla NAT estática, los espacios de direcciones de **InternalMapping** y **ExternalMapping** contienen el mismo número de direcciones IP.

   * **Nombre:** ingressRule01
   * **Tipo:** Estático.
   * **Modo:** IngressSnat.
   * **InternalMapping:** 10.30.0.0/24
   * **ExternalMapping:** 172.30.0.0/24
   * **Conexión de vínculo:** Link A

 2.  Cambie **BGP Route Translation** (Traducción de rutas BGP) a "Habilitar".

       :::image type="content" source="./media/nat-rules-vpn-gateway/enable-bgp.png" alt-text="Captura de pantalla que muestra cómo habilitar la traducción BGP.":::


3. Asegúrese de que la instancia de VPN Gateway de sitio a sitio se pueda emparejar con el par BGP local.

      En este ejemplo, la **regla NAT de entrada** deberá traducir 10.30.0.132 a 127.30.0.132. Para ello, haga clic en "Editar sitio VPN" para configurar la dirección BGP del vínculo Link A del sitio de VPN para que refleje esta dirección del par BGP traducido (127.30.0.132). 

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site-bgp.png" alt-text="Captura de pantalla que muestra cómo cambiar la dirección IP del emparejamiento BGP."lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

 

### <a name="considerations-if-the-vpn-site-connects-via-bgp"></a><a name="considerations"></a>Consideraciones a tener en cuenta si el sitio VPN se conecta a través de BGP
* El tamaño de la subred para la asignación interna y externa debe ser el mismo para la regla NAT estática de uno a uno.
* Si la traducción **BGP** está habilitada, la instancia de VPN Gateway de sitio a sitio anunciará automáticamente la **asignación externa** de **reglas NAT de salida** en el entorno local, así como la **asignación externa** de **reglas NAT de entrada** en Azure (Virtual WAN Hub, redes virtuales de radio conectadas, instancias de VPN o ExpresRoute conectadas). Si la **traducción BGP** está deshabilitada, las rutas traducidas no se anuncian automáticamente en el entorno local. Por lo tanto, el altavoz BGP local debe configurarse para anunciar el intervalo posterior a NAT (**ExternalMapping**) de reglas **NAT de entrada** asociadas a esa conexión de vínculo del sitio VPN. De forma similar, se debe aplicar una ruta al intervalo posterior a NAT (**ExternalMapping**) de **reglas NAT de salida** en el dispositivo local.
* La puerta de enlace de VPN de sitio a sitio traducirá automáticamente la dirección IP del BGP del mismo nivel del entorno local **si** está contenida en la **asignación interna** de una **regla NAT de entrada**. Como resultado, la **dirección del BGP de la conexión de vínculo** del sitio VPN debe reflejar la dirección traducida por NAT (parte de la asignación externa). 

    Por ejemplo, si la dirección IP del BGP local es 10.30.0.133 y hay una regla **NAT de entrada** que traduce 10.30.0.0/24 a 127.30.0.0/24, la **dirección del BGP de la conexión de vínculo** del sitio VPN debe configurarse para que sea la dirección traducida (127.30.0.133).

 
### <a name="ingress-snat-vpn-site-with-statically-configured-routes"></a>SNAT de entrada (sitio VPN con rutas configuradas estáticamente)

**Las reglas NAT de entrada** se aplican a los paquetes que entran en Azure a través de la puerta de enlace de VPN Gateway de sitio a sitio de Virtual WAN. En este escenario, puede conectar dos ramas de VPN de sitio a sitio a Azure. VPN Site 1 se conecta a través del vínculo A y VPN Site 2 se conecta a través del vínculo B. Cada sitio tiene el mismo espacio de direcciones 10.30.0.0/24.

En este ejemplo, vamos a traducir la dirección de red del sitio VPN 1 a 127.30.0.0.0/24. Sin embargo, dado que el sitio VPN no está conectado a la puerta de enlace de VPN de sitio a sitio mediante el BGP, los pasos de configuración son ligeramente diferentes a los del ejemplo habilitado para BGP. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram-static.png" alt-text="Captura de pantalla que muestra configuraciones de diagrama para sitios VPN que usan enrutamiento estático.":::


1. Especifique una regla NAT.

    Especifique una regla NAT para asegurarse de que la puerta de enlace de VPN de sitio a sitio puede distinguir entre las dos ramas con el mismo espacio de direcciones: 10.30.0.0/24. En este ejemplo, nos centramos en el vínculo Link A para VPN Site 1.

    La siguiente regla NAT se puede configurar y asociar al vínculo Link A del sitio VPN 1. Dado que se trata de una regla NAT estática, los espacios de direcciones de **InternalMapping** y **ExternalMapping** contienen el mismo número de direcciones IP.

    * **Nombre**: IngressRule01
    * **Tipo**: Estático.
    * **Modo**: IngressSnat.
    * **InternalMapping**: 10.30.0.0/24
    * **ExternalMapping**: 172.30.0.0/24
    * **Conexión de vínculo**: Link A

2. Edite el campo "Espacio de direcciones privadas" del sitio VPN 1 para asegurarse de que la puerta de enlace de VPN de sitio a sitio aprende el intervalo posterior a NAT (172.30.0.0/24)

   * Navegue hasta el recurso de centro virtual que contiene la instancia de VPN Gateway de sitio a sitio. En la página del centro virtual, en Conectividad, seleccione VPN (sitio a sitio) .

   * Seleccione el sitio VPN que está conectado al centro de conectividad de Virtual WAN mediante el vínculo Link A. Seleccione Editar sitio y la entrada 172.30.0.0/24 como espacio de direcciones privadas del sitio VPN. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/vpn-site-static.png" alt-text="Captura de pantalla que muestra cómo editar el espacio de direcciones privadas de un sitio VPN" lightbox="./media/nat-rules-vpn-gateway/vpn-site-static.png":::

### <a name="considerations-if-vpn-sites-are-statically-configured-not-connected-via-bgp"></a><a name="considerationsnobgp"></a>Consideraciones a tener en cuenta si los sitios VPN están configurados estáticamente (no conectados a través de BGP)
* El tamaño de la subred para la asignación interna y externa debe ser el mismo para la regla NAT estática de uno a uno.
* Edite el sitio VPN en Azure Portal para agregar los prefijos de **ExternalMapping** de las **reglas NAT de entrada** en el campo "Espacio de direcciones privadas". 
* Para las configuraciones que implican **reglas NAT de salida**, es necesario aplicar una directiva de ruta o una ruta estática con **ExternalMapping** de la **regla NAT de salida** en el dispositivo local.

### <a name="packet-flow"></a>Flujo de paquetes

En los ejemplos anteriores, un dispositivo local quiere acceder a un recurso en una red virtual de radios. El flujo de paquetes es el siguiente, con las traducciones NAT en negrita.

1. Se inicia el tráfico desde el entorno local.
   * Dirección IP de origen: **10.30.0.4**
   * Dirección IP de destino: 10.200.0.4
1. El tráfico entra en la puerta de enlace de sitio a sitio, se traduce mediante la regla NAT y se envía a los radios.
   * Dirección IP de origen: **172.30.0.4**
   * Dirección IP de destino: 10.200.0.4
1. Se inicia la respuesta desde los radios.
   * Dirección IP de origen: 10.200.0.4
   * Dirección IP de destino: **172.30.0.4**
1. El tráfico entra en VPN Gateway de sitio a sitio, y la traducción se invierte y se envía al entorno local.
   * Dirección IP de origen: 10.200.0.4
   * Dirección IP de destino: **10.30.0.4**

### <a name="verification-checks"></a>Comprobaciones

En esta sección se muestran las comprobaciones para determinar que la configuración está configurada correctamente. 

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Validar DefaultRouteTable, reglas y rutas

Las ramas de Virtual WAN se asocian al recurso **DefaultRouteTable**, lo que implica que todas las conexiones de rama aprenden las rutas que se rellenan en DefaultRouteTable. Verá la regla NAT con el prefijo traducido en las rutas efectivas del recurso DefaultRouteTable.

En el ejemplo anterior:

* **Prefijo:** 172.30.0.0/24  
* **Tipo de próximo salto:** VPN_S2S_Gateway
* **Próximo salto:** recurso VPN_S2S_Gateway

#### <a name="validate-address-prefixes"></a>Validar prefijos de direcciones

Este ejemplo se aplica a los recursos de redes virtuales que están asociados al recurso DefaultRouteTable.

Las **rutas efectivas** en las tarjetas de interfaz de red (NIC) de cualquier máquina virtual que se encuentre en una red virtual de radios conectada al centro de conectividad de Virtual WAN también deben contener los prefijos de dirección del espacio de direcciones **ExternalMapping** de las **reglas NAT de entrada**. 

El dispositivo local también debe contener rutas para los prefijos contenidos en **ExternalMapping** de las **reglas NAT de salida**. 

####  <a name="common-configuration-patterns"></a>Problemas de configuración comunes 

> [!NOTE]
> No se admite NAT de sitio a sitio con conexiones VPN de sitio a sitio donde se usan selectores de tráfico basados en directivas.

En la tabla siguiente se muestran los patrones de configuración comunes que aparecen al configurar diferentes tipos de reglas NAT en la puerta de enlace de VPN de sitio a sitio.  

| Tipo de sitio VPN | Reglas NAT de entrada | Reglas NAT de salida
|--- |--- | ---|
|Sitio VPN con rutas configuradas estáticamente |Edite el campo "Espacio de direcciones privadas" en el sitio VPN para que contenga el espacio de direcciones **ExternalMapping** de la regla NAT.| Aplique rutas del espacio de direcciones **ExternalMapping** de la regla NAT en el dispositivo local.|
|Sitio VPN (traducción de BGP habilitada)|Coloque la dirección **ExternalMapping** del BGP del mismo nivel en la dirección BGP de la conexión de vínculo del sitio VPN.  | No hay consideraciones especiales a tener en cuenta. |
| Sitio VPN (traducción de BGP habilitada) | Asegúrese de que el altavoz BGP local anuncia los prefijos en el espacio de direcciones **ExternalMapping** de la regla NAT. Coloque también la dirección ExternalMapping del BGP del mismo nivel en la dirección BGP de la conexión de vínculo del sitio VPN.| Aplique rutas del espacio de direcciones **ExternalMapping** de la regla NAT en el dispositivo local.|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las configuraciones de sitio a sitio, consulte [Configuración de una conexión de sitio a sitio de Virtual WAN](virtual-wan-site-to-site-portal.md).
