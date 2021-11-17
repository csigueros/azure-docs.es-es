---
title: Opciones de redes de Azure Functions
description: Introducción a todas las opciones de redes disponibles en Azure Functions.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: c954f411d14aaa0f33bf4a3423a67ae64204f932
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892865"
---
# <a name="azure-functions-networking-options"></a>Opciones de redes de Azure Functions

En este artículo se describen las características de redes disponibles en las opciones de hospedaje de Azure Functions. Todas las opciones de redes siguientes ofrecen la posibilidad de acceder a recursos sin usar direcciones enrutables de Internet o de restringir el acceso a Internet a una aplicación de funciones.

Los modelos de hospedaje tienen diferentes niveles de aislamiento de red disponibles. Elegir el correcto le ayuda a cumplir los requisitos de aislamiento de red que tenga.

Puede hospedar aplicaciones de funciones de dos formas:

* Puede elegir entre un conjunto de opciones de plan que se ejecutan en una infraestructura multiinquilino, con distintos niveles de conectividad de red virtual y opciones de escalado:
    * El [plan de consumo](consumption-plan.md) se escala dinámicamente como respuesta a la carga y ofrece opciones de aislamiento de red mínimo.
    * El [plan Premium](functions-premium-plan.md) también se escala dinámicamente y ofrece un aislamiento de red más completo.
    * El [plan de Azure App Service](dedicated-plan.md) funciona en una escala fija y ofrece un aislamiento de red similar al del plan Premium.
* Puede ejecutar funciones en un [App Service Environment](../app-service/environment/intro.md). Este método implementa la función en la red virtual y ofrece aislamiento y control de la red completos.

## <a name="matrix-of-networking-features"></a>Matriz de las características de redes

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>Restricciones de acceso de entrada

Puede usar restricciones de acceso para definir una lista de direcciones IP ordenadas por prioridad a las que se les permite o deniega el acceso a la aplicación. La lista puede incluir direcciones IPv4 e IPv6, bien o subredes de red virtual específicas con [puntos de conexión de servicio](#use-service-endpoints). Si hay una o varias entradas, existe un "denegar a todos" implícito al final de la lista. Las restricciones de IP funcionan con todas las opciones de hospedaje de funciones.

Las restricciones de acceso están disponibles para [Premium](functions-premium-plan.md), [Consumo](consumption-plan.md) y [App Service](dedicated-plan.md).

> [!NOTE]
> Con las restricciones de red vigentes, solo puede implementar desde la red virtual o si ha incluido en la lista de destinatarios seguros la dirección IP del equipo que usa para acceder a Azure Portal. Sin embargo, todavía puede administrar la función en el portal.

Para obtener más información, consulte [Restricciones de acceso estático de Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-endpoint-connections"></a>Conexiones de punto de conexión privado

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

Para llamar a otros servicios que tienen una conexión de punto de conexión privado, como Storage Bus o Services Bus, asegúrese de configurar la aplicación para que haga [llamadas salientes a puntos de conexión privados](#private-endpoints).

### <a name="use-service-endpoints"></a>Uso de puntos de conexión de servicio

Con los puntos de conexión de servicio, puede restringir el acceso a las subredes de Azure Virtual Network. Para restringir el acceso a una subred específica, cree una regla de restricción con un tipo **Virtual Network**. Después, puede seleccionar la suscripción, la red virtual y la subred a las que desea permitir o denegar el acceso. 

Si los puntos de conexión de servicio ya no están habilitados con Microsoft.Web para la subred seleccionada, se habilitarán automáticamente a menos que active la casilla **Omitir los puntos de conexión de servicio de Microsoft.Web que faltan**. La situación en la que convendría habilitar puntos de conexión de servicio en la aplicación pero no en la subred depende fundamentalmente de si se tienen los permisos para habilitarlos en la subred. 

Si necesita que otra persona habilite los puntos de conexión de servicio en la subred, active la casilla **Omitir los puntos de conexión de servicio de Microsoft.Web que faltan**. La aplicación se configurará para los puntos de conexión de servicio en previsión de que se habiliten posteriormente en la subred. 

![Captura de pantalla del panel "Agregar restricción de IP" con el tipo Virtual Network seleccionado.](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

No puede usar puntos de conexión de servicio para restringir el acceso a las aplicaciones que se ejecutan en un entorno App Service Environment. Si la aplicación está en un entorno App Service Environment, puede controlar el acceso a ella con reglas de acceso de IP. 

Para más información sobre cómo configurar los puntos de conexión de servicio, consulte [Establecimiento del acceso a un sitio privado de Azure Functions](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Integración de la red virtual

La integración de red virtual permite que la aplicación de funciones acceda a recursos dentro de una red virtual.
Azure Functions admite dos tipos de integración de redes virtuales:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

La integración de red virtual de Azure Functions usa una infraestructura compartida con las aplicaciones web de App Service. Para obtener más información sobre los dos tipos de integración de red virtual, vea:

* [Integración de red virtual regional](../app-service/overview-vnet-integration.md#regional-virtual-network-integration)
* [Integración de red virtual con requisito de puerta de enlace](../app-service/overview-vnet-integration.md#gateway-required-virtual-network-integration)

Para obtener información sobre cómo configurar la integración de red virtual, consulte [Habilitación de la integración con red virtual](#enable-vnet-integration).

## <a name="enable-vnet-integration"></a>Habilitación de Integración con red virtual

1. Vaya a la hoja **Redes** en el portal de aplicación de funciones. En **Integración de red virtual**, seleccione **Haga clic aquí para configurar**.

1. Seleccione **Agregar VNET**.

    :::image type="content" source="./media/functions-networking-options/vnet-int-function-app.png" alt-text="Selección de Integración con red virtual":::

1. La lista desplegable contiene todas las redes virtuales de Azure Resource Manager de la suscripción en la misma región. Seleccione la VNet con la que quiere realizar la integración.

    :::image type="content" source="./media/functions-networking-options/vnet-int-add-vnet-function-app.png" alt-text="Seleccione la red virtual.":::

    * El plan premium de funciones solo admite la integración de red virtual regional. Si la VNet está en la misma región, cree una nueva subred o seleccione una subred vacía existente.
    * Para seleccionar una VNet de otra región, debe tener una puerta de enlace de VNet aprovisionada con la conectividad de punto a sitio habilitada. La integración de red virtual entre regiones solo se admite para los planes dedicados.

Durante la integración, la aplicación se reinicia. Una vez finalizada la integración, verá los detalles de la VNet con la que está integrada. De forma predeterminada, la opción Enrutar todo estará habilitada y todo el tráfico se enrutará a la red virtual.

Si desea que solo se enrute el tráfico privado (tráfico [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)), siga los pasos descritos en la [documentación de servicio de aplicaciones](../app-service/overview-vnet-integration.md#application-routing).

## <a name="regional-virtual-network-integration"></a>Integración de red virtual regional

Cuando se utiliza la versión regional de Integración con red virtual, la aplicación puede acceder a:

* Recursos en una VNet de la misma región que la aplicación.
* Recursos de las VNet emparejadas con la VNet con la que se integra la aplicación.
* Servicios protegidos mediante puntos de conexión de servicio.
* Recursos de diferentes conexiones de Azure ExpressRoute.
* Recursos de la VNet con la que está integrado.
* Recursos en conexiones emparejadas, lo que incluye conexiones de Azure ExpressRoute.
* Puntos de conexión privados 

Si Integración con red virtual se utiliza con redes virtuales de la misma región, se pueden usar las siguientes características de redes de Azure:

* **Grupos de seguridad de red (NSG)** : el tráfico saliente se puede bloquear con un grupo de seguridad de red que se encuentre en la subred de integración. Las reglas de entrada no se aplican, ya que Integración con red virtual no se puede usar para proporcionar acceso de entrada a la aplicación.
* **Tablas de enrutamiento (UDR)** : puede colocar una tabla de enrutamiento en la subred de integración para enviar el tráfico de salida donde quiera.

> [!NOTE]
> Si enruta todo el tráfico de salida a la VNet, este estará sujeto a los grupos de seguridad de red y las rutas definidas por los usuarios que se apliquen a la subred de integración. Cuando se integra VNet, el tráfico de salida de aplicación de funciones a las direcciones IP públicas sigue enviándose desde las direcciones que se muestran en las propiedades de la aplicación, a menos que proporcione rutas que dirijan el tráfico a otro lugar.
> 
> La integración de red virtual regional no puede usar el puerto 25.

Existen algunas limitaciones cuando se la característica Integración con red virtual se utiliza con redes virtuales que están en la misma región:

* No se puede acceder a los recursos en las conexiones de emparejamiento global.
* La característica está disponible en todas las unidades de escalado de App Service de nivel Premium V2 y Premium V3. También está disponible en Estándar, pero solo desde las unidades de escalado de App Service más recientes. Si está en una unidad de escalado anterior, solo puede usar la característica desde un plan de App Service Premium V2. Si quiere estar seguro de que puede usar la característica en un plan de App Service Estándar, cree la aplicación en un plan de App Service Premium V3. Estos planes solo se admiten en las unidades de escalado más recientes. Si quiere, después puede reducir verticalmente.
* La subred de integración solo puede usarla un plan de App Service.
* Las aplicaciones del plan Aislado que estén en una instancia de App Service Environment no pueden usar la característica.
* La característica requiere una subred sin usar que sea /28 o mayor en una red virtual de Azure Resource Manager.
* La aplicación y la VNet deben estar en la misma región.
* No puede eliminar una VNet con una aplicación integrada. Quite la integración antes de eliminar la VNet.
* Solo se puede tener una característica Integración con red virtual regional por plan de App Service. Varias aplicaciones en el mismo plan de App Service pueden usar la misma red virtual.
* No se puede cambiar la suscripción de una aplicación o un plan mientras haya una aplicación que use Integración con red virtual regional.
* La aplicación no puede resolver direcciones en Azure DNS Private Zones sin que se realicen cambios en la configuración.

## <a name="subnets"></a>Subredes

La integración con red virtual depende de una subred dedicada. Al aprovisionar una subred, la subred de Azure pierde cinco direcciones IP desde el inicio. Se usa una dirección de la subred de integración para cada instancia del plan. Si escala la aplicación a cuatro instancias, se usan cuatro direcciones. 

Al escalar o reducir verticalmente el tamaño, el espacio de direcciones necesario se duplica durante un breve período de tiempo. Esto afecta a las instancias admitidas reales y disponibles para un tamaño de subred determinado. En la tabla siguiente se muestran las direcciones máximas disponibles por bloque CIDR y el impacto que esto tiene en la escala horizontal:

| Tamaño de bloque CIDR | Número máximo de direcciones disponibles | Escala horizontal máxima (instancias)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Se da por supuesto que tendrá que escalar o reducir verticalmente el tamaño o la SKU en algún momento. 

Puesto que el tamaño de la subred no se puede cambiar después de la asignación, use una subred lo suficientemente grande como para dar cabida a cualquier escala que pueda alcanzar la aplicación. Para evitar problemas con la capacidad de subred de los planes Premium de Functions, debe usar un /24 con 256 direcciones para Windows y un /26 con 64 direcciones para Linux. 

Si quiere que las aplicaciones de otro plan lleguen a una VNet a la que ya están conectadas aplicaciones de otro plan, seleccione una subred distinta a la usada por la característica Integración con VNet ya existente.

La característica es totalmente compatible con aplicaciones para Windows y Linux, incluidos los [contenedores personalizados](../app-service/configure-custom-container.md). Todos los comportamientos actúan del mismo modo entre aplicaciones para Windows y Linux.

### <a name="service-endpoints"></a>Puntos de conexión del servicio

Con el fin de proporcionar un mayor nivel de seguridad, puede restringir una serie de servicios de Azure a una red virtual mediante puntos de conexión de servicio. La integración con red virtual regional permite que la aplicación de funciones llegue a los servicios de Azure protegidos con puntos de conexión de servicio. Esta configuración es compatible con todos los [planes](functions-scale.md#networking-features) que admiten la integración de redes virtuales. Para acceder a un servicio protegido mediante puntos de conexión de servicio, debe hacer lo siguiente:

1. Configure la integración de red virtual regional con la aplicación de funciones para conectarse a una subred específica.
1. Vaya al servicio de destino y configure los puntos de conexión de servicio en la subred de integración.

Para más información, consulte [Puntos de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="network-security-groups"></a>Grupos de seguridad de red

Puede usar grupos de seguridad de red para bloquear el tráfico de entrada y salida de los recursos de una VNet. Una aplicación que emplee la versión regional de Integración con red virtual puede usar un [grupo de seguridad de red][VNETnsg] para bloquear el tráfico de salida a los recursos de la VNet o Internet. Para bloquear el tráfico a direcciones públicas, debe tener habilitada la integración de red virtual con la opción Enrutar todo habilitada. Las reglas de entrada de un grupo de seguridad de red no se aplican a la aplicación, ya que Integración con red virtual solo afecta al tráfico saliente de la aplicación.

Para controlar el trafico de entrada a la aplicación, use la característica Restricciones de acceso. Un grupo de seguridad de red que se aplique a la subred de integración está en vigor, con independencia de las rutas aplicadas a la subred de integración. Si la aplicación de funciones está integrada con VNet mediante Enrutar todo habilitado, y no tiene ninguna ruta que afecte al tráfico de direcciones públicas en la subred de integración, todo el tráfico de salida sigue estando sujeto a los grupos de seguridad de red asignados a la subred de integración. Cuando Enrutar todo no está habilitado, los NSG solo se aplican al tráfico RFC1918.

### <a name="routes"></a>Rutas

Las tablas de enrutamiento se pueden usar para enrutar el tráfico de salida de la aplicación al lugar que se desee. De forma predeterminada, las tablas de enrutamiento solo afectan al tráfico de destino de RFC 1918. Cuando Enrutar todo está habilitado, todas las llamadas salientes se ven afectadas. Cuando [Enrutar todo](../app-service/overview-vnet-integration.md#application-routing) está deshabilitado, solo el tráfico privado (RFC1918) se ve afectado por sus tablas de enrutamiento. Las rutas que se establecen en la subred de integración no afectan a las respuestas a las solicitudes de entrada de la aplicación. Los destinos más habituales suelen ser puertas de enlace o dispositivos de firewall.

Si desea enrutar todo el tráfico de salida del entorno local, puede utilizar una tabla de rutas para enviar el tráfico de salida a la puerta de enlace de ExpressRoute. Si no enruta el tráfico a una puerta de enlace, asegúrese de establecer las rutas en la red externa para poder enviar de vuelta las respuestas.

Las rutas del Protocolo de puerta de enlace de borde (BGP) también afectan al tráfico de la aplicación. Si tiene rutas de BGP cuyo origen es algo similar a una puerta de enlace de ExpressRoute, el tráfico de salida de la aplicación se verá afectado. De forma predeterminada, las rutas de BGP solo afectan al tráfico de destino de RFC 1918. Cuando la aplicación de funciones está integrada en una red virtual y la opción Enrutar todo está habilitada, todo el tráfico saliente puede verse afectado por las rutas BGP.

### <a name="azure-dns-private-zones"></a>Zonas privadas de Azure DNS 

Una vez que la aplicación se integra con la red virtual, usa el mismo servidor DNS que el configurado para la red virtual. De forma predeterminada, la aplicación no funcionará con zonas privadas de Azure DNS. Para que lo haga es preciso agregar la siguiente configuración de la aplicación:

- `WEBSITE_VNET_ROUTE_ALL` con el valor `1`

Esta configuración envía todas las llamadas salientes desde la aplicación a la red virtual y permite que la aplicación tenga acceso a una zona privada de Azure DNS. Con esta configuración, su aplicación puede usar Azure DNS consultando la zona DNS privada en el nivel de trabajo.  

### <a name="private-endpoints"></a>Puntos de conexión privados

Si quiere realizar llamadas a [Puntos de conexión privados][privateendpoints], debe asegurarse de que las búsquedas de DNS se resuelvan en el punto de conexión privado. Puede aplicar este comportamiento de una de las siguientes formas: 

* Realizar la integración con zonas privadas de Azure DNS. Cuando la red virtual no tiene un servidor DNS personalizado, esto se hace automáticamente.
* Administrar el punto de conexión privado en el servidor DNS que usa la aplicación. Para ello, debe conocer la dirección del punto de conexión privado y, a continuación, apuntar el punto de conexión al que está intentando acceder a esa dirección con un registro A.
* Configurar su propio servidor DNS para reenviarlo a [zonas privadas de Azure DNS](#azure-dns-private-zones).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restricción de la cuenta de almacenamiento a una red virtual 

Al crear una aplicación de funciones, debe crear una cuenta de Azure Storage de uso general compatible con Blob, Queue y Table Storage, o vincular a una. Puede reemplazar esta cuenta de almacenamiento por una que esté protegida con puntos de conexión de servicio o puntos de conexión privados. 

Esta característica se admite para todas las SKU compatibles con la red virtual de Windows en el plan dedicado (App Service) y para el plan Premium. También se admite con DNS privado para SKU compatibles con la red virtual Linux. El plan de consumo y el DNS personalizado en los planes Linux no son compatibles. Para obtener información sobre cómo configurar una función con una cuenta de almacenamiento restringida a una red privada, consulte [Restricción de la cuenta de almacenamiento a una red virtual](configure-networking-how-to.md#restrict-your-storage-account-to-a-virtual-network).

## <a name="use-key-vault-references"></a>Uso de referencias de Key Vault

Puede usar las referencias de Azure Key Vault para utilizar secretos de esta solución en la aplicación Azure Functions sin necesidad de realizar cambios en el código. Azure Key Vault es un servicio que proporciona administración centralizada de los secretos, con control total sobre las directivas de acceso y el historial de auditoría.

Si la integración de la red virtual está configurada para la aplicación, se pueden usar [referencias de Key Vault](../app-service/app-service-key-vault-references.md) para recuperar secretos de un almacén restringido de red.

## <a name="virtual-network-triggers-non-http"></a>Desencadenadores de red virtual (no HTTP)

Actualmente, puede usar funciones de desencadenador no HTTP desde una red virtual de una de estas dos formas:

+ Ejecute la aplicación de funciones en un plan Premium y habilite la compatibilidad con el desencadenador de red virtual.
+ Ejecute una aplicación de funciones en un plan de App Service o App Service Environment.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plan Premium con desencadenadores de red virtual

Cuando cuenta con un plan Premium, puede conectar funciones de desencadenador no HTTP a los servicios que se ejecutan en una red virtual. Para ello, debe habilitar la compatibilidad con el desencadenador de red virtual para la aplicación de funciones. La configuración **Supervisión de la escala en tiempo de ejecución** se encuentra en [Azure Portal](https://portal.azure.com), en **Configuración** > **Configuración del tiempo de ejecución de la función**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

También puede habilitar los desencadenadores de red virtual mediante el siguiente comando de la CLI de Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> Habilitar los desencadenadores de red virtual puede afectar al rendimiento de la aplicación, ya que las instancias del plan de App Service deberán supervisar los desencadenadores para determinar cuándo se debe escalar. Es probable que este impacto sea muy pequeño.

Los desencadenadores de red virtual se admiten en la versión 2.x y superior del tiempo de ejecución de Functions. Se admiten los siguientes tipos de desencadenador no HTTP.

| Extensión | Versión mínima |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 o superior |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 o superior|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 o superior|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 o superior|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 o superior|

> [!IMPORTANT]
> Cuando se habilita la compatibilidad con desencadenadores de red virtual, solo los tipos de desencadenador incluidos en la tabla anterior se escalan dinámicamente con la aplicación. Podrá seguir usando desencadenadores que no figuran en la tabla, pero no se escalarán más allá de su recuento de instancias activadas previamente. Consulte la lista completa de los desencadenadores en [Enlaces admitidos](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plan de App Service y App Service Environment con desencadenadores de red virtual

Cuando la aplicación de funciones se ejecuta en un plan de App Service o en App Service Environment, puede usar funciones de desencadenador no HTTP. Para que las funciones se desencadenen correctamente, debe estar conectado a una red virtual con acceso al recurso definido en la conexión del desencadenador.

Por ejemplo, imagine que quiere configurar Azure Cosmos DB para aceptar el tráfico solo desde una red virtual. En este caso, debe implementar la aplicación de funciones en un plan de App Service que proporcione integración de red virtual con esa red virtual. La integración permite que un recurso de Azure Cosmos DB desencadene una función.

## <a name="hybrid-connections"></a>conexiones híbridas

[Conexiones híbridas](../azure-relay/relay-hybrid-connections-protocol.md) es una característica de Azure Relay que se puede usar para acceder a recursos de la aplicación en otras redes. Proporciona acceso desde la aplicación a un punto de conexión de la aplicación. No se puede usar para acceder a la aplicación. Las conexiones híbridas están disponibles en funciones que se ejecutan Windows con todos los planes, excepto el plan Consumo.

Dado que se usa en Azure Functions, cada conexión híbrida se correlaciona con una combinación única de host y puerto TCP. Esto significa que el punto de conexión de la conexión híbrida puede estar en cualquier sistema operativo y en cualquier aplicación, siempre que se acceda a un puerto de escucha TCP. La característica Conexiones híbridas no sabe lo que es el protocolo de aplicación ni a qué se accede. Simplemente ofrece acceso a la red.

Para obtener más información, vea la [documentación de App Service para Conexiones híbridas](../app-service/app-service-hybrid-connections.md). Estos mismos pasos de configuración sirven para Azure Functions.

>[!IMPORTANT]
> Las conexiones híbridas solo se admiten en los planes de Windows. Linux no se admite.

## <a name="outbound-ip-restrictions"></a>Restricciones de IP de salida

Las restricciones de IP de salida solo están disponibles para el plan Premium, el plan de App Service o App Service Environment. Puede configurar las restricciones de salida de la red virtual en la que está implementado el App Service Environment.

Cuando se integra una aplicación de funciones de un plan Premium o un plan de App Service con una red virtual, la aplicación todavía puede realizar llamadas salientes a Internet de forma predeterminada. Al integrar la aplicación de funciones con una red virtual con Enrutar todo habilitado, se fuerza el envío de todo el tráfico saliente a la red virtual, donde se pueden usar reglas de grupo de seguridad de red para restringir el tráfico.

Para obtener información sobre cómo controlar la IP de salida mediante una red virtual, consulte [Tutorial: Control de la IP de salida de Azure Functions mediante un servicio NAT Gateway de Azure Virtual Network](functions-how-to-use-nat-gateway.md). 

## <a name="automation"></a>Automation
Las siguientes API permiten administrar mediante programación las integraciones de redes virtuales regionales:

+ **CLI de Azure**: Use los comandos [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) para agregar, enumerar o quitar una integración de red virtual regional.  
+ **Plantillas ARM**: La integración de la red virtual regional se puede habilitar mediante una plantilla de Azure Resource Manager. Para obtener un ejemplo completo, consulte [esta plantilla de inicio rápido de Functions](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Solución de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las redes y Azure Functions:

* [Siga el tutorial sobre cómo empezar con la integración de red virtual](./functions-create-vnet.md)
* [Lea las preguntas más frecuentes de las redes de Functions](./functions-networking-faq.yml)
* [Obtenga más información sobre la integración de red virtual con App Service o Functions](../app-service/overview-vnet-integration.md)
* [Obtenga más información sobre las redes virtuales en Azure](../virtual-network/virtual-networks-overview.md)
* [Habilite más características de redes y control con App Service Environment](../app-service/environment/intro.md)
* [Conéctese a recursos locales individuales mediante Conexiones híbridas sin modificar el firewall](../app-service/app-service-hybrid-connections.md)


<!--Links-->
[VNETnsg]: ../virtual-network/network-security-groups-overview.md
[privateendpoints]: ../app-service/networking/private-endpoint.md