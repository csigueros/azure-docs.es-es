---
title: Integración de aplicaciones con Azure Virtual Network
description: Integre aplicaciones en Azure App Service con Azure Virtual Network.
author: madsd
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: madsd
ms.openlocfilehash: ced89f28924ef27f90f2f960a11d8269e56cd080
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269964"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integración de su aplicación con una instancia de Azure Virtual Network

En este artículo, se describe la característica de integración con red virtual de Azure App Service y cómo configurarla con aplicaciones en [Azure App Service](./overview.md). Con las [redes virtuales de Azure](../virtual-network/virtual-networks-overview.md) (VNet), puede colocar muchos de los recursos de Azure en una red que se puede enrutar, distinta de Internet. La característica de integración con red virtual permite a las aplicaciones acceder a los recursos de una red virtual o mediante esta. No se permite el acceso privado a las aplicaciones.

Azure App Service adopta dos variantes:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Descubra [cómo habilitar la integración con red virtual](./configure-vnet-integration-enable.md).

## <a name="regional-vnet-integration"></a>Integración con red virtual regional

La integración con red virtual regional admite la conexión a una red virtual de la misma región y no requiere una puerta de enlace. Cuando se utiliza la integración con red virtual regional, la aplicación puede acceder a:

* Recursos de la VNet con la que está integrado.
* Recursos de las VNet emparejadas con la VNet con la que se integra la aplicación, incluidas conexiones de emparejamiento global.
* Recursos de diferentes conexiones de Azure ExpressRoute.
* Servicios protegidos mediante puntos de conexión de servicio.
* Servicios habilitados para puntos de conexión privados.

Si usa la integración con red virtual regional, puede usar las siguientes características de red de Azure:

* **Grupos de seguridad de red (NSG)** : el tráfico saliente se puede bloquear con un grupo de seguridad de red que se encuentre en la subred de integración. Las reglas de entrada no se aplican, ya que la integración con red virtual no se puede usar para proporcionar acceso de entrada a la aplicación.
* **Tablas de enrutamiento (UDR)** : puede colocar una tabla de enrutamiento en la subred de integración para enviar el tráfico de salida donde quiera.

### <a name="how-regional-vnet-integration-works"></a>Funcionamiento de la integración con red virtual regional

Las aplicaciones en App Service se hospedan en roles de trabajo. La integración con red virtual regional lo que hace es montar interfaces virtuales en los roles de trabajo con las direcciones de la subred delegada. Como la dirección de origen está en la VNet, tiene acceso a la mayoría de las cosas que están en esa VNet o a las que se puede acceder a través de ella, como cualquier máquina virtual que estuviera en la VNet. La implementación de redes es diferente de la ejecución de una máquina virtual en la VNet. Este es el motivo por el que algunas características de red todavía no están disponibles para esta característica.

:::image type="content" source="./media/overview-vnet-integration/vnetint-how-regional-works.png" alt-text="Funcionamiento de la integración con red virtual regional":::

Cuando la integración con red virtual regional está habilitada, su aplicación realiza llamadas salientes a través de la red virtual. Las direcciones salientes que se muestran en el portal de propiedades de la aplicación siguen siendo las direcciones usadas por la aplicación. Pero si la llamada saliente es a una máquina virtual o un punto de conexión privado en la red virtual de integración o en la red virtual emparejada, la dirección saliente será una dirección de la subred de integración. La dirección IP privada asignada a una instancia se expone por medio de la variable de entorno ```WEBSITE_PRIVATE_IP```.

Si todo el enrutamiento de tráfico está habilitado, todo el tráfico saliente se envía a la red virtual. Si no está habilitado todo el enrutamiento de tráfico, solo se enviarán a la red virtual el tráfico privado (RFC1918) y los puntos de conexión de servicio configurados en la subred de integración y el tráfico saliente a Internet pasará por los mismos canales normales.

La característica solo admite una interfaz virtual por trabajo. Una interfaz virtual por trabajo se traduce en una integración con red virtual regional por plan de App Service. Todas las aplicaciones del mismo plan de App Service pueden usar la misma integración con red virtual. Si necesita que una aplicación se conecte a otra red virtual, debe crear otro plan de App Service. La interfaz virtual utilizada no es un recurso al que los clientes tengan acceso directo.

Dada la forma en que funciona esta tecnología, el tráfico que se usa con la integración con red virtual no se muestra en los registros de flujos de NSG o Azure Network Watcher.

### <a name="subnet-requirements"></a>Requisitos de subred

La integración con red virtual depende de una subred dedicada. Al crear una subred, la subred de Azure pierde cinco direcciones IP desde el inicio. Se usa una dirección de la subred de integración para cada instancia del plan. Si escala la aplicación a cuatro instancias, se usan cuatro direcciones.

Al escalar o reducir verticalmente el tamaño, el espacio de direcciones necesario se duplica durante un breve período de tiempo. Esto afecta a las instancias admitidas reales y disponibles para un tamaño de subred determinado. En la tabla siguiente se muestran las direcciones máximas disponibles por bloque CIDR y el impacto que esto tiene en la escala horizontal:

| Tamaño de bloque CIDR | Número máximo de direcciones disponibles | Escala horizontal máxima (instancias)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Se da por supuesto que tendrá que escalar o reducir verticalmente el tamaño o la SKU en algún momento. 

Puesto que el tamaño de la subred no se puede cambiar después de la asignación, use una subred lo suficientemente grande como para dar cabida a cualquier escala que pueda alcanzar la aplicación. Para evitar problemas con la capacidad de la subred, debe usar /26 con 64 direcciones.

Si quiere que las aplicaciones de su plan lleguen a una red virtual a la que ya están conectadas mediante las aplicaciones de otro plan, seleccione una subred distinta a la usada por la característica de integración con red virtual ya existente.

### <a name="routes"></a>Rutas

Hay dos tipos de enrutamiento que se deben tener en cuenta al configurar la integración con red virtual regional. El enrutamiento de aplicaciones define qué tráfico se enruta desde la aplicación y hacia la red virtual. El enrutamiento de red es la capacidad de controlar cómo se enruta y sale el tráfico desde la red virtual.

#### <a name="application-routing"></a>Enrutamiento de aplicaciones

Al configurar el enrutamiento de aplicaciones, puede enrutar todo el tráfico o solo el tráfico privado (también conocido como tráfico [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)) a la red virtual. Este comportamiento se configura mediante la opción Enrutar todo. Si la opción Enrutar todo está deshabilitada, la aplicación solo enruta el tráfico privado a la red virtual. Si desea enrutar todo el tráfico saliente a la red virtual, asegúrese de que Enrutar todo está habilitado.

> [!NOTE]
> * Cuando Enrutar todo está habilitado, todo el tráfico está sujeto a los NSG y UDR que se aplican a la subred de integración. Cuando se habilita el enrutamiento de todo el tráfico, el tráfico de salida sigue enviándose desde las direcciones que se muestran en las propiedades de la aplicación, a menos que proporcione rutas que dirijan el tráfico a otro lugar.
> * Los contenedores de Windows no admiten el enrutamiento de referencias a Key Vault de App Service o la extracción de imágenes de contenedor personalizadas a través de la integración con red virtual.
> * La integración de red virtual regional no puede usar el puerto 25.

Descubra [cómo configurar el enrutamiento de aplicaciones](./configure-vnet-integration-routing.md).

La opción de configuración Enrutar todo es la manera recomendada de habilitar el enrutamiento de todo el tráfico. El uso de la opción de configuración permite auditar el comportamiento con [una directiva integrada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33228571-70a4-4fa1-8ca1-26d0aba8d6ef). Todavía se puede usar la configuración de aplicación `WEBSITE_VNET_ROUTE_ALL` existente y se puede habilitar todo el enrutamiento del tráfico con cualquiera de estas opciones.

#### <a name="network-routing"></a>Enrutamiento de red

Las tablas de enrutamiento se pueden usar para enrutar el tráfico de salida de la aplicación al lugar que se desee. Las tablas de enrutamiento afectan al tráfico de destino. Cuando Enrutar todo está deshabilitado en el [enrutamiento de aplicaciones](#application-routing), solo el tráfico privado (RFC1918) se ve afectado por sus tablas de enrutamiento. Los destinos más habituales suelen ser puertas de enlace o dispositivos de firewall. Las rutas que se establecen en la subred de integración no afectan a las respuestas a las solicitudes de entrada de la aplicación. 

Si quiere enrutar todo el tráfico saliente del entorno local, puede utilizar una tabla de rutas para enviarlo todo a la puerta de enlace de ExpressRoute. Si no enruta el tráfico a una puerta de enlace, asegúrese de establecer las rutas en la red externa para poder enviar de vuelta las respuestas.

Las rutas del Protocolo de puerta de enlace de borde (BGP) también afectan al tráfico de la aplicación. Si tiene rutas de BGP cuyo origen es algo similar a una puerta de enlace de ExpressRoute, el tráfico de salida de la aplicación se verá afectado. De forma similar a las rutas definidas por el usuario, las rutas BGP afectan al tráfico según la configuración del ámbito de enrutamiento.

### <a name="network-security-groups"></a>Grupos de seguridad de red

Una aplicación que emplee la integración con red virtual regional puede usar un [grupo de seguridad de red](../virtual-network/network-security-groups-overview.md) para bloquear el tráfico saliente a los recursos de la red virtual o de Internet. Para bloquear el tráfico a las direcciones públicas, debe asegurarse de habilitar la opción [Enrutar todo](#application-routing) en la red virtual. Cuando Enrutar todo no está habilitado, los NSG solo se aplican al tráfico RFC1918.

Un grupo de seguridad de red que se aplique a la subred de integración está en vigor, con independencia de las tablas de enrutamiento aplicadas a la subred de integración. 

Las reglas de entrada de un grupo de seguridad de red no incumben a la aplicación, ya que la integración con red virtual solo afecta al tráfico saliente de la aplicación. Para controlar el trafico de entrada a la aplicación, use la característica Restricciones de acceso.

### <a name="service-endpoints"></a>Puntos de conexión del servicio

La integración con red virtual regional le permite acceder a los servicios de Azure que están protegidos con puntos de conexión de servicio. Para acceder a un servicio protegido mediante puntos de conexión de servicio, siga estos pasos:

* Configure la integración con red virtual regional con su aplicación web para conectarse a una subred específica y realizar la integración.
* Vaya al servicio de destino y configure los puntos de conexión de servicio en la subred de integración.

### <a name="private-endpoints"></a>Puntos de conexión privados

Si quiere realizar llamadas a [Puntos de conexión privados](./networking/private-endpoint.md), debe asegurarse de que las búsquedas de DNS se resuelvan en el punto de conexión privado. Puede aplicar este comportamiento de una de las siguientes formas: 

* Realizar la integración con zonas privadas de Azure DNS. Si la red virtual no tiene un servidor DNS personalizado, esta integración se lleva a cabo automáticamente cuando las zonas están vinculadas a ella.
* Administrar el punto de conexión privado en el servidor DNS que usa la aplicación. Para administrar la configuración, debe conocer la dirección IP del punto de conexión privado y, después, apuntar el punto de conexión al que está intenta acceder a esa dirección con un registro D.
* Configurar su propio servidor DNS para reenviarlo a zonas privadas de Azure DNS.

### <a name="azure-dns-private-zones"></a>Zonas privadas de Azure DNS 

Una vez que la aplicación se integra con la red virtual, usa el mismo servidor DNS con el que está configurada la red virtual y, si no se especifica ningún DNS personalizado, usará DNS predeterminado de Azure y cualquier zona privada vinculada a la red virtual.

### <a name="limitations"></a>Limitaciones

Existen algunas limitaciones cuando se usa la integración con red virtual regional:

* La característica está disponible en todas las unidades de escalado de App Service de nivel Premium V2 y Premium V3. También está disponible en Estándar, pero solo desde las unidades de escalado de App Service más recientes. Si está en una unidad de escalado anterior, solo puede usar la característica desde un plan de App Service Premium V2. Si quiere estar seguro de que puede usar la característica en un plan de App Service Estándar, cree la aplicación en un plan de App Service Premium V3. Estos planes solo se admiten en las unidades de escalado más recientes. Si quiere, puede reducir verticalmente después de crear el plan.
* Las aplicaciones del plan Aislado que estén en una instancia de App Service Environment no pueden usar la característica.
* No puede acceder a los recursos a través de conexiones de emparejamiento con redes virtuales clásicas.
* La característica requiere una subred sin usar que sea un bloque IPv4 `/28` o mayor en una red virtual de Azure Resource Manager.
* La aplicación y la VNet deben estar en la misma región.
* La red virtual de integración no puede tener definidos espacios de direcciones IPv6.
* La subred de integración solo puede usarla un plan de App Service.
* No puede eliminar una VNet con una aplicación integrada. Quite la integración antes de eliminar la VNet.
* Solo se puede tener una integración con red virtual regional por plan de App Service. Varias aplicaciones en el mismo plan de App Service pueden usar la misma red virtual.
* No se puede cambiar la suscripción de una aplicación o un plan mientras haya una aplicación que use esta característica.

## <a name="gateway-required-vnet-integration"></a>Integración con red virtual con requisito de puerta de enlace

La integración con red virtual con requisito de puerta de enlace permite conectarse a una red virtual de otra región o a una red virtual clásica. Integración con red virtual con requisito de puerta de enlace

* Permite que una aplicación se conecte solo a una VNet a la vez.
* Permite la integración de hasta cinco VNet dentro de un plan de App Service.
* Permite que varias aplicaciones de un mismo plan de App Service usen la misma VNet sin que ello afecte al número total que se puede utilizar en un plan de App Service. Si tiene seis aplicaciones que usan la misma VNet en el mismo plan de App Service, solo se contabilizará una sola VNet en uso.
* Admite un SLA del 99,9 % debido al SLA de la puerta de enlace.
* Permite que las aplicaciones utilicen el DNS con el que se ha configurado la VNet.
* Para poder conectarse a una aplicación, se necesita una puerta de enlace basada en rutas de red virtual que esté configurada con una VPN de punto a sitio SSTP.

No se puede usar la integración con red virtual con requisito de puerta de enlace en las siguientes circunstancias:

* Con una VNet conectada a Azure ExpressRoute.
* Desde una aplicación Linux.
* Desde un [contenedor de Windows](./quickstart-custom-container.md).
* Para acceder a recursos protegidos por puntos de conexión de servicio.
* Con una puerta de enlace coexistente que admita tanto VPN de ExpressRoute como VPN de punto a sitio o de sitio a sitio.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Configuración de una puerta de enlace en Azure Virtual Network

Para crear una puerta de enlace:

1. [Cree la puerta de enlace de red virtual y la subred](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw). Seleccione un tipo de VPN basada en rutas.

1. [Establezca las direcciones de punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool). Si la puerta de enlace no está en la SKU básica, IKEV2 debe estar deshabilitado en la configuración de punto a sitio y SSTP debe estar seleccionado. El espacio de direcciones de punto a sitio debe especificarse en bloques de direcciones de RFC 1918 (10.0.0.0/8, 172.16.0.0/12 y 192.168.0.0/16).

Si crea la puerta de enlace para usarla con la integración con red virtual con requisito de puerta de enlace, no es necesario cargar un certificado. La creación de la puerta de enlace puede tardar 30 minutos. No podrá integrar su aplicación con la red virtual hasta que se cree la puerta de enlace.

### <a name="how-gateway-required-vnet-integration-works"></a>Funcionamiento de la integración con red virtual con requisito de puerta de enlace

La integración con red virtual con requisito de puerta de enlace se basa en la tecnología de VPN de punto a sitio. Las VPN de punto a sitio limitan el acceso de red a la máquina virtual que hospeda la aplicación. Las aplicaciones están restringidas para enviar tráfico a Internet solo a través de conexiones híbridas o de la integración con red virtual. Cuando la aplicación se ha configurado con el portal para que utilice la integración con red virtual con requisito de puerta de enlace, se entabla una compleja negociación en su nombre para crear y asignar certificados en la puerta de enlace y en la aplicación. El resultado es que los trabajos que se usan para hospedar las aplicaciones pueden conectarse directamente a la puerta de enlace de red virtual de la VNet seleccionada.

:::image type="content" source="./media/overview-vnet-integration/vnetint-how-gateway-works.png" alt-text="Funcionamiento de la integración con red virtual con requisito de puerta de enlace":::

### <a name="access-on-premises-resources"></a>Acceso a recursos locales

Las aplicaciones pueden acceder a los recursos locales mediante la integración con redes virtuales que tengan conexiones de sitio a sitio. Si usa la integración con red virtual con requisito de puerta de enlace, actualice las rutas de puerta de enlace de red virtual locales con los bloques de direcciones de punto a sitio. En la configuración inicial de la VPN de sitio a sitio, los scripts que se usan para configurarla deben configurar las rutas correctamente. Si agrega las direcciones de punto a sitio después de crear la VPN de sitio a sitio, deberá actualizar las rutas manualmente. El procedimiento varía según la puerta de enlace y no se describe aquí. No se puede tener BGP configurado con una conexión VPN de sitio a sitio.

No se requiere ninguna configuración adicional para que la característica de integración con red virtual regional acceda a los recursos locales mediante la red virtual. Basta con conectar la red virtual a los recursos de entorno local mediante ExpressRoute o una VPN de sitio a sitio.

> [!NOTE]
> La característica de integración con red virtual con requisito de puerta de enlace no integra una aplicación con una red virtual que tenga una puerta de enlace de ExpressRoute. Aunque la puerta de enlace de ExpressRoute esté configurada en [modo de coexistencia](../expressroute/expressroute-howto-coexist-resource-manager.md), la integración con red virtual no funciona. Si necesita acceder a los recursos mediante una conexión de ExpressRoute, utilice la característica de integración con red virtual regional o una instancia de [App Service Environment](./environment/intro.md) que se ejecute en la red virtual.

### <a name="peering"></a>Emparejamiento

Si usa el emparejamiento con integración con red virtual regional, no es necesario configurar nada más.

Si usa la integración con red virtual con requisito de puerta de enlace con el emparejamiento, deberá configurar algunos elementos más. Para configurar el emparejamiento para que funcione con su aplicación:

1. Agregue una conexión de emparejamiento en la red virtual a la que se conecta su aplicación. Al agregar la conexión de emparejamiento, habilite **Permitir acceso a red virtual** y active **Permitir tráfico reenviado** y **Permitir tránsito de puerta de enlace**.
1. Agregue una conexión de emparejamiento en la VNet que se está emparejando con la VNet a la que está conectado. Al agregar la conexión de emparejamiento en la VNet de destino, habilite **Permitir acceso a red virtual** y seleccione **Permitir tráfico reenviado** y **Allow remote gateways** (Permitir puertas de enlace remotas).
1. Vaya a la interfaz de usuario **Plan de App Service** > **Redes** > **Integración con red virtual** en el portal. Seleccione la red virtual a la que está conectada su aplicación. En la sección de enrutamiento, agregue el intervalo de direcciones de la VNet que está emparejada con la VNet a la que está conectada su aplicación.

## <a name="manage-vnet-integration"></a>Administración de integración con red virtual

La conexión y la desconexión con una red virtual tiene lugar en el nivel de la aplicación. Las operaciones que pueden afectar a la integración con red virtual en varias aplicaciones se encuentran en el nivel de plan de App Service. En el portal de la aplicación > **Redes** > **Integración con red virtual**, puede obtener detalles sobre la red virtual. Puede encontrar información similar en el nivel del plan de App Service en el portal **Plan de App Service** > **Redes** > **Integración con red virtual**.

La única operación que puede realizar en la vista de aplicación de la instancia de integración con red virtual es desconectar la aplicación de la red virtual si está conectada a ella. Para desconectar la aplicación de una red virtual, seleccione **Desconectar**. La aplicación se reinicia cuando se desconecta de una red virtual. La desconexión no cambia la red virtual. La subred o la puerta de enlace no se quitan. Si después quiere eliminar la red virtual, primero desconecte la aplicación de la red virtual y elimine los recursos que hay en ella, como las puertas de enlace.

La interfaz de usuario de integración con red virtual del plan de App Service muestra todas las integraciones con redes virtuales usadas por las aplicaciones de su plan de App Service. Para ver más detalles sobre cada red virtual, seleccione la red virtual que le interese. Hay dos acciones que puede realizar aquí en relación con la integración con red virtual con requisito de puerta de enlace:

* **Sincronización de red**: la operación de sincronización de red solo se usa con la característica de integración con red virtual con requisito de puerta de enlace. Realizar una operación de sincronización de red garantiza que los certificados y la información de red estén sincronizados. Si agrega o cambia el DNS de la red virtual, debe ejecutar una operación de sincronización de la red. Esta operación reinicia todas las aplicaciones que usen esta red virtual. Esta operación no funcionará si usa una aplicación y una red virtual que pertenezcan a distintas suscripciones.
* **Agregar rutas**: la adición de rutas impulsa el tráfico de salida a la red virtual.

La dirección IP privada asignada a la instancia se expone por medio de la variable de entorno, **WEBSITE_PRIVATE_IP**. La interfaz de usuario de la consola de Kudu también muestra la lista de variables de entorno disponibles para la aplicación web. Esta dirección IP se asigna desde el intervalo de direcciones de la subred integrada. En el caso de la integración con red virtual regional, el valor de WEBSITE_PRIVATE_IP es una dirección IP del intervalo de direcciones de la subred delegada y, en el de la integración con red virtual con requisito de puerta de enlace, el valor es una dirección IP del intervalo de direcciones del grupo de direcciones de punto a sitio configurado en la puerta de enlace de red virtual. Esta es la dirección IP que va a usar la aplicación web para conectarse a los recursos a través de la red virtual de Azure. 

> [!NOTE]
> El valor de WEBSITE_PRIVATE_IP va a cambiar. Pero va a ser una dirección del intervalo de direcciones de la subred de integración o el intervalo de direcciones de punto a sitio, por lo que va a tener que permitir el acceso desde el intervalo de direcciones completo.
>

### <a name="gateway-required-vnet-integration-routing"></a>Enrutamiento de la integración con red virtual con requisito de puerta de enlace
Las rutas definidas en la red virtual se usan para dirigir el tráfico desde la aplicación hacia la red virtual. Para enviar más tráfico de salida a la red virtual, agregue aquí esos bloques de direcciones. Esta característica solo funciona con la integración con red virtual con requisito de puerta de enlace. Las tablas de rutas no afectan del mismo modo al tráfico de la aplicación cuando se utiliza la integración con red virtual con requisito de puerta de enlace que cuando se utiliza la integración con red virtual regional.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificados de integración con red virtual con requisito de puerta de enlace
Cuando la integración con red virtual con requisito de puerta de enlace está habilitada, existe un intercambio necesario de certificados para garantizar la seguridad de la conexión. Junto con los certificados, se incluyen la configuración de DNS, las rutas y otros elementos similares que describen la red.

Si los certificados o la información de red cambian, es necesario hacer clic en **Sincronizar red**. Al seleccionar **Sincronizar red**, se producirá una breve interrupción en la conectividad entre la aplicación y la red virtual. Aunque no se reinicia la aplicación, la pérdida de conectividad podría hacer que su sitio no funcione correctamente.

## <a name="pricing-details"></a>Detalles de precios
El uso de la característica de integración con red virtual regional no conlleva ningún cargo adicional aparte de los cargos del plan de tarifa del plan de App Service.

Hay tres cargos relacionados con el uso de la característica de integración con red virtual con requisito de puerta de enlace:

* **Cargos de plan de tarifa del plan de App Service**: Las aplicaciones deben estar en un plan de App Service Estándar, Premium, PremiumV2 o PremiumV3. Para obtener más información sobre estos costes, consulte [Precios de App Service](https://azure.microsoft.com/pricing/details/app-service/).
* **Costos de la transferencia de datos**: Se aplica un cargo de salida de datos, aunque la red virtual esté en el mismo centro de datos. Estos cargos se describen en la página de [detalles de precios de Transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/).
* **Costos de VPN Gateway**: Existe un costo para la puerta de enlace de red virtual necesario para la VPN de punto a sitio. Para obtener más información, vea [Precios de VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/).

## <a name="troubleshooting"></a>Solución de problemas

> [!NOTE]
> La integración con red virtual no se admite en escenarios de Docker Compose en App Service.
> Las restricciones de acceso se omiten si existe un punto de conexión privado.

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]
