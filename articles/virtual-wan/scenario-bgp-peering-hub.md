---
title: Acerca del emparejamiento BGP con un centro virtual
titleSuffix: Azure Virtual WAN
description: Obtenga información sobre el emparejamiento BGP con un centro virtual de Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 1f57d48d2fecded727e4087a138fa67c514bea0e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781136"
---
# <a name="scenario-bgp-peering-with-a-virtual-hub-preview"></a>Escenario: emparejamiento BGP con un centro virtual (versión preliminar)

El enrutador del centro de Azure Virtual WAN, también llamado enrutador del centro virtual, actúa como administrador de rutas y proporciona simplificación en las operaciones de enrutamiento dentro y entre centros virtuales. En otras palabras, el enrutador del centro virtual hace lo siguiente:

* Simplifica la administración del enrutamiento al ser el motor de enrutamiento central que se comunica con puertas de enlace como VPN, ExpressRoute, P2S y aplicaciones virtuales de red (NVA). 
* Permite escenarios de enrutamiento avanzado de tablas de rutas personalizadas, asociación y propagación de rutas.
* Actúa como enrutador para el tráfico que transita entre las redes virtuales conectadas a un centro virtual o hacia estas.

El enrutador del centro virtual ahora también expone la capacidad de emparejarse con él, lo que hace que intercambie la información de enrutamiento directamente mediante el protocolo de enrutamiento Protocolo de puerta de enlace de borde (BGP). Una NVA o un punto de conexión BGP aprovisionados en una red virtual conectada a un centro virtual pueden emparejarse directamente con el enrutador del centro virtual si admiten el protocolo de enrutamiento BGP y se garantiza que el ASN de la NVA está configurado para que sea diferente del ASN del centro virtual.

## <a name="benefits-and-considerations"></a>Ventajas y consideraciones

**Ventajas principales**

* Ya no es necesario actualizar manualmente la tabla de enrutamiento en la NVA cada vez que se actualicen las direcciones de la red virtual.
* Ya no es necesario actualizar manualmente las rutas definidas por el usuario cada vez que la NVA anuncie nuevas rutas o retire las anteriores.
* Una NVA que se encuentre en redes virtuales conectadas a un centro virtual puede aprender las rutas de la puerta de enlace del centro virtual (VPN, ExpressRoute o NVA administrada).
* Puede emparejar varias instancias de la NVA con el enrutador de un centro virtual. Puede configurar los atributos de BGP en la NVA y, en función del diseño (activo-activo o activo-pasivo), dejar que el enrutador del centro virtual sepa qué instancia de la NVA es activa o pasiva.

**Consideraciones**

* No se puede emparejar un enrutador de centro virtual con Azure Route Server aprovisionado en una red virtual.
* El enrutador del centro virtual solo admite ASN de 16 bits (2 bytes).
* La conexión de red virtual que tiene el punto de conexión BGP de la NVA siempre debe estar asociada y propagarse a defaultRouteTable. En este momento, no se admiten tablas de rutas personalizadas.
* El enrutador del centro virtual admite la conectividad de tránsito entre redes virtuales conectadas a centros virtuales. Esto no tiene nada que ver con esta característica para la funcionalidad de emparejamiento BGP, puesto que Virtual WAN ya admite la conectividad de tránsito. Ejemplos:
  * VNET1: NVA1 conectada al centro virtual 1 -> (conectividad de tránsito) -> VNET2: NVA2 conectada al centro virtual 1.
  * VNET1: NVA1 conectada al centro virtual 1 -> (conectividad de tránsito) -> VNET2: NVA2 conectada al centro virtual 2.
* Puede utilizar sus propios ASN públicos o ASN privados en la aplicación virtual de red. No se pueden usar los rangos reservados por Azure o IANA. Los siguientes ASN están reservados por Azure o IANA:
   * ASN reservados por Azure:
     * ASN públicos: 8074, 8075, 12076
     * ASN privados: 65515, 65517, 65518, 65519, 65520
   * ASN reservados por IANA: 23456, 64496-64511, 65535-65551
* Aunque el enrutador del centro virtual intercambia rutas BGP con la NVA y las propaga a la red virtual, facilita directamente la propagación de rutas desde el entorno local mediante las puertas de enlace hospedadas en el centro virtual (VPN Gateway, puerta de enlace de ExpressRoute, puertas de enlace de NVA administradas). 

   El enrutador del centro virtual tiene los siguientes límites:

   | Resource | Límite |
   |---|---|
   |  Número de rutas que puede anunciar cada emparejamiento BGP al centro virtual.| El centro solo puede aceptar un número máximo de 10 000 rutas (total) de sus recursos conectados. Por ejemplo, si un centro virtual tiene un total de 6000 rutas desde las redes virtuales, ramas y centros virtuales conectados, cuando se configura un nuevo emparejamiento BGP con una NVA, la NVA solo puede anunciar hasta 4000 rutas. |

## <a name="bgp-peering-scenarios"></a>Escenarios de emparejamiento BGP

En esta sección, se describen los escenarios en los que se puede usar la característica de emparejamiento BGP para configurar el enrutamiento.

## <a name="transit-vnet-connectivity"></a><a name="vnet-vnet"></a>Conectividad de red virtual de tránsito

:::image type="content" source="./media/scenario-bgp-peering-hub/vnet-vnet.png" alt-text="Gráfico con el enrutamiento de red virtual a red virtual.":::

En este escenario, el centro virtual llamado "Hub 1" está conectado a varias redes virtuales. El objetivo es establecer el enrutamiento entre las redes virtuales VNET1 y VNET5.

### <a name="configuration-steps-without-bgp-peering"></a>Pasos de configuración sin emparejamiento BGP

Son necesarios los pasos siguientes cuando no se usa el emparejamiento BGP en el centro virtual:

Configuración del centro virtual

* En defaultRouteTable del centro Hub 1, configure una ruta estática para VNET5 (subred 10.2.1.0/24) que apunte a la conexión de VNET2.
* En la conexión de red virtual del centro Hub 1 para VNET2, configure una ruta estática para VNET5 que apunte a la dirección IP de la NVA de VNET2 (subred 10.2.0.5).
* En el centro Hub 1, propague las rutas de las conexiones de VNET1 y VNET2 a defaultRouteTable y asócielas a defaultRouteTable.

Configuración de redes virtuales

* En VNET5, configure una ruta definida por el usuario (UDR) para que apunte a la dirección IP de la NVA de VNET2.

### <a name="configuration-steps-with-bgp-peering"></a>Pasos de configuración con emparejamiento BGP

En la configuración anterior, el mantenimiento de las rutas estáticas y la UDR puede volverse complejo si la configuración de VNET5 cambia con frecuencia. Para abordar este desafío, se puede usar la característica de emparejamiento BGP con un centro virtual y la configuración de enrutamiento se debe cambiar a los pasos siguientes:

Configuración del centro virtual

* En el centro Hub 1, configure la NVA de VNET2 como un emparejamiento BGP. Además, configure la NVA de VNET2 para que tenga un emparejamiento BGP con el centro Hub 1.
* En el centro Hub 1, propague las rutas de las conexiones de VNET1 y VNET2 a defaultRouteTable y asócielas a defaultRouteTable.

Configuración de redes virtuales

* En VNET5, configure una ruta definida por el usuario (UDR) para que apunte a la dirección IP de la NVA de VNET2.

#### <a name="effective-routes"></a>Rutas eficaces

En la tabla siguiente, se muestran algunas entradas de las rutas efectivas del centro Hub 1 en defaultRouteTable. Observe la ruta de VNET5 (subred 10.2.1.0/24): esto confirma que VNET1 y VNET5 podrán comunicarse entre sí.

| Prefijo de destino |  Próximo salto| Origen | Ruta de acceso de ASN|
| --- | --- | --- | ---|
| 10.2.0.0/24 |eastusconn  | Identificador de conexión de red virtual | - |
| 10.2.1.0/24 |Identificador de conexión del emparejamiento BGP de la NVA  | Identificador de conexión del emparejamiento BGP de la NVA |  65510|
|  10.4.1.0/24 | Hub 2 | Hub 2 | - |

La configuración del enrutamiento de esta manera mediante la característica elimina la necesidad de entradas de ruta estáticas en el centro virtual. Por lo tanto, la configuración es más sencilla y las tablas de rutas se actualizan dinámicamente cuando cambia la configuración de las redes virtuales conectadas (como VNET5).

## <a name="branch-vnet-connectivity"></a><a name="branch-vnet"></a>Conectividad de rama a red virtual

:::image type="content" source="./media/scenario-bgp-peering-hub/branch-vnet.png" alt-text="Gráfico con el enrutamiento de rama a red virtual.":::

En este escenario, el sitio local llamado "NVA Branch 1" tiene una VPN configurada para finalizar en la NVA de VNET2. El objetivo es configurar el enrutamiento entre NVA Branch 1 y la red virtual VNET1.

### <a name="configuration-steps-without-bgp-peering"></a>Pasos de configuración sin emparejamiento BGP

Son necesarios los pasos siguientes cuando no se usa el emparejamiento BGP en el centro virtual:

Configuración del centro virtual

* En defaultRouteTable del centro Hub 1, configure una ruta estática para NVA Branch 1 que apunte a la conexión de VNET2.
* En la conexión de red virtual del centro Hub 1 para VNET2, configure una ruta estática para NVA Branch 1 que apunte a la dirección IP de la NVA de VNET2 (10.2.0.5).
* En el centro Hub 1, propague las rutas de las conexiones de VNET1 y VNET2 a defaultRouteTable y asócielas a defaultRouteTable.

Configuración de redes virtuales

* Emparejamiento BGP entre la NVA de VNET2 y NVA Branch 1, y anuncios de rutas para VNET1 desde la NVA de VNET2 a NVA Branch 1.

### <a name="configuration-steps-with-bgp-peering"></a>Pasos de configuración con emparejamiento BGP

Con el tiempo, los prefijos de destino de NVA Branch 1 pueden cambiar o puede haber muchos sitios como NVA Branch 1 que necesitan conectividad con VNET1. Esto daría lugar a la necesidad de actualizaciones de las rutas estáticas en el centro Hub 1 y la conexión VNET2, lo que puede resultar complicado. En tales casos, podemos usar la característica de emparejamiento BGP con un centro virtual y los pasos de configuración para la conectividad de enrutamiento serían los que se indican a continuación.

Configuración del centro virtual

* En el centro Hub 1, configure la NVA de VNET2 como un emparejamiento BGP. Además, configure la NVA de VNET2 para que tenga un emparejamiento BGP con el centro Hub 1.
* En el centro Hub 1, propague las rutas de las conexiones de VNET1 y VNET2 a defaultRouteTable y asócielas a defaultRouteTable.

Configuración de redes virtuales

* Emparejamiento BGP entre la NVA de VNET2 y NVA Branch 1, y anuncios de rutas para VNET1 desde la NVA de VNET2 a NVA Branch 1.

#### <a name="effective-routes"></a>Rutas eficaces

En la tabla siguiente, se muestran algunas entradas de las rutas efectivas del centro Hub 1 en defaultRouteTable. Observe que la ruta de NVA Branch 1 (subred 192.168.1.0/24) se aprende mediante el emparejamiento BGP con la NVA.

 | Prefijo de destino |  Próximo salto| Origen | Ruta de acceso de ASN|
| --- | --- | --- | ---|
| 10.2.0.0/24 | eastusconn  | Identificador de conexión de red virtual | - |
| 192.168.1.0/24 | Identificador de conexión del emparejamiento BGP de la NVA  | Identificador de conexión del emparejamiento BGP de la NVA |  65510|

Para administrar los cambios de red en NVA Branch 1 o establecer la conectividad entre nuevos sitios como NVA Branch 1, no se requiere ninguna configuración adicional en el centro Hub 1 porque el emparejamiento BGP entre el centro Hub 1 y la NVA actualizará dinámicamente las tablas de rutas. Por lo tanto, se simplifican la configuración y el mantenimiento.

## <a name="next-steps"></a>Pasos siguientes

* [Creación del emparejamiento BGP con un centro virtual (versión preliminar): Azure Portal](create-bgp-peering-hub-portal.md).
