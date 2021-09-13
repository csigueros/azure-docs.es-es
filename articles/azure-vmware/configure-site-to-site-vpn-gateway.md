---
title: Configuración de una VPN de sitio a sitio en vWAN para Azure VMware Solution
description: Obtenga información sobre cómo establecer un túnel de sitio a sitio de VPN (IPsec IKEv1 e IKEv2) en Azure VMware Solution.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 06/30/2021
ms.openlocfilehash: fc2f62549a9a06122b77e0e8864c029cb6af8029
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515231"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>Configuración de una VPN de sitio a sitio en vWAN para Azure VMware Solution

En este artículo, establecerá un túnel VPN (IPsec IKEv1 e IKEv2) de sitio a sitio que termine en el centro de Microsoft Azure Virtual WAN. El centro contiene la puerta de enlace de ExpressRoute de Azure VMware Solution y la puerta de enlace de VPN de sitio a sitio. Conecta un dispositivo VPN local con un punto de conexión de Azure VMware Solution.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagrama que muestra la arquitectura de un túnel VPN de sitio a sitio." border="false":::

## <a name="prerequisites"></a>Requisitos previos
Debe tener una dirección IP de acceso público que termine en un dispositivo VPN local.

## <a name="create-an-azure-virtual-wan"></a>Creación de una instancia de Azure Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-virtual-hub"></a>Crear un centro virtual

Un centro de conectividad virtual es una red virtual que Virtual WAN crea y usa. Es el núcleo de la red Virtual WAN en una región.  Puede contener puertas de enlace de sitio a sitio y ExpressRoute. 

>[!TIP]
>También puede [crear una puerta de enlace en un centro de conectividad ya existente](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub), editándolo.


[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-vpn-gateway"></a>Creación de una puerta de enlace de VPN 

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]


## <a name="create-a-site-to-site-vpn"></a>Crear una VPN de sitio a sitio.

1. En Azure Portal, seleccione la instancia de Virtual WAN que creó anteriormente.

2. En la página **Información general** del centro de conectividad virtual, seleccione **Conectividad** > **VPN (De sitio a sitio)**  > **Crear un nuevo sitio de VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Captura de pantalla de la página de información general del centro virtual, con las opciones VPN (De sitio a sitio) y Crear un nuevo sitio de VPN seleccionadas.":::  
 
3. En la pestaña **Aspectos básicos**, complete los campos necesarios. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Captura de pantalla en la que se muestra la página Crear un sitio VPN con la pestaña Aspectos básicos abierta." lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png":::

   * **Región**: anteriormente se conocía como ubicación. Se trata de la ubicación en la que quiere crear este recurso de sitio.
   
   * **Nombre**: el nombre que desee usar para hacer referencia a su sitio local.
   
   * **Proveedor del dispositivo**: el nombre del proveedor del dispositivo VPN (por ejemplo: Citrix, Cisco o Barracuda). Esto ayuda al equipo de Azure a comprender mejor su entorno para agregar más posibilidades de optimización en el futuro, así como para ayudarle a solucionar problemas.

   * **Espacio de direcciones privadas**: es el espacio de direcciones IP CIDR que se encuentra en el sitio local. El tráfico destinado a este espacio de direcciones se enruta al sitio local. El bloque CIDR solo es necesario si no se habilita [BGP](../vpn-gateway/bgp-howto.md) para el sitio.
    
   >[!NOTE]
   >Si edita el espacio de direcciones después de crear el sitio (por ejemplo, agrega un espacio de direcciones adicional), puede tardar entre 8 y 10 minutos en actualizar las rutas efectivas mientras se recrean los componentes.


1. Seleccione **Vínculos** para agregar información sobre los vínculos físicos en la rama. Si tiene un dispositivo CPE de asociado de Virtual WAN, consulte con su asociado para ver si esta información se intercambia con Azure como parte de la configuración de carga de información de la sucursal desde sus sistemas.

   La especificación de los nombres del proveedor y el vínculo le permite distinguir entre cualquier número de puertas de enlace que puedan crearse en algún momento como parte del centro.  El [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) y el número de sistema autónomo (ASN) deben ser únicos dentro de su organización. BGP garantiza que tanto Azure VMware Solution como los servidores locales anuncian sus rutas a través del túnel. Si esta opción está deshabilitada, las subredes que tienen que anunciarse deberán mantenerse manualmente. Si faltan subredes, HCX no podrá formar la malla del servicio. 
 
   >[!IMPORTANT]
   >De forma predeterminada, Azure asigna una dirección IP privada desde el intervalo del prefijo GatewaySubnet automáticamente como dirección IP de BGP de Azure en la instancia de Azure VPN Gateway. La dirección de BGP APIPA de Azure es necesaria cuando los dispositivos VPN locales usan una dirección APIPA (169.254.0.1 a 169.254.255.254) como dirección IP de BGP. Azure VPN Gateway elegirá la dirección APIPA personalizada si el recurso de puerta de enlace de red local correspondiente (red local) tiene una dirección APIPA como dirección IP del par BGP. Si la puerta de enlace de red local usa una dirección IP normal (no APIPA), Azure VPN Gateway revertirá a la dirección IP privada del intervalo GatewaySubnet.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Captura de pantalla en la que se muestra la página Crear un sitio VPN con la pestaña Vínculos abierta." lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-links.png":::

1. Seleccione **Revisar + crear**. 

1. Vaya hasta el centro de conectividad virtual que quiera y anule la selección de la **Asociación de concentrador** para conectar el sitio VPN al centro.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="La captura de pantalla muestra Conectarse a este centro." lightbox="../../includes/media/virtual-wan-tutorial-site-include/connect.png":::   

## <a name="optional-create-policy-based-vpn-site-to-site-tunnels"></a>(Opcional) Creación de túneles de sitio a sitio de VPN basados en directivas

>[!IMPORTANT]
>Este es un paso opcional y se aplica únicamente a las VPN basadas en directivas. 

Las [configuraciones de VPN basadas en directivas](../virtual-wan/virtual-wan-custom-ipsec-portal.md) requieren que se especifiquen redes locales y de Azure VMware Solution, incluidos los intervalos del centro de conectividad.  Dichos intervalos se usan para especificar el dominio de cifrado del punto de conexión local del túnel VPN basado en directivas.  El lado de Azure VMware Solution solo requiere que se habilite el indicador del selector de tráfico basado en directivas. 

1. En Azure Portal, vaya al sitio del centro de Virtual WAN y, en **Conectividad**, seleccione **VPN (de sitio a sitio)** .

2. Seleccione el sitio VPN para el que quiere configurar una directiva de IPsec personalizada.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png" alt-text="Captura de pantalla en la que se muestran los sitios VPN existentes para configurar directivas de IPsec personalizadas." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png":::

3. Seleccione el nombre del sitio VPN, el botón **Más** (…) situado en el extremo derecho y, luego, **Editar conexión VPN**.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png" alt-text="Captura de pantalla en la que se muestra el menú contextual de un sitio VPN existente." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png":::

   - Para el protocolo de seguridad de Internet (IPSec), seleccione **Personalizado**.

   - Para la opción Usar el selector de tráfico basado en directivas, seleccione **Habilitar**.

   - Especifique los detalles de **Fase 1 IKE** y **Fase 2 IKE (IPsec)** . 

4. Cambie la configuración de IPsec de predeterminada a personalizada y personalice la directiva de IPsec. Después, seleccione **Guardar**.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png" alt-text="Captura de pantalla en la que se muestran los sitios VPN existentes." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png":::

   Los selectores de tráfico o las subredes que forman parte del dominio de cifrado basado en directivas deben ser los siguientes:
    
   - Centro de centro de conectividad de Virtual WAN `/24`

   - Nube privada de Azure VMware Solution `/22`

   - Red virtual de Azure conectada (si existe)

## <a name="connect-your-vpn-site-to-the-hub"></a>Conexión del sitio de VPN con el centro

1. Elija el nombre del sitio VPN y, luego, seleccione **Conectar sitios de VPN**. 

1. En el campo **Clave precompartida**, escriba la clave definida previamente para el punto de conexión local. 

   >[!TIP]
   >Si no tiene una clave ya definida, puede dejar este campo en blanco. Se generará una clave de forma automática. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Captura de pantalla que muestra el panel Sitios conectados del centro de conectividad virtual listo para una clave precompartida y la configuración asociada"::: 

1. Si va a implementar un firewall en el centro de conectividad y es el próximo salto, establezca la opción **Propagar la ruta predeterminada** en **Habilitar**. 

   Cuando está habilitada, el centro de conectividad de Virtual WAN se propaga a una conexión solo si dicho centro ya ha aprendido la ruta predeterminada al implementar un firewall en el centro de conectividad o si otro sitio conectado tiene habilitada la tunelización forzada. La ruta predeterminada no se origina en el centro de conectividad de Virtual WAN.  

1. Seleccione **Conectar**. Al cabo de unos minutos, en el sitio aparecerá el estado de la conexión y de la conectividad.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Captura de pantalla en la que se muestra una conexión de sitio a sitio y el estado de conectividad" lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

   **Estado de la conexión**: el estado del recurso de Azure de la conexión que conecta el sitio de VPN con la puerta de enlace de VPN del centro de conectividad de Azure. Una vez que esta operación de plano de control se realiza correctamente, la puerta de enlace de VPN de Azure y el dispositivo VPN local establecen la conectividad.

   **Estado de conectividad**: el estado de conectividad real (ruta de acceso a datos) entre la puerta de enlace de VPN de Azure en el centro de conectividad y el sitio VPN. Puede mostrar cualquiera de los siguientes estados:

    * **Desconocido**: suele observarse si los sistemas de back-end están trabajando en una transición a otro estado.
    * **Conectando**: La puerta de enlace de VPN de Azure está intentando ponerse en contacto con el sitio VPN local.
    * **Conectado**: se ha establecido la conectividad entre la puerta de enlace de VPN de Azure y el sitio VPN local.
    * **Desconectado**: normalmente se muestra si se desconecta la conexión por cualquier motivo (ya sea local o en Azure).



1. Descargue el archivo de configuración de VPN para aplicarlo al punto de conexión local.  
   
   1. Cerca de la parte superior de la página VPN (de sitio a sitio), seleccione **Descargar la configuración de VPN**. Azure crea una cuenta de almacenamiento en el grupo de recursos "microsoft-network-\[location\]", donde "location" es la ubicación de la red de área extensa. Una vez que haya aplicado la configuración a los dispositivos VPN, puede eliminar esta cuenta de almacenamiento.

   1. Después de crear el archivo, seleccione el vínculo para descargarlo. 

   1. Aplique la configuración al dispositivo VPN local.

   Para obtener más información sobre el archivo de configuración, vea la sección [Archivo de configuración del dispositivo VPN](../virtual-wan/virtual-wan-site-to-site-portal.md#config-file).

1. Aplique una revisión de ExpressRoute de Azure VMware Solution en el centro de Virtual WAN. 

   >[!IMPORTANT]
   >En primer lugar, debe crear una nube privada para poder aplicar revisiones a la plataforma. 


   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

1. Vincule Azure VMware Solution y la puerta de enlace de VPN en el centro de Virtual WAN. Usará la clave de autorización y el identificador de ExpressRoute (URI del circuito del mismo nivel) del paso anterior.

   1. Seleccione la puerta de enlace de ExpressRoute y, a continuación, seleccione **Canjear la clave de autorización**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Captura de pantalla que muestra la página de ExpressRoute de la nube privada con la opción Canjear la clave de autorización seleccionada.":::

   1. Pegue la clave de autorización en el campo **Clave de autorización**.

   1. Pegue el identificador de ExpressRoute en el campo **URI de circuito del mismo nivel**. 

   1. Seleccione **Asocie automáticamente este circuito de ExpressRoute con el centro**. 

   1. Seleccione **Agregar** para establecer el vínculo. 

1. Para probar la conexión, [cree un segmento NSX-T](./tutorial-nsx-t-network-segment.md) y aprovisione una máquina virtual en la red. Haga ping a los puntos de conexión locales y de Azure VMware Solution.

   >[!NOTE]
   >Espere unos 5 minutos antes de probar la conectividad de un cliente que se encuentre detrás del circuito ExpressRoute, por ejemplo, una máquina virtual en la red virtual que haya creado anteriormente.
