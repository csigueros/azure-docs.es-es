---
title: Integración de aplicaciones con Azure Virtual Network
description: Integre aplicaciones en Azure App Service con Azure Virtual Network.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/04/2021
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 444831d1d8e9982ac0837e90fe04941b5ae928a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728104"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integración de su aplicación con una instancia de Azure Virtual Network

En este artículo, se describe la característica Integración con red virtual de Azure App Service y se explica cómo configurarla con aplicaciones en [Azure App Service](./overview.md). Con [Azure Virtual Network][VNETOverview] (VNet), puede colocar muchos de los recursos de Azure en una red que se puede enrutar, distinta de Internet. La característica Integración con red virtual permite a las aplicaciones acceder a los recursos de una red virtual o mediante esta. La integración con redes virtuales no permite el acceso privado a las aplicaciones.

Azure App Service adopta dos variantes:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Habilitación de Integración con red virtual

1. Vaya a la interfaz de usuario de **Redes** en el portal de App Service. En **Integración de red virtual**, seleccione **Haga clic aquí para configurar**.

1. Seleccione **Agregar VNET**.

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-app.png" alt-text="Selección de Integración con red virtual":::

1. La lista desplegable contiene todas las redes virtuales de Azure Resource Manager de la suscripción en la misma región. Debajo, se muestra una lista de las redes virtuales de Resource Manager en todas las demás regiones. Seleccione la VNet con la que quiere realizar la integración.

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-add-vnet.png" alt-text="Seleccione la red virtual.":::

    * Si la VNet está en la misma región, cree una nueva subred o seleccione una subred vacía existente.
    * Para seleccionar una VNet de otra región, debe tener una puerta de enlace de VNet aprovisionada con la conectividad de punto a sitio habilitada.
    * Para integrar con una red virtual clásica, en lugar de seleccionar la lista desplegable **Virtual Network**, seleccione **Haga clic aquí para conectarse a una red virtual clásica**. Seleccione la red virtual clásica que desee usar. La VNet de destino debe tener una puerta de enlace de Virtual Network aprovisionada con la conectividad de punto a sitio habilitada.

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-classic.png" alt-text="Seleccione una red virtual clásica.":::

Durante la integración, la aplicación se reinicia. Una vez finalizada la integración, verá los detalles de la VNet con la que está integrada.

## <a name="regional-vnet-integration"></a>Integración con red virtual regional

La integración con red virtual regional admite la conexión a una red virtual de la misma región y no requiere una puerta de enlace. Cuando se utiliza la versión regional de Integración con red virtual, la aplicación puede acceder a:

* Recursos en una VNet de la misma región que la aplicación.
* Recursos de las VNet emparejadas con la VNet con la que se integra la aplicación.
* Servicios protegidos mediante puntos de conexión de servicio.
* Recursos de diferentes conexiones de Azure ExpressRoute.
* Recursos de la VNet con la que está integrado.
* Recursos en conexiones emparejadas, lo que incluye conexiones de Azure ExpressRoute.
* Servicios habilitados para puntos de conexión privados.

Si Integración con red virtual se utiliza con redes virtuales de la misma región, se pueden usar las siguientes características de redes de Azure:

* **Grupos de seguridad de red (NSG)** : el tráfico saliente se puede bloquear con un grupo de seguridad de red que se encuentre en la subred de integración. Las reglas de entrada no se aplican, ya que Integración con red virtual no se puede usar para proporcionar acceso de entrada a la aplicación.
* **Tablas de enrutamiento (UDR)** : puede colocar una tabla de enrutamiento en la subred de integración para enviar el tráfico de salida donde quiera.

La característica es totalmente compatible con aplicaciones para Windows y Linux, incluidos los [contenedores personalizados](./quickstart-custom-container.md). Todos los comportamientos actúan del mismo modo entre aplicaciones para Windows y Linux.

### <a name="how-regional-vnet-integration-works"></a>Funcionamiento de la versión regional de Integración con red virtual

Las aplicaciones en App Service se hospedan en roles de trabajo. Los planes de precios básico y más elevados son planes de hospedaje dedicados donde no hay ninguna otra carga de trabajo de cliente ejecutándose en los mismos trabajos. La versión regional de Integración con red virtual funciona montando interfaces virtuales con las direcciones de la subred delegada. Como la dirección de origen está en la VNet, tiene acceso a la mayoría de las cosas que están en esa VNet o a las que se puede acceder a través de ella, como cualquier máquina virtual que estuviera en la VNet. La implementación de redes es diferente de la ejecución de una máquina virtual en la VNet. Este es el motivo por el que algunas características de red todavía no están disponibles para esta característica.

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-how-regional-works.png" alt-text="Funcionamiento de la versión regional de Integración con red virtual":::

Cuando la integración de VNet regional está habilitada, su aplicación realiza salidas a través de su VNet. Las direcciones salientes que se muestran en el portal de propiedades de la aplicación siguen siendo las direcciones usadas por la aplicación. Si todo el enrutamiento de tráfico está habilitado, todo el tráfico saliente se envía a la red virtual. Si no está habilitado todo el enrutamiento de tráfico, solo se enviarán a la red virtual el tráfico privado (RFC1918) y los puntos de conexión de servicio configurados en la subred de integración y el tráfico saliente a Internet pasará por los mismos canales normales.

La característica solo admite una interfaz virtual por trabajo. Una interfaz virtual por trabajo significa una característica Integración con red virtual regional por plan de App Service. Todas las aplicaciones en el mismo plan de App Service pueden usar la misma Integración con red virtual. Si necesita que una aplicación se conecte a una VNet adicional, debe crear otro plan de App Service. La interfaz virtual utilizada no es un recurso al que los clientes tengan acceso directo.

Dada la forma en que esta tecnología funciona, el tráfico que se usa con Integración con red virtual no se muestra en los registros de flujos de NSG o Azure Network Watcher.

### <a name="subnet-requirements"></a>Requisitos de subred

La integración con red virtual depende de una subred dedicada. Al aprovisionar una subred, la subred de Azure pierde cinco direcciones IP desde el inicio. Se usa una dirección de la subred de integración para cada instancia del plan. Si escala la aplicación a cuatro instancias, se usan cuatro direcciones. 

Al escalar o reducir verticalmente el tamaño, el espacio de direcciones necesario se duplica durante un breve período de tiempo. Esto afecta a las instancias admitidas reales y disponibles para un tamaño de subred determinado. En la tabla siguiente se muestran las direcciones máximas disponibles por bloque CIDR y el impacto que esto tiene en la escala horizontal:

| Tamaño de bloque CIDR | Número máximo de direcciones disponibles | Escala horizontal máxima (instancias)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Se da por supuesto que tendrá que escalar o reducir verticalmente el tamaño o la SKU en algún momento. 

Puesto que el tamaño de la subred no se puede cambiar después de la asignación, use una subred lo suficientemente grande como para dar cabida a cualquier escala que pueda alcanzar la aplicación. Para evitar problemas con la capacidad de la subred, debe usar /26 con 64 direcciones.

Si quiere que las aplicaciones de su plan lleguen a una VNet a la que ya están conectadas aplicaciones de otro plan, seleccione una subred distinta a la usada por la característica Integración con VNet ya existente.

### <a name="routes"></a>Rutas

Hay dos tipos de enrutamiento que se deben tener en cuenta al configurar la integración de red virtual regional. El enrutamiento de aplicaciones define qué tráfico se enruta desde la aplicación y hacia la red virtual. El enrutamiento de red es la capacidad de controlar cómo se enruta y sale el tráfico desde la red virtual.

#### <a name="application-routing"></a>Enrutamiento de aplicaciones

Al configurar el enrutamiento de aplicaciones, puede enrutar todo el tráfico o solo el tráfico privado (también conocido como tráfico [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)) a la red virtual. Se configura mediante la opción Enrutar todo. Si la opción Enrutar todo está deshabilitada, la aplicación solo enruta el tráfico privado a la red virtual. Si desea enrutar todo el tráfico saliente a la red virtual, asegúrese de que Enrutar todo está habilitado.

> [!NOTE]
> * Cuando Enrutar todo está habilitado, todo el tráfico está sujeto a los NSG y UDR que se aplican a la subred de integración. Cuando se habilita el enrutamiento de todo el tráfico, el tráfico de salida sigue enviándose desde las direcciones que se muestran en las propiedades de la aplicación, a menos que proporcione rutas que dirijan el tráfico a otro lugar.
> 
> * La opción de Enrutar todo no se admite actualmente en contenedores de Windows.
>
> * La integración de red virtual regional no puede usar el puerto 25.

Puede usar los pasos siguientes para deshabilitar la opción Enrutar todo en la aplicación a través del portal: 

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-enabled.png" alt-text="Opción Enrutar todo habilitada":::

1. Vaya a la interfaz de usuario de **Integración de red virtual** en el portal de la aplicación.
1. Establezca **Enrutar todo** en deshabilitado.
    
    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-disabling.png" alt-text="Deshabilitar Enrutar todo":::

1. Seleccione **Sí**.

También puede configurar Enrutar todo con la CLI (*Nota*: el mínimo `az version` requerido es 2.27.0):

```azurecli-interactive
az webapp config set --resource-group myRG --name myWebApp --vnet-route-all-enabled [true|false]
```

La opción de configuración Enrutar todo reemplaza y tiene prioridad sobre la configuración de la aplicación `WEBSITE_VNET_ROUTE_ALL` heredada.

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-appsetting.png" alt-text="Valor de aplicaciones Enrutar todo":::

#### <a name="network-routing"></a>Enrutamiento de red

Las tablas de enrutamiento se pueden usar para enrutar el tráfico de salida de la aplicación al lugar que se desee. Las tablas de enrutamiento afectan al tráfico de destino. Cuando Enrutar todo está deshabilitado en el [enrutamiento de aplicaciones](#application-routing), solo el tráfico privado (RFC1918) se ve afectado por sus tablas de enrutamiento. Los destinos más habituales suelen ser puertas de enlace o dispositivos de firewall. Las rutas que se establecen en la subred de integración no afectan a las respuestas a las solicitudes de entrada de la aplicación. 

Si desea enrutar todo el tráfico de salida del entorno local, puede utilizar una tabla de rutas para enviar el tráfico de salida a la puerta de enlace de ExpressRoute. Si no enruta el tráfico a una puerta de enlace, asegúrese de establecer las rutas en la red externa para poder enviar de vuelta las respuestas.

Las rutas del Protocolo de puerta de enlace de borde (BGP) también afectan al tráfico de la aplicación. Si tiene rutas de BGP cuyo origen es algo similar a una puerta de enlace de ExpressRoute, el tráfico de salida de la aplicación se verá afectado. De forma similar a las rutas definidas por el usuario, las rutas BGP afectan al tráfico según la configuración del ámbito de enrutamiento.

### <a name="network-security-groups"></a>Grupos de seguridad de red

Una aplicación que emplee la versión regional de Integración con red virtual puede usar un [grupo de seguridad de red][VNETnsg] para bloquear el tráfico de salida a los recursos de la VNet o Internet. Para bloquear el tráfico a las direcciones públicas, debe asegurarse de habilitar la opción [Enrutar todo](#application-routing) en la red virtual. Cuando Enrutar todo no está habilitado, los NSG solo se aplican al tráfico RFC1918.

Un grupo de seguridad de red que se aplique a la subred de integración está en vigor, con independencia de las tablas de enrutamiento aplicadas a la subred de integración. 

Las reglas de entrada de un grupo de seguridad de red no se aplican a la aplicación, ya que Integración con red virtual solo afecta al tráfico saliente de la aplicación. Para controlar el trafico de entrada a la aplicación, use la característica Restricciones de acceso.

### <a name="service-endpoints"></a>Puntos de conexión del servicio

La integración con red virtual regional le permite acceder a los servicios de Azure que están protegidos con puntos de conexión de servicio. Para acceder a un servicio protegido mediante puntos de conexión de servicio, debe hacer lo siguiente:

* Configure la versión regional de integración con red virtual con su aplicación web para conectarse a una subred específica para la integración.
* Vaya al servicio de destino y configure los puntos de conexión de servicio en la subred de integración.

### <a name="private-endpoints"></a>Puntos de conexión privados

Si quiere realizar llamadas a [Puntos de conexión privados][privateendpoints], debe asegurarse de que las búsquedas de DNS se resuelvan en el punto de conexión privado. Puede aplicar este comportamiento de una de las siguientes formas: 

* Realizar la integración con zonas privadas de Azure DNS. Cuando la red virtual no tiene un servidor DNS personalizado, automáticamente se le asigna uno cuando las zonas están vinculadas a la red virtual.
* Administrar el punto de conexión privado en el servidor DNS que usa la aplicación. Para ello, debe conocer la dirección del punto de conexión privado y, a continuación, apuntar el punto de conexión al que está intentando acceder a esa dirección con un registro A.
* Configurar su propio servidor DNS para reenviarlo a zonas privadas de Azure DNS.

### <a name="azure-dns-private-zones"></a>Zonas privadas de Azure DNS 

Una vez que la aplicación se integra con la red virtual, usa el mismo servidor DNS con el que está configurada la red virtual y, si no se especifica ningún DNS personalizado, usará DNS predeterminado de Azure y cualquier zona privada vinculada a la red virtual.

> [!NOTE]
> En el caso de las aplicaciones Linux, Azure DNS Private Zones solo funciona si Enrutar todo está habilitado.

### <a name="limitations"></a>Limitaciones

Existen algunas limitaciones cuando se la característica Integración con red virtual se utiliza con redes virtuales que están en la misma región:

* No se puede acceder a los recursos en las conexiones de emparejamiento global.
* No se puede acceder a los recursos a través de conexiones de emparejamiento con redes virtuales clásicas.
* La característica está disponible en todas las unidades de escalado de App Service de nivel Premium V2 y Premium V3. También está disponible en Estándar, pero solo desde las unidades de escalado de App Service más recientes. Si está en una unidad de escalado anterior, solo puede usar la característica desde un plan de App Service Premium V2. Si quiere estar seguro de que puede usar la característica en un plan de App Service Estándar, cree la aplicación en un plan de App Service Premium V3. Estos planes solo se admiten en las unidades de escalado más recientes. Si quiere, después puede reducir verticalmente.  
* La subred de integración solo puede usarla un plan de App Service.
* Las aplicaciones del plan Aislado que estén en una instancia de App Service Environment no pueden usar la característica.
* La característica requiere una subred sin usar que sea /28 o mayor en una red virtual de Azure Resource Manager.
* La aplicación y la VNet deben estar en la misma región.
* No puede eliminar una VNet con una aplicación integrada. Quite la integración antes de eliminar la VNet.
* Solo se puede tener una característica Integración con red virtual regional por plan de App Service. Varias aplicaciones en el mismo plan de App Service pueden usar la misma red virtual.
* No se puede cambiar la suscripción de una aplicación o un plan mientras haya una aplicación que use Integración con red virtual regional.
* La aplicación no puede resolver direcciones en Azure DNS Private Zones en planes Linux sin que se realicen cambios en la configuración.

## <a name="gateway-required-vnet-integration"></a>Integración con red virtual con requisito de puerta de enlace

La versión de Integración con red virtual que necesita una puerta de enlace permite conectarse a una red virtual de otra región o a una red virtual clásica. Integración con red virtual con requisito de puerta de enlace:

* Permite que una aplicación se conecte solo a una VNet a la vez.
* Permite la integración de hasta cinco VNet dentro de un plan de App Service.
* Permite que varias aplicaciones de un mismo plan de App Service usen la misma VNet sin que ello afecte al número total que se puede utilizar en un plan de App Service. Si tiene seis aplicaciones que usan la misma VNet en el mismo plan de App Service, solo se contabilizará una sola VNet en uso.
* Admite un SLA del 99,9 % debido al SLA de la puerta de enlace.
* Permite que las aplicaciones utilicen el DNS con el que se ha configurado la VNet.
* Para poder conectarse a la aplicación, necesita una puerta de enlace basada en rutas de Virtual Network que esté configurada con una VPN de punto a sitio SSTP.

La versión de Integración con red virtual que necesita una puerta de enlace no puede utilizarse en las siguientes circunstancias:

* Con una VNet conectada a Azure ExpressRoute.
* Desde una aplicación Linux.
* Desde un [contenedor de Windows](quickstart-custom-container.md).
* Para acceder a recursos protegidos por puntos de conexión de servicio.
* Con una puerta de enlace coexistente que admita tanto VPN de ExpressRoute como VPN de punto a sitio o de sitio a sitio.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Configuración de una puerta de enlace en Azure Virtual Network

Para crear una puerta de enlace:

1. [Cree una subred de puerta de enlace][creategatewaysubnet] en su red virtual.

1. [Cree la instancia de VPN Gateway][creategateway]. Seleccione un tipo de VPN basada en rutas.

1. [Establezca las direcciones de punto a sitio][setp2saddresses]. Si la puerta de enlace no está en la SKU básica, IKEV2 debe estar deshabilitado en la configuración de punto a sitio y SSTP debe estar seleccionado. El espacio de direcciones de punto a sitio debe especificarse en bloques de direcciones de RFC 1918 (10.0.0.0/8, 172.16.0.0/12 y 192.168.0.0/16).

Si crea la puerta de enlace para su uso con Integración con red virtual de App Service, no es necesario cargar un certificado. La creación de la puerta de enlace puede tardar 30 minutos. No podrá integrar su aplicación con la VNet hasta que la puerta de enlace esté aprovisionada.

### <a name="how-gateway-required-vnet-integration-works"></a>Funcionamiento de la versión de Integración con red virtual que necesita una puerta de enlace

La versión de Integración con red virtual que necesita una puerta de enlace se basa en la tecnología VPN de punto a sitio. Las VPN de punto a sitio limitan el acceso de red a la máquina virtual que hospeda la aplicación. Las aplicaciones están restringidas para enviar tráfico a Internet solo a través de conexiones híbridas o de la integración con red virtual. Cuando la aplicación se ha configurado con el portal para que utilice la versión de Integración con red virtual que necesita una puerta de enlace, se entabla una compleja negociación en su nombre para crear y asignar certificados en la puerta de enlace y en la aplicación. El resultado es que los trabajos que se usan para hospedar las aplicaciones pueden conectarse directamente a la puerta de enlace de red virtual de la VNet seleccionada.

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-how-gateway-works.png" alt-text="Funcionamiento de la versión de Integración con red virtual que necesita una puerta de enlace":::

### <a name="access-on-premises-resources"></a>Acceso a recursos locales

Las aplicaciones pueden acceder a los recursos locales mediante la integración con redes virtuales que tengan conexiones de sitio a sitio. Si usa la Integración con red virtual con requisito de puerta de enlace, actualice las rutas de puerta de enlace de red virtual locales con los bloques de direcciones de punto a sitio. En la configuración inicial de la VPN de sitio a sitio, los scripts que se usan para configurarla deben configurar las rutas correctamente. Si agrega las direcciones de punto a sitio después de crear la VPN de sitio a sitio, deberá actualizar las rutas manualmente. El procedimiento varía según la puerta de enlace y no se describe aquí. No se puede tener BGP configurado con una conexión VPN de sitio a sitio.

No se requiere ninguna configuración adicional para que la característica Integración con red virtual regional acceda a través de la red virtual a los recursos locales. Basta con conectar la red virtual a los recursos de entorno local mediante ExpressRoute o una VPN de sitio a sitio.

> [!NOTE]
> La característica Integración con red virtual con requisito de puerta de enlace no integra una aplicación con una red virtual que tiene una puerta de enlace de ExpressRoute. Aunque la puerta de enlace de ExpressRoute esté configurada en [modo de coexistencia][VPNERCoex], la Integración con red virtual no funciona. Si necesita acceder a los recursos mediante una conexión de ExpressRoute, utilice la característica Integración con red virtual regional o un entorno [App Service Environment][ASE] que se ejecute en la red virtual.
>
>

### <a name="peering"></a>Emparejamiento

Si usa el emparejamiento con Integración con red virtual regional, no es necesario configurar nada más.

Si usa la Integración con red virtual con requisito de puerta de enlace con el emparejamiento, deberá configurar algunos elementos más. Para configurar el emparejamiento para que funcione con su aplicación:

1. Agregue una conexión de emparejamiento en la red virtual a la que se conecta su aplicación. Al agregar la conexión de emparejamiento, habilite **Permitir acceso a red virtual** y active **Permitir tráfico reenviado** y **Permitir tránsito de puerta de enlace**.
1. Agregue una conexión de emparejamiento en la VNet que se está emparejando con la VNet a la que está conectado. Al agregar la conexión de emparejamiento en la VNet de destino, habilite **Permitir acceso a red virtual** y seleccione **Permitir tráfico reenviado** y **Allow remote gateways** (Permitir puertas de enlace remotas).
1. Vaya a la interfaz de usuario **Plan de App Service** > **Redes** > **Integración con red virtual** en el portal. Seleccione la red virtual a la que está conectada su aplicación. En la sección de enrutamiento, agregue el intervalo de direcciones de la VNet que está emparejada con la VNet a la que está conectada su aplicación.

## <a name="manage-vnet-integration"></a>Administración de Integración con red virtual

La conexión y la desconexión con una red virtual tiene lugar en el nivel de la aplicación. Las operaciones que pueden afectar a la integración con red virtual en varias aplicaciones se encuentran en un nivel del plan de App Service. En el portal de la aplicación > **Redes** > **Integración con red virtual**, puede obtener detalles sobre la red virtual. Puede encontrar información similar en el nivel del plan de App Service en el portal **Plan de App Service** > **Redes** > **Integración con red virtual**.

La única operación que puede realizar en la vista de aplicación de la instancia de Integración con red virtual es desconectar la aplicación de la red virtual si está conectada a ella. Para desconectar la aplicación de una red virtual, seleccione **Desconectar**. La aplicación se reinicia cuando se desconecta de una red virtual. La desconexión no cambia la red virtual. La subred o la puerta de enlace no se quitan. Si después quiere eliminar la red virtual, primero desconecte la aplicación de la red virtual y elimine los recursos que hay en ella, como las puertas de enlace.

La interfaz de usuario de Integración con red virtual del plan de App Service muestra todas las integraciones de redes virtuales usadas por las aplicaciones de su plan de App Service. Para ver más detalles sobre cada red virtual, seleccione la red virtual que le interese. Hay dos acciones que puede realizar aquí en relación con la versión de Integración con red virtual que necesita una puerta de enlace:

* **Sincronizar red**: la operación de sincronización de red solo se usa con la característica Integración con red virtual con requisito de puerta de enlace. Realizar una operación de sincronización de red garantiza que los certificados y la información de red estén sincronizados. Si agrega o cambia el DNS de la red virtual, debe ejecutar una operación de sincronización de la red. Esta operación reinicia todas las aplicaciones que usen esta red virtual. Esta operación no funcionará si usa una aplicación y una red virtual que pertenezcan a distintas suscripciones.
* **Agregar rutas**: la adición de rutas impulsa el tráfico de salida a la red virtual.

La dirección IP privada asignada a la instancia se expone por medio de la variable de entorno, **WEBSITE_PRIVATE_IP**. La interfaz de usuario de la consola de Kudu también muestra la lista de variables de entorno disponibles para la aplicación web. Esta dirección IP se asigna desde el intervalo de direcciones de la subred integrada. En el caso de la integración con red virtual regional, el valor de WEBSITE_PRIVATE_IP es una dirección IP del intervalo de direcciones de la subred delegada y, en el de la integración con red virtual con requisito de puerta de enlace, el valor es una dirección IP del intervalo de direcciones del grupo de direcciones de punto a sitio configurado en la puerta de enlace de red virtual. Esta es la dirección IP que va a usar la aplicación web para conectarse a los recursos a través de la red virtual. 

> [!NOTE]
> El valor de WEBSITE_PRIVATE_IP va a cambiar. Pero va a ser una dirección del intervalo de direcciones de la subred de integración o el intervalo de direcciones de punto a sitio, por lo que va a tener que permitir el acceso desde el intervalo de direcciones completo.
>

### <a name="gateway-required-vnet-integration-routing"></a>Enrutamiento de Integración con red virtual con requisito de puerta de enlace
Las rutas definidas en la red virtual se usan para dirigir el tráfico desde la aplicación hacia la red virtual. Para enviar más tráfico de salida a la red virtual, agregue aquí esos bloques de direcciones. Esta característica solo funciona con la Integración con red virtual con requisito de puerta de enlace. Las tablas de enrutamiento no afectan del mismo modo al tráfico de la aplicación cuando se utiliza la versión de Integración con red virtual que necesita una puerta de enlace del mismo modo que cuando se utiliza la versión regional.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificados de Integración con red virtual con requisito de puerta de enlace
Cuando la Integración con red virtual con requisito de puerta de enlace está habilitada, existe un intercambio necesario de certificados para garantizar la seguridad de la conexión. Junto con los certificados, se incluyen la configuración de DNS, las rutas y otros elementos similares que describen la red.

Si los certificados o la información de red cambian, es necesario hacer clic en **Sincronizar red**. Al seleccionar **Sincronizar red**, se producirá una breve interrupción en la conectividad entre la aplicación y la red virtual. Aunque no se reinicia la aplicación, la pérdida de conectividad podría hacer que su sitio no funcione correctamente.

## <a name="pricing-details"></a>Detalles de precios
La característica Integración con red virtual regional no tiene ningún cargo extra por uso más allá de los cargos del plan de tarifa del plan de App Service.

Hay tres cargos relacionados con el uso de la característica Integración con red virtual con requisito de puerta de enlace:

* **Cargos de plan de tarifa del plan de App Service**: Las aplicaciones deben estar en un plan de App Service Estándar, Premium, PremiumV2 o PremiumV3. Para obtener más información sobre estos costes, consulte [Precios de App Service][ASPricing].
* **Costos de la transferencia de datos**: Se aplica un cargo de salida de datos, aunque la red virtual esté en el mismo centro de datos. Estos cargos se describen en la página de [detalles de precios de Transferencia de datos][DataPricing].
* **Costos de VPN Gateway**: Existe un costo para la puerta de enlace de red virtual necesario para la VPN de punto a sitio. Para obtener más información, vea [Precios de VPN Gateway][VNETPricing].

## <a name="troubleshooting"></a>Solución de problemas

> [!NOTE]
> La integración con red virtual no se admite en escenarios de Docker Compose en App Service.
> Las restricciones de acceso de Azure Functions se omiten si existe un punto de conexión privado.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Hay disponible compatibilidad con la CLI para la versión regional de Integración con red virtual. Para acceder a los comandos siguientes, [instale la CLI de Azure][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.
```

PowerShell también ofrece compatibilidad con la integración con red virtual regional, pero es necesario crear un recurso genérico con una matriz de propiedades del resourceID de subred

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

# Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

# Creation of the VNet Integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```

<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
[VNETnsg]: /azure/virtual-network/security-overview/
