---
title: Direcciones IP públicas en Azure
titleSuffix: Azure Virtual Network
description: Obtenga más información sobre las direcciones IP públicas en Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: allensu
ms.openlocfilehash: 4497b58e40ccc280661d45932586c14bb8a21108
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179882"
---
# <a name="public-ip-addresses"></a>Direcciones IP públicas

Las direcciones IP públicas permiten a los recursos de Internet la comunicación entrante a los recursos de Azure. Permiten que los recursos de Azure se comuniquen con los servicios de Azure orientados al público e Internet. Hasta que cancele la asignación, la dirección estará dedicada al recurso. Un recurso sin una dirección IP pública asignada puede realizar comunicaciones salientes. Azure asigna dinámicamente una dirección IP disponible que no está dedicada al recurso. Para más información acerca de las conexiones salientes en Azure, consulte [Comprender las conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

En el Administrador de recursos de Azure, una dirección [IP pública](virtual-network-public-ip-address.md) es un recurso que cuenta con propiedades específicas. Estos son algunos de los recursos con los que puede asociar un recurso de dirección IP pública:

* Interfaces de red de máquinas virtuales
* Equilibradores de carga accesibles desde Internet
* Puertas de enlace de red virtual (VPN/ER)
* Puertas de enlace NAT
* Puertas de enlace de aplicaciones
* Azure Firewall
* Host bastión

Para conjuntos de escalado de máquinas virtuales, use [prefijos de dirección IP pública](public-ip-address-prefix.md).

## <a name="at-a-glance"></a>De un vistazo

En la siguiente tabla se muestra la propiedad con la que una dirección IP pública se puede asociar a un recurso y los métodos de asignación. Tenga en cuenta que la compatibilidad con IPv6 pública no está disponible para todos los tipos de recurso en este momento.

| Recurso de nivel superior | Asociación de dirección IP | IPv4 dinámico | IPv4 estático | IPv6 dinámico | IPv6 estático |
| --- | --- | --- | --- | --- | --- |
| Máquina virtual |interfaz de red |Sí | Sí | Sí | Sí |
| Equilibrador de carga accesible desde Internet |Configuración de front-end |Sí | Sí | Sí |Sí |
| Puerta de enlace de red virtual (VPN) |Configuración de dirección IP de puerta de enlace |Sí (solo sin AZ) |Sí (solo AZ) | No |No |
| Puerta de enlace de red virtual (ER) |Configuración de dirección IP de puerta de enlace |Sí | No | Sí (versión preliminar) |No |
| NAT Gateway |Configuración de dirección IP de puerta de enlace |No |Sí | No |No |
| puerta de enlace de aplicaciones |Configuración de front-end |Sí (solo en V1) |Sí (solo en V2) | No | No |
| Azure Firewall | Configuración de front-end | No | Sí | No | No |
| Host bastión | Configuración de IP pública | No | Sí | No | No |

## <a name="ip-address-version"></a>Versión de la dirección IP

Las direcciones IP públicas se pueden crear con una dirección IPv4 o IPv6. Es posible que tenga la opción de crear una implementación de doble pila con una dirección IPv4 e IPv6.

## <a name="sku"></a>SKU

Las direcciones IP públicas se crean con una de las SKU siguientes:

### <a name="standard"></a>Estándar

Las direcciones IP públicas de SKU estándar:

- Utilice siempre el método de asignación estática.
- Tiene un tiempo espera de inactividad del flujo originado de entrada ajustable de entre 4 y 30 minutos, y un valor predeterminado de 4 minutos, y el valor predeterminado del tiempo de espera del flujo originado es de 4 minutos.
- Son seguras de forma predeterminada y se cierran al tráfico de entrada. Permiten mostrar el tráfico de entrada con un [grupo de seguridad de red](./network-security-groups-overview.md#network-security-groups).
- Se asignan a interfaces de red, equilibradores de carga estándar públicos o puertas de enlace de aplicaciones. Para más información sobre Azure Load Balancer, consulte [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Pueden tener redundancia de zona (es decir, se anuncian desde las tres zonas), ser zonales (es decir, se garantizan en una zona de disponibilidad preseleccionada específica) o no tener zona (es decir, no se asocian a ninguna zona de disponibilidad preseleccionada concreta). Para obtener más información acerca de las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Standard Load Balancer and Availability Zones](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Load Balancer Standard y zonas de disponibilidad). **Las direcciones IP con redundancia de zona solo se pueden crear en [regiones en las que haya tres zonas de disponibilidad](../availability-zones/az-region.md) activas.** Las direcciones IP creadas antes de que las zonas estén activas no tendrán redundancia de zona.
- Se puede usar con la [preferencia de enrutamiento](routing-preference-overview.md) para permitir un control más detallado de cómo se enruta el tráfico entre Azure e Internet.
- Se puede usar como dirección IP de front-end de difusión por proximidad (anycast) para [equilibradores de carga entre regiones](../load-balancer/cross-region-overview.md) (funcionalidad de versión preliminar).
 
> [!NOTE]
> Para evitar que se produzca un error en la comunicación de entrada con el recurso SKU estándar, debe crear un [grupo de seguridad de red](./network-security-groups-overview.md#network-security-groups), asociarlo y permitir explícitamente el tráfico de entrada deseado.

> [!NOTE]
> Cuando se usa el [servicio de metadatos de instancia IMDS](../virtual-machines/windows/instance-metadata-service.md), solo hay disponibles direcciones IP públicas con SKU básica. No se admiten las SKU estándar.

> [!NOTE]
> La configuración de diagnóstico no aparece en la hoja del recurso cuando se usa una dirección IP pública de SKU estándar. Para habilitar el registro en un recurso de dirección IP pública estándar, vaya a la configuración de diagnóstico en la hoja Azure Monitor y seleccione el recurso de dirección IP.

### <a name="basic"></a>Básica

Las direcciones de SKU básica:

- Para IPv4: se pueden asignar mediante el método de asignación dinámica o estática.  Para IPv6: solo se pueden asignar mediante el método de asignación dinámica.
- Tiene un tiempo espera de inactividad del flujo originado de entrada ajustable de entre 4 y 30 minutos, y un valor predeterminado de 4 minutos, y el valor predeterminado del tiempo de espera del flujo originado es de 4 minutos.
- Están abiertas de forma predeterminada.  Se recomienda el uso de grupos de seguridad de red, pero es opcional para restringir el tráfico de entrada o de salida.
- No admiten escenarios de zona de disponibilidad. Use la dirección IP pública de SKU estándar para los escenarios de zona de disponibilidad en las regiones correspondientes. Para obtener más información acerca de las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Standard Load Balancer and Availability Zones](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Load Balancer Standard y zonas de disponibilidad).
- No admiten las funcionalidades de [preferencia de enrutamiento](routing-preference-overview.md) ni [equilibradores de carga entre regiones](../load-balancer/cross-region-overview.md).

> [!NOTE]
> Las direcciones IPv4 de SKU básica se pueden actualizar después de su creación a una SKU estándar.  Para obtener información acerca de la actualización de SKU, consulte [Actualización de IP pública](./public-ip-upgrade-portal.md).

>[!IMPORTANT]
> En los recursos de IP pública y en el equilibrador de carga se requieren SKU coincidentes. No puede tener una combinación de recursos de SKU básica y recursos de SKU estándar. No se pueden asociar máquinas virtuales independientes, máquinas virtuales en un recurso de conjunto de disponibilidad o conjuntos de escalado de máquinas virtuales a ambas SKU simultáneamente.  Para los nuevos diseños, deberá considerar usar los recursos de SKU estándar.  Consulte [Load Balancer Estándar](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obtener más información.

## <a name="ip-address-assignment"></a>Asignación de dirección IP

 Tanto las direcciones IPv4 públicas estándar, IPv4 públicas básicas como IPv6 públicas estándar admiten la asignación **estática**.  Al recurso se le asigna una dirección IP cuando aquel se crea. La dirección IP se libera cuando el recurso se elimina.  

> [!NOTE]
> Incluso cuando se establece el método de asignación en **estático**, no se puede especificar la dirección IP real asignada al recurso de dirección IP pública. Azure asigna la dirección IP desde un grupo de direcciones IP disponibles en la ubicación de Azure cuando se crea el recurso.
>

Las direcciones IP públicas se suelen usar en los escenarios siguientes:

* Cuando debe actualizar las reglas de firewall para comunicarse con los recursos de Azure.
* La resolución de nombres DNS, en la que un cambio de dirección IP requeriría actualizar los registros D.
* Los recursos de Azure se comunican con otras aplicaciones o servicios que utilizan un modelo de seguridad basado en dirección IP.
* Use certificados TLS/SSL vinculados a una dirección IP.

Las direcciones IPv4 e IPv6 públicas básicas admiten una asignación **dinámica**.  La dirección IP **no** se asigna al recurso en el momento de crearse cuando se selecciona el método dinámico.  La dirección IP se asigna cuando se asocia la dirección IP pública a un recurso. La dirección IP se libera cuando se detiene o elimina el recurso.   Por ejemplo, un recurso de dirección IP pública se libera de un recurso denominado **Recurso A**. **Recurso A** recibe una dirección IP diferente en el inicio si el recurso de dirección IP pública se vuelve a asignar. Una dirección IP se libera si se cambia el método de asignación de **estático** a **dinámico**. Establezca el método de asignación en **estático** para asegurarse de que la dirección IP siga siendo igual.

> [!NOTE]
> Azure asigna direcciones IP públicas a partir de un rango único para cada región en cada nube de Azure. Puede descargar la lista de intervalos (prefijos) para las nubes de Azure [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [Gobierno de Estados Unidos](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) y [Alemania](https://www.microsoft.com/download/details.aspx?id=57064).
>

## <a name="dns-name-label"></a>Etiqueta de nombre DNS

Seleccione esta opción para especificar una etiqueta de DNS para un recurso de dirección IP pública. Esta funcionalidad funciona tanto para direcciones IPv4 (registros A de 32 bits) como para direcciones IPv6 (registros AAAA de 128 bits).

### <a name="dns-hostname-resolution"></a>Resolución de nombres de host DNS

Esta selección crea una asignación para **domainnamelabel**.**location**.cloudapp.azure.com a la dirección IP pública en el DNS administrado por Azure. 

Por ejemplo, la creación de una dirección IP pública con:

* **contoso** como **domainnamelabel**
* **Ubicación** de Azure **Oeste de EE. UU.**

El nombre de dominio completo (FQDN) **contoso.westus.cloudapp.azure.com** se resuelve en la dirección IP pública del recurso.

> [!IMPORTANT]
> Cada etiqueta de nombre de dominio que se cree debe ser única dentro de su ubicación de Azure.  

### <a name="dns-recommendations"></a>Recomendaciones de DNS

No se puede migrar el FQDN de la dirección IP pública entre regiones. Puede usar el nombre de dominio completo para crear un registro CNAME personalizado que apunte a la dirección IP pública. Si se requiere un traslado a una dirección IP pública diferente, actualice el registro CNAME.

Puede usar [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) o un proveedor de DNS externo para el registro DNS.

## <a name="other-public-ip-address-features"></a>Otras características de dirección IP pública

Hay otros atributos que se pueden usar para una dirección IP pública.  

* El **Nivel** global permite usar una dirección IP pública con equilibradores de carga entre regiones. 
* La opción **Preferencia de enrutamiento** por Internet minimiza el tiempo que el tráfico pasa en la red de Microsoft, lo que reduce el costo de transferencia de los datos de salida.

> [!NOTE]
> En este momento, las características **Nivel** y **Preferencia de enrutamiento** solo están disponibles para direcciones IPv4 de SKU estándar.  Tampoco se pueden usar en la misma dirección IP simultáneamente.
>

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="limits"></a>Límites 

Los límites de una dirección IP se encuentran en el conjunto completo de [límites de red](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) de Azure. Los límites son por región y suscripción. [Póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar los límites predeterminados según sus necesidades empresariales.

## <a name="pricing"></a>Precios

Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP en Azure, revise la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="limitations-for-ipv6"></a>Limitaciones para IPv6

* Las puertas de enlace de VPN no se pueden usar en una red virtual que tiene IPv6 habilitado, ya sea directamente o emparejadas con "UseRemoteGateway".
* Las direcciones IPv6 públicas se bloquean con un tiempo de espera de inactividad de 4 minutos.
* Azure no admite la comunicación de IPv6 para contenedores.
* No se admite el uso de máquinas virtuales ni conjuntos de escalado de máquinas virtuales solo con IPv6. Cada NIC debe incluir al menos una configuración IP de IPv4 (pila doble).
* Al agregar IPv6 a las implementaciones IPv4 existentes, los rangos de IPv6 no se pueden agregar a una red virtual con vínculos de navegación de recursos existentes.
* El reenvío de DNS para IPv6 es compatible con DNS público de Azure. No se admite el DNS inverso.
* No se admiten la preferencia de enrutamiento y el equilibrio de carga entre regiones.

Para más información sobre IPv6 en Azure, consulte [aquí](./ipv6-overview.md).

## <a name="next-steps"></a>Pasos siguientes
* Obtener información sobre las [direcciones IP privadas en Azure](private-ip-addresses.md)
* [Implementar una máquina virtual con una dirección IP pública estática mediante el portal de Azure](virtual-network-deploy-static-pip-arm-portal.md)
