---
title: ¿Qué es Azure Virtual Network IP Services?
description: Introducción a Azure Virtual Network IP Services. Obtenga información sobre cómo funcionan los servicios de IP y cómo usar los recursos de IP en Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subService: ip-services
ms.topic: overview
ms.date: 10/01/2021
ms.custom: template-overview
ms.openlocfilehash: 4215fb4682e29a06a7003e30ae53656e09f6f246
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369801"
---
# <a name="what-is-azure-virtual-network-ip-services"></a>¿Qué es Azure Virtual Network IP Services?

Los servicios de IP son una colección de servicios relacionados con direcciones IP que permiten la comunicación en una instancia de Azure Virtual Network. Las direcciones IP públicas y privadas se usan en Azure para la comunicación entre los recursos. La comunicación con los recursos puede producirse en una instancia de Azure Virtual Network y en la red pública de Internet.

Los servicios de IP constan de:

* Direcciones IP públicas

* Prefijos de direcciones IP públicas

* Direcciones IP privadas

* Preferencia de enrutamiento

* Preferencias de enrutamiento no medido

## <a name="public-ip-addresses"></a>Direcciones IP públicas

Los recursos de Internet usan las direcciones IP públicas para realizar comunicaciones de entrada con los recursos de Azure. Las direcciones IP públicas se pueden crear con una dirección IPv4 o IPv6. Es posible que tenga la opción de crear una implementación de doble pila con una dirección IPv4 e IPv6. Las direcciones IP públicas están disponibles en dos SKU: **estándar** y **básica**. Las direcciones IP públicas pueden ser estáticas o asignadas dinámicamente.

Una dirección IP pública es un recurso con sus propias propiedades. Estos son algunos de los recursos con los que puede asociar las direcciones IP públicas:

* Interfaces de red de máquinas virtuales
* Equilibradores de carga accesibles desde Internet
* Puertas de enlace de red virtual (VPN/ER)
* Puertas de enlace NAT
* Puertas de enlace de aplicaciones
* Azure Firewall
* Host bastión

Para más información sobre las direcciones IP públicas, consulte [Direcciones IP públicas](../../virtual-network/public-ip-addresses.md) y [Creación, cambio o eliminación de una dirección IP pública de Azure](../../virtual-network/virtual-network-public-ip-address.md).

## <a name="public-ip-address-prefixes"></a>Prefijos de direcciones IP públicas

Los prefijos de direcciones IP públicas son intervalos reservados de direcciones IP en Azure. Los prefijos de dirección IP pública constan de direcciones IPv4 o IPv6.  En las regiones con Availability Zones, los prefijos de dirección IP pública se pueden crear como con prefijos con redundancia de zona o asociados a una zona de disponibilidad específica. Después de crear el prefijo de IP pública, puede crear direcciones IP públicas.

Están disponibles los tamaños de prefijo de IP pública siguientes:

-  /28 (IPv4) o /124 (IPv6) = 16 direcciones
-  /29 (IPv4) o /125 (IPv6) = 8 direcciones
-  /30 (IPv4) o /126 (IPv6) = 4 direcciones
-  /31 (IPv4) o /127 (IPv6) = 2 direcciones

El tamaño del prefijo se especifica como un tamaño de máscara de enrutamiento de interdominios sin clases (CIDR).

No hay límites en cuanto al número de prefijos creados en una suscripción. El número de intervalos creados no puede superar el límite de direcciones IP públicas estáticas permitido en su suscripción. Para más información, consulte [Límites de Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Para más información sobre los prefijos de direcciones IP públicas, consulte [Prefijo de direcciones IP públicas](../../virtual-network/public-ip-address-prefix.md) y [Creación, cambio o eliminación de un prefijo de dirección IP pública](../../virtual-network/manage-public-ip-address-prefix.md).

## <a name="private-ip-addresses"></a>Direcciones IP privadas

Las direcciones IP privadas permiten la comunicación entre los recursos de Azure. Azure asigna direcciones IP privadas a recursos del intervalo de direcciones de la subred de la red virtual en la que se encuentra el recurso. Las direcciones IP privadas en Azure son estáticas o se asignan dinámicamente.

Estos son algunos de los recursos con los que puede asociar las direcciones IP privadas:

* Máquinas virtuales
* Equilibradores de carga internos
* Puertas de enlace de aplicaciones
* Puntos de conexión privados

Para más información sobre las direcciones IP privadas, consulte [Direcciones IP privadas](../../virtual-network/private-ip-addresses.md).

## <a name="routing-preference"></a>Preferencia de enrutamiento

Las preferencias de enrutamiento de Azure le permiten elegir cómo se enruta el tráfico entre Azure e Internet. Puede optar por enrutar el tráfico a través de la red de Microsoft o a través de una red de ISP (Internet público). Puede elegir la opción de enrutamiento al crear una dirección IP pública. De forma predeterminada, el tráfico se enruta a través de la red global de Microsoft para todos los servicios de Azure. 

Las opciones de preferencias de enrutamiento incluyen:

* **Microsoft Network**: tanto el tráfico de entrada como el de salida se mantiene la mayor parte del tiempo en la red global de Microsoft. Este enrutamiento también se conoce como enrutamiento de tipo *"cold potato"* .

* **Internet pública (red de ISP)** : la nueva opción de enrutamiento denominada enrutamiento de Internet minimiza el viaje a través de la red global de Microsoft y utiliza la red de ISP de tránsito para enrutar el tráfico. Este enrutamiento también se conoce como enrutamiento de tipo *"hot potato"* .

Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento?](../../virtual-network/routing-preference-overview.md)

## <a name="routing-preference-unmetered"></a>Preferencias de enrutamiento no medido

La preferencia de enrutamiento no medido está disponible para los proveedores de Content Delivery Network (CDN) cuyos clientes hospedan su contenido de origen en Azure. El servicio permite a los proveedores de CDN establecer una conexión de emparejamiento directo con los enrutadores perimetrales de la red global de Microsoft en varias ubicaciones.

La salida del tráfico de red desde el origen en Azure destinado al proveedor de CDN se beneficia de la conectividad directa.

* La factura de transferencia de datos para el tráfico de salida de los recursos de Azure que se enrutan mediante estos vínculos directos es gratuita.

* La conexión directa entre el proveedor de CDN y el origen en Azure proporciona un rendimiento óptimo, ya que no hay saltos entre ellos. Esta conexión beneficia a la carga de trabajo de la red CDN, que recupera datos del origen con frecuencia.

Para obtener más información sobre la preferencia de enrutamiento no medido, vea [¿Qué es la preferencia de enrutamiento no medido?](../../virtual-network/routing-preference-unmetered.md)

## <a name="next-steps"></a>Pasos siguientes

Introducción a la creación de recursos de servicios IP:

- [Creación de una dirección IP pública mediante Azure Portal](../../virtual-network/create-public-ip-portal.md).
- [Creación de un prefijo de dirección IP pública mediante Azure Portal](../../virtual-network/create-public-ip-prefix-portal.md).
- [Configuración de una dirección IP privada para una VM mediante Azure Portal](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).
- [Configuración de la preferencia de enrutamiento para una dirección IP pública mediante Azure Portal](../../virtual-network/routing-preference-portal.md).
