---
title: Configuración de conectividad en Azure Virtual Network Manager (versión preliminar)
description: Obtenga información sobre diferentes tipos de topología de red que puede crear con una configuración de conectividad en Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: bf7adbb0fd54243e71f6e313892f3f418071c217
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719175"
---
# <a name="connectivity-configuration-in-azure-virtual-network-manager-preview"></a>Configuración de conectividad en Azure Virtual Network Manager (versión preliminar)

En este artículo, aprenderá sobre los distintos tipos de configuraciones que puede crear e implementar mediante Azure Virtual Network Manager. Hay dos tipos de configuraciones disponibles actualmente: *Administración de conectividad* y *seguridad*. 

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-configuration"></a>Configuración de conectividad

Las configuraciones de *conectividad* permiten crear diferentes topologías de red en función de sus necesidades de red. Tiene dos topologías entre las que elegir, una *red de malla* y un *centro de conectividad y radio*. Las conexiones entre redes virtuales se definen dentro de las opciones de configuración.

## <a name="mesh-network-topology"></a>Topología de red de malla

Una red de malla es una topología en la que todas las redes virtuales del [grupo de redes](concept-network-groups.md) están conectadas entre sí. Todas las redes virtuales están conectadas y pueden pasar tráfico bidireccionalmente entre sí. De forma predeterminada, la malla es una malla regional, por lo que solo las redes virtuales de la misma región pueden comunicarse entre sí. **La malla** global se puede habilitar para establecer la conectividad de redes virtuales en todas las regiones de Azure. Una red virtual puede formar parte de hasta cinco grupos conectados. Los espacios de direcciones de red virtual no se pueden superponer en una configuración o, de lo contrario, los recursos no se pueden comunicar entre sí debido a conflictos de red.

:::image type="content" source="./media/concept-configuration-types/mesh-topology.png" alt-text="Diagrama de una topología de red.":::

### <a name="connected-group"></a><a name="connectedgroup"></a> Grupo conectado

Cuando se crea una topología de malla, se crea una nueva construcción de conectividad denominada *Grupo conectado*. Las redes virtuales de un grupo conectado pueden comunicarse entre sí como si se conectara manualmente a las redes virtuales. Cuando vea las rutas eficaces de una interfaz de red, verá un tipo de próximo salto **ConnectedGroup**. La red virtual conectada en un grupo conectado no tendrá una configuración de emparejamiento en *Emparejamientos* para la red virtual.

> [!NOTE]
> * Si tiene subredes en conflicto en dos o más redes virtuales, los recursos de esas subredes *no* podrán comunicarse entre sí aunque forme parte de la misma red de malla.
> * Una red virtual puede formar parte de hasta **dos** configuraciones de malla.

## <a name="hub-and-spoke-topology"></a>Topología de red en estrella tipo hub-and-spoke

Un centro de conectividad y radio es una topología de red en la que tiene una red virtual seleccionada como red virtual del centro de conectividad. Esta red virtual se empareja bidireccionalmente con cada red virtual de radio de la configuración. Esta topología es útil cuando desea aislar una red virtual, pero quiere que tenga conectividad con recursos comunes de la red virtual del centro. 

En esta configuración, tiene opciones que puede habilitar, como la *conectividad directa* entre redes virtuales de radio. De forma predeterminada, esta conectividad es solo para redes virtuales de la misma región. Para permitir la conectividad entre diferentes regiones de Azure, deberá habilitar la *malla global*. También puede habilitar el tránsito de *puerta de enlace* para permitir que las redes virtuales de radio usen la VPN o la puerta de enlace de ExpressRoute implementada en el centro.

### <a name="direct-connectivity"></a>Conectividad directa

Al habilitar la *conectividad directa,* se crea un [*grupo conectado*](#connectedgroup) que contiene redes virtuales de radio con la misma región. Esta conexión solo se establece para las redes virtuales del mismo grupo de red. 

Por ejemplo, se crean dos grupos de red. La conectividad directa se habilita para el grupo de red de *producción*, pero no para el grupo de red de *prueba*. Esta configuración solo permite que las redes virtuales del grupo de redes de *producción* se comuniquen entre sí, pero no con las del grupo de red de *prueba*. 

Consulte el diagrama de ejemplo siguiente:

:::image type="content" source="./media/concept-configuration-types/hub-and-spoke.png" alt-text="Diagrama de una topología de centro de conectividad y radio con dos grupos de red.":::

Cuando se observan rutas eficaces en una máquina virtual, la ruta entre las redes virtuales del centro de conectividad y del radio tendrá el tipo de próximo salto *VNetPeering* o *GlobalVNetPeering.* Las rutas entre redes virtuales de radios se mostrarán con el tipo de próximo salto *ConnectedGroup*. Con el ejemplo anterior, solo el grupo de red de *producción* tendría un *ConnectedGroup* porque tiene habilitada la *Conectividad directa*.

> [!NOTE]
> El espacio de direcciones de red del centro de conectividad se agrega a *ConnectedGroup* cuando la opción *Conectividad directa* está **habilitada**. Por lo tanto, si se produce un error en el emparejamiento de red virtual entre el centro de conectividad y la red virtual de radio, pueden seguir comunicándose mediante *ConnectedGroup*.

#### <a name="use-cases"></a>Casos de uso

La habilitación de la conectividad directa entre redes virtuales de radios puede ser útil cuando se desea tener una NVA o un servicio común en la red virtual del centro, pero no es necesario tener acceso siempre al centro. Pero, en su lugar, necesita que las redes virtuales de radio del grupo de red se comuniquen entre sí. En comparación con las redes de centro de conectividad y radio tradicionales, esta topología mejora el rendimiento mediante la eliminación del salto adicional a través de la red virtual del centro.

### <a name="global-mesh"></a>Malla global

La malla global es necesaria cuando desea que las redes virtuales de radio se comuniquen entre sí entre regiones. Esta conectividad se limita a la red virtual del mismo grupo de red. Para habilitar la conectividad de redes virtuales entre regiones, deberá **Habilitar la conectividad de malla entre regiones** para el grupo de red. Las conexiones creadas entre redes virtuales de radios están en un [*Grupo conectado*](#connectedgroup). 

### <a name="use-hub-as-a-gateway"></a>Uso del centro de conectividad como puerta de enlace

Otra opción que puede habilitar en una configuración de centro de conectividad y radio es usar el centro como puerta de enlace. Esta configuración permitirá que todas las redes virtuales del grupo de redes usen la VPN o la puerta de enlace de ExpressRoute en la red virtual del centro de conectividad para pasar el tráfico. Consulte [Puertas de enlace y conectividad local](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity).

Al implementar una topología de centro de conectividad y radio desde el Azure Portal, **Usar el centro de conectividad como puerta de enlace** está habilitado de forma predeterminada para las redes virtuales de radio en el grupo de red. Azure Virtual Network Manager intentará crear una conexión de emparejamiento de red virtual entre el centro y la red virtual de radios en el grupo de recursos. Si la puerta de enlace no existe en la red virtual del centro de conectividad, se producirá un error en la creación del emparejamiento desde la red virtual de radio al centro de conectividad. La conexión de emparejamiento del centro de conectividad al radio se seguirá creando sin una conexión establecida. 

> [!NOTE]
> Si habilita la *Conectividad directa* para el grupo de redes de radios, el centro de conectividad también se incluirá en el grupo conectado. Por lo tanto, incluso si no se crea la conexión de emparejamiento desde el radio al centro de conectividad, las redes virtuales de radio pueden comunicarse con la red virtual del centro a través del grupo conectado.
>

## <a name="next-steps"></a>Pasos siguientes

- Cree una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md).
- Obtenga información sobre las [implementaciones de configuración](concept-deployments.md) en Azure Virtual Network Manager.
- Obtenga información sobre cómo bloquear el tráfico de red con una [configuración de administración de seguridad](how-to-block-network-traffic-portal.md).