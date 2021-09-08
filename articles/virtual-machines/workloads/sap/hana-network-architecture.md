---
title: Arquitectura de red de SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Más información sobre la arquitectura de red para implementar SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5add931f71dfdb5034e614b3d6c3ddc8703293a2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461578"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Arquitectura de red de SAP HANA (instancias grandes)

En este artículo, se analiza la arquitectura de red para implementar SAP HANA en Azure (instancias grandes) (también conocida como infraestructura sin sistema operativo). 

La arquitectura del servicio de red de Azure es un componente clave para implementar las aplicaciones de SAP de forma correcta en HANA (instancias grandes). Normalmente, las implementaciones de SAP HANA en Azure (instancias grandes) tienen un entorno de SAP más amplio. Es probable que incluyan varias soluciones de SAP con distintos tamaños de bases de datos, consumo de recursos de CPU y uso de memoria. 

Es probable que no todos los sistemas de TI ya se encuentren en Azure. El entorno de SAP también puede ser híbrido. El sistema de administración de bases de datos (DBMS) y la aplicación SAP pueden usar una combinación de NetWeaver, S/4HANA y SAP HANA. La aplicación SAP puede incluso usar otro DBMS.

Azure ofrece distintos servicios que permiten ejecutar los sistemas DBMS, NetWeaver y S/4HANA en Azure. Azure ofrece tecnología de red, de forma que se asemeja a un centro de datos virtual para las implementaciones de software locales. La funcionalidad de red de Azure incluye: 

- Las redes virtuales de Azure se conectan al circuito [ExpressRoute](https://azure.microsoft.com/services/expressroute/) que conecta con los recursos de red locales.
- Un circuito ExpressRoute que conecta el entorno local a Azure con un ancho de banda mínimo de [1 Gbps o más](https://azure.microsoft.com/pricing/details/expressroute/). Este circuito permite suficiente ancho de banda para la transferencia de datos entre los sistemas locales y los sistemas que se ejecutan en máquinas virtuales. También permite un ancho de banda adecuado para la conexión de los usuarios locales a los sistemas de Azure.
- Todos los sistemas SAP de Azure se configuran en redes virtuales para poder comunicarse entre sí.
- Active Directory y DNS hospedados localmente se extienden a Azure a través de ExpressRoute desde un entorno local. También se pueden ejecutar completamente en Azure.

Al integrar HANA (instancias grandes) en el tejido de red del centro de datos de Azure, también se usa la tecnología de ExpressRoute.


> [!NOTE] 
> Solo se puede vincular una suscripción de Azure a un inquilino en una demarcación de HANA (Instancias grandes) de una región de Azure específica. Y al contrario, un solo inquilino con la demarcación de HANA (Instancias grandes) solo se puede vincular a una suscripción de Azure. Este requisito es coherente con otros objetos facturables en Azure.

Si se implementa SAP HANA en Azure (instancias grandes) en varias regiones de Azure diferentes, se implementará un inquilino diferente en HANA (instancias grandes). Puede ejecutar las dos en la misma suscripción de Azure, siempre que estas instancias formen parte de la misma infraestructura de SAP. 

> [!IMPORTANT] 
> La única implementación compatible con SAP HANA en Azure (Instancias grandes) es la de Azure Resource Manager.

## <a name="extra-virtual-network-information"></a>Información adicional sobre la red virtual

Para conectar una red virtual a ExpressRoute, se debe crear una puerta de enlace de Azure ExpressRoute. Para más información, consulte [Acerca de las puertas de enlace de ExpressRoute para ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md). 

Se puede usar una puerta de enlace de Azure ExpressRoute con ExpressRoute para una infraestructura fuera de Azure o para una demarcación de instancias grandes de Azure. Puede conectar la puerta de enlace de Azure ExpressRoute a un máximo de cuatro circuitos ExpressRoute si esas conexiones proceden de diferentes enrutadores perimetrales empresariales de Microsoft (MSEE). Para más información, consulte [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md). 

> [!NOTE] 
> El rendimiento máximo que se puede lograr con una puerta de enlace de ExpressRoute es de 10 Gbps mediante una conexión de ExpressRoute. La copia de archivos entre una máquina virtual que resida en una red virtual y un sistema local (como una sola secuencia de copia) no consigue el rendimiento total de las diferentes SKU de puerta de enlace. Para aprovechar el ancho de banda completo de la puerta de enlace de ExpressRoute, use varias secuencias o copie diferentes archivos en secuencias paralelas de un único archivo.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitectura de redes de HANA (instancias grandes)
La arquitectura de redes de HANA (instancias grandes) se puede dividir en cuatro partes:

- Redes locales y conexión de ExpressRoute a Azure. Esta parte es el dominio de los clientes y se conecta a Azure a través de ExpressRoute. Este circuito ExpressRoute lo paga usted por completo. El ancho de banda debe ser lo suficientemente grande como para controlar el tráfico de red entre los recursos locales y la región de Azure con la que se conecta. Consulte la parte inferior derecha en la ilustración siguiente.
- Servicios de red de Azure, como se explicó anteriormente, que requieren que se agreguen puertas de enlace de ExpressRoute. Para esta parte, debe crear los diseños adecuados para satisfacer los requisitos de la aplicación y los de cumplimiento y seguridad. Considere si debe usar HANA (instancias grandes) dado el número de redes virtuales y las SKU de puerta de enlace de Azure entre las que elegir. Observe la parte superior derecha de la ilustración.
- Conectividad de HANA (instancias grandes) a través de ExpressRoute en Azure. Microsoft implementa y controla esta parte. Todo lo que necesita hacer es proporcionar algunos intervalos de direcciones IP después de haber implementado los recursos de HANA (instancias grandes) y conectado el circuito ExpressRoute a las redes virtuales. Para más información, consulte [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md). No se le cobra ninguna tarifa adicional por la conectividad entre el tejido de red del centro de datos de Azure y las unidades de HANA (Instancias grandes).
- Las redes dentro de la demarcación de HANA (Instancias grandes), lo que resulta casi transparente para usted.

![Red virtual conectada a SAP HANA en Azure (instancias grandes) y localmente](./media/hana-overview-architecture/image1-architecture.png)

Los dos requisitos siguientes se mantienen aunque use HANA (instancias grandes):
- Los recursos locales deben conectarse a través de ExpressRoute a Azure.
- Necesita una o varias redes virtuales que ejecuten las máquinas virtuales. Estas máquinas virtuales hospedan la capa de la aplicación que se conecta a las instancias de HANA hospedadas en HANA (Instancias grandes).

Las diferencias en las implementaciones de SAP en Azure son:

- Las unidades de HANA (instancias grandes) del inquilino se conectan a las redes virtuales través de otro circuito ExpressRoute. Los circuitos ExpressRoute entre el entorno local y la red virtual de Azure y los circuitos entre las redes virtuales de Azure y HANA (instancias grandes) no comparten los mismos enrutadores. Sus condiciones de carga permanecen independientes.
- El perfil de la carga de trabajo entre el nivel de la aplicación SAP y HANA (instancias grandes) es de un tipo diferente. SAP HANA genera muchas solicitudes y ráfagas pequeñas, como transferencias de datos (conjuntos de resultados) en el nivel de aplicación.
- La arquitectura de aplicación de SAP es más sensible a la latencia de la red que los escenarios típicos donde los datos se intercambian entre el entorno local y Azure.
- La puerta de enlace de Azure ExpressRoute tiene al menos dos conexiones ExpressRoute. Un circuito que se ha conectado desde el entorno local y otro que se ha conectado desde HANA (instancias grandes). Esta configuración solo deja espacio para dos circuitos más de diferentes MSEE para conectarse a la puerta de enlace de ExpressRoute. Esta restricción es independiente del uso de ExpressRoute FastPath. Todos los circuitos conectados comparten el ancho de banda máximo para los datos de entrada de la puerta de enlace de ExpressRoute.

Con la revisión 3 de las demarcaciones de HANA (instancias grandes), la latencia de red que se experimenta entre las máquinas virtuales y las unidades de HANA (instancias grandes) puede ser mayor que una latencia de red de recorrido de ida y vuelta entre máquinas virtuales. En función de la región de Azure, los valores pueden superar la latencia de recorrido de ida y vuelta de 0,7 ms clasificada como por debajo del promedio en [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (Nota de SAP 1100926: Preguntas más frecuentes sobre el rendimiento de red). En función de la región de Azure y de la herramienta para medir la latencia de ida y vuelta de la red entre una máquina virtual de Azure y la unidad de HANA (instancias grandes) la latencia puede ser de hasta 2 milisegundos. A pesar de ello, los clientes pueden implementar aplicaciones de SAP de producción basadas en SAP HANA en SAP HANA (instancias grandes). Asegúrese de probar exhaustivamente los procesos empresariales en Azure HANA (instancias grandes). Una funcionalidad nueva, llamada ExpressRoute FastPath, es capaz de reducir sustancialmente la latencia de red entre HANA (instancias grandes) y las máquinas virtuales en el nivel de la aplicación en Azure (consulte a continuación). 

La revisión 4 de las demarcaciones de HANA (instancias grandes) mejora la latencia de red entre las máquinas virtuales de Azure implementadas en una ubicación próxima. La latencia supera el promedio, como se documenta en [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (Nota de SAP #1100926: Preguntas más frecuentes: Rendimiento de la red) si Azure ExpressRoute FastPath está configurado (consulte a continuación). 

Para implementar máquinas virtuales de Azure en una ubicación próxima a las unidades de HANA (instancias grandes) de la revisión 4, debe aplicar los [grupos con ubicación por proximidad de Azure](../../co-location.md). Los grupos con ubicación por proximidad se pueden utilizar para ubicar el nivel de la aplicación SAP en el mismo centro de datos de Azure que la revisión 4 de las unidades hospedadas de HANA (instancias grandes). Para más información, consulte [Grupos de selección de ubicación de proximidad de Azure para una latencia de red óptima con aplicaciones SAP](sap-proximity-placement-scenarios.md).

Para proporcionar latencia de red determinista entre las máquinas virtuales y HANA (instancias grandes), es fundamental el uso de la SKU de puerta de enlace de ExpressRoute. A diferencia de los patrones de tráfico entre el entorno local y las máquinas virtuales, los patrones de tráfico entre las máquinas virtuales y HANA (instancias grandes) pueden generar pequeñas pero elevadas ráfagas de solicitudes y volúmenes de datos. Para poder controlar esas ráfagas, se recomienda el uso de la SKU de puerta de enlace UltraPerformance. En el caso de la clase de SKU de HANA (instancias grandes) de tipo II, es obligatorio el uso de la SKU de puerta de enlace UltraPerformance como puerta de enlace de ExpressRoute.

> [!IMPORTANT] 
> Dado el tráfico de red general entre la capa de base de datos y la de la aplicación de SAP, solo se admiten las SKU de puerta de enlace HighPerformance o UltraPerformance para redes virtuales para la conexión a SAP HANA en Azure (instancias grandes). Con las SKU de tipo II de HANA (Instancias grandes), solo se admite la SKU de puerta de enlace UltraPerformance como puerta de enlace de ExpressRoute. Se aplican excepciones al usar ExpressRoute FastPath (consulte a continuación).

### <a name="expressroute-fastpath"></a>FastPath de ExpressRoute
En mayo de 2019 se publicó ExpressRoute FastPath. FastPath reduce la latencia entre HANA (instancias grandes) y las redes virtuales de Azure que hospedan las máquinas virtuales de la aplicación SAP. Con FastPath, los flujos de datos entre las máquinas virtuales y HANA (instancias grandes) ya no se enrutan a través de la puerta de enlace de ExpressRoute. Las máquinas virtuales asignadas en las subredes de la red virtual de Azure se comunican directamente con el enrutador perimetral empresarial dedicado. 

> [!IMPORTANT] 
> ExpressRoute FastPath requiere que las subredes que ejecutan las máquinas virtuales de las aplicaciones de SAP estén en la misma red virtual de Azure que está conectada a HANA (instancias grandes). Las máquinas virtuales ubicadas en redes virtuales de Azure que están emparejadas con la red virtual de Azure conectada a las unidades de HANA (instancias grandes) no se benefician de ExpressRoute FastPath. Como consecuencia de ello, en los diseños típicos de red virtual con topología de red en estrella de tipo hub-and-spoke, en los que los circuitos ExpressRoute se conectan con una red virtual de centro de conectividad y se emparejan las redes virtuales que contienen el nivel de aplicación SAP (radios), la optimización de ExpressRoute FastPath no funcionará. ExpressRoute FastPath tampoco admite actualmente reglas de enrutamiento definidas por el usuario (UDR). Para más información, consulte [Puerta de enlace de red virtual de ExpressRoute y FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md). 


Para más información sobre cómo configurar ExpressRoute FastPath, consulte [Conexión de una red virtual a instancias grandes de HANA](./hana-connect-vnet-express-route.md).    

> [!NOTE]
> Para que funcione ExpressRoute FastPath, se requiere una puerta de enlace de ExpressRoute UltraPerformance.


## <a name="single-sap-system"></a>Único sistema SAP

La infraestructura local mostrada anteriormente se conecta a través de ExpressRoute con Azure. El circuito ExpressRoute se conecta a un enrutador perimetral empresarial (MSEE). Para más información, consulte [Información técnica sobre ExpressRoute](../../../expressroute/expressroute-introduction.md). Una vez establecida la ruta, se conecta a la red troncal de Azure.

> [!NOTE] 
> Para ejecutar infraestructuras de SAP en Azure, conéctese al enrutador perimetral de empresa más cercano a la región de Azure en la infraestructura de SAP. Las demarcaciones de HANA (instancias grandes) están conectadas a través de dispositivos enrutadores perimetrales empresariales dedicados para minimizar la latencia de red entre las máquinas virtuales de IaaS de Azure y las demarcaciones HANA (instancias grandes).

La puerta de enlace de ExpressRoute de las VM que hospedan instancias de aplicaciones SAP está conectada a un circuito ExpressRoute que se conecta al entorno local. La misma red virtual está conectada a un enrutador perimetral empresarial independiente dedicado. Ese enrutador perimetral se dedica a la conexión con las demarcaciones de instancias grandes. De nuevo, con FastPath, el flujo de datos de HANA (instancias grandes) hacia las máquinas virtuales en el nivel de la aplicación no se enruta a través de la puerta de enlace de ExpressRoute. Esta configuración reduce la latencia de ida y vuelta de la red.

Este sistema es un ejemplo sencillo de un único sistema SAP. El nivel de aplicación de SAP se hospeda en Azure. La base de datos de SAP HANA se ejecuta en SAP HANA en Azure (instancias grandes). Se supone que el ancho de banda de la puerta de enlace de ExpressRoute de 2 Gbps o 10 Gbps de rendimiento no representa un cuello de botella.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Varios sistemas SAP o sistemas SAP grandes

Si implementa varios sistemas SAP o sistemas SAP grandes con conexión a SAP HANA (instancias grandes), el rendimiento de la puerta de enlace de ExpressRoute podría convertirse en un cuello de botella. En ese caso, divida los niveles de aplicación en varias redes virtuales. También puede dividir los niveles de la aplicación si desea aislar sistemas de producción y de no producción en diferentes redes virtuales de Azure. 

Puede crear una red virtual especial que se conecta a HANA (instancias grandes) al:

- Realizar copias de seguridad directamente desde las instancias de HANA en HANA (instancias grandes) a una máquina virtual de Azure que hospeda los recursos compartidos de NFS.
- Copiar las copias de seguridad de gran tamaño u otros archivos de unidades de HANA (instancias grandes) en espacio en disco administrado en Azure.

Use una red virtual independiente para hospedar las máquinas virtuales que administran el almacenamiento para la transferencia masiva de datos entre HANA (Instancias grandes) y Azure. Esta disposición evita los efectos de las transferencias de archivos o datos de gran volumen desde HANA (instancias grandes) hasta Azure en la puerta de enlace de ExpressRoute que atiende a las máquinas virtuales que ejecutan el nivel de la aplicación de SAP. 

Para una arquitectura de red más ampliable:

- Use varias redes virtuales para una sola capa de aplicación de SAP más grande.
- Implemente una red virtual distinta para cada sistema SAP implementado, en comparación con la combinación de estos sistemas SAP en subredes independientes en la misma red virtual.

    En el diagrama siguiente se muestra una arquitectura de red más ampliable para SAP HANA en Azure (instancias grandes):

![Implementación del nivel de aplicación de SAP en varias redes virtuales](./media/hana-overview-architecture/image4-networking-architecture.png)

Las redes virtuales se deben emparejar en función de las reglas y las restricciones que quiera aplicar entre esas distintas redes virtuales que hospedan máquinas virtuales de distintos sistemas SAP. Para más información sobre el emparejamiento de redes virtuales, consulte [Emparejamiento de redes virtuales](../../../virtual-network/virtual-network-peering-overview.md).


## <a name="routing-in-azure"></a>Enrutamiento en Azure

En una implementación hay, de forma predeterminada, tres consideraciones importantes sobre el enrutamiento de red para SAP HANA en Azure (Instancias grandes):

- Solo se puede acceder a SAP HANA en Azure (Instancias grandes) mediante máquinas virtuales de Azure y la conexión ExpressRoute dedicada, no directamente desde el entorno local. El acceso directo desde local a las unidades de HANA (instancias grandes), tal como lo entrega Microsoft, no es posible inmediatamente. Las restricciones de enrutamientos transitivas son debidas a la arquitectura de red de Azure actual utilizada para SAP HANA (instancias grandes). Los clientes de administración y las aplicaciones que necesiten acceso directo, como SAP Solution Manager ejecutado de forma local, no se pueden conectar a la base de datos de SAP HANA. Para conocer las excepciones, consulte la sección siguiente [Enrutamiento directo a HANA (instancias grandes)](#direct-routing-to-hana-large-instances).

- Si tiene implementadas unidades de HANA (instancias grandes) en dos regiones de Azure distintas para la recuperación ante desastres, se aplican las mismas restricciones del enrutamiento transitorio. En otras palabras, las direcciones IP de una unidad de HANA (instancias grandes) en una región (por ejemplo, Oeste de EE. UU.) no se enrutaban a una unidad de HANA (instancias grandes) implementada en otra región (por ejemplo, Este de EE. UU.). Esta restricción es independiente del uso del emparejamiento de redes de Azure entre regiones o de los circuitos ExpressRoute que conectan las unidades de HANA (instancias grandes) con las redes virtuales. Para ver una representación gráfica, consulte la ilustración de la sección [Uso de unidades de HANA (instancias grandes) en varias regiones](#use-hana-large-instance-units-in-multiple-regions). Esta restricción, que se incluía con la arquitectura implementada, prohibía el uso inmediato de la replicación del sistema de HANA para la recuperación ante desastres. Para conocer los cambios recientes, de nuevo, consulte la sección [Uso de unidades de HANA (instancias grandes) en varias regiones](#use-hana-large-instance-units-in-multiple-regions). 

- SAP HANA en Azure (instancias grandes) tiene una dirección IP asignada del intervalo de direcciones del grupo de direcciones IP del servidor que ha enviado al solicitar la implementación de HANA (instancias grandes). Para más información, consulte [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md). Se puede acceder a esta dirección IP mediante las suscripciones a Azure y el circuito que conecta las redes virtuales de Azure a HANA (instancias grandes). La dirección IP asignada fuera del intervalo de direcciones del grupo IP de servidor se asigna directamente a la unidad de hardware. Ya *no* se asigna mediante traducción de direcciones de red (NAT), como sucedía en las primeras implementaciones de esta solución. 

### <a name="direct-routing-to-hana-large-instances"></a>Enrutamiento directo a HANA (Instancias grandes)

De forma predeterminada, el enrutamiento transitivo no funciona en estos escenarios:

- Entre unidades de instancias grandes de HANA y una implementación local.

- Entre unidades de HANA (instancias grandes) que están implementadas en regiones diferentes.

Hay tres maneras de habilitar el enrutamiento transitivo en esos escenarios:

- Un servidor proxy inverso para enrutar los datos, desde y hacia. Por ejemplo, F5 BIG-IP y NGINX con Traffic Manager implementado en una red virtual de Azure que se conecta a HANA (Instancias grandes) y al entorno local como una solución de enrutamiento virtual para el firewall y el tráfico.
- Usar reglas de IPTables en una máquina virtual Linux para habilitar el enrutamiento entre las ubicaciones locales y las unidades de HANA (instancias grandes) o entre unidades de HANA (instancias grandes) en regiones diferentes. La máquina virtual que ejecuta IPTables debe implementarse en la red virtual de Azure que se conecta a HANA (instancias grandes) y al entorno local. Se debe ajustar el tamaño de la máquina virtual de forma que el rendimiento de red de la máquina virtual sea suficiente para el tráfico de red esperado. Para más información sobre el ancho de banda de red de la máquina virtual, consulte el artículo [Tamaños de las máquinas virtuales Linux en Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Otra solución para permitir el tráfico directo entre el entorno local y las unidades de HANA (Instancias grandes) sería [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/). 

Todo el tráfico de estas soluciones se enrutaría a través de una red virtual de Azure. Por lo tanto, los dispositivos de software que usan los grupos de seguridad de red de Azure también podrían restringir el tráfico. De esta forma, direcciones IP o intervalos de direcciones IP concretos del entorno local podrían bloquear o permitir explícitamente el acceso a HANA (instancias grandes). 

> [!NOTE]  
> Tenga en cuenta que Microsoft no proporciona la implementación y soporte técnico para las soluciones personalizadas que tienen dispositivos de red de terceros o IPTables. El proveedor del componente utilizado o el integrador es el que debe proporcionar el soporte técnico. 

#### <a name="express-route-global-reach"></a>ExpressRoute Global Reach
Microsoft presentó una nueva funcionalidad llamada [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md). Global Reach puede usarse con HANA (Instancias grandes) en dos escenarios:

- Para permitir el acceso directo desde el entorno local a las unidades de HANA (instancias grandes) implementadas en distintas regiones.
- Para permitir la comunicación directa entre las unidades de HANA (instancias grandes) implementadas en distintas regiones.


##### <a name="direct-access-from-on-premises"></a>Acceso directo desde el entorno local
En las regiones de Azure en las que se ofrece Global Reach, puede solicitar habilitar este servicio para el circuito ExpressRoute. Ese circuito conecta la red local a la red virtual de Azure que se conecta a HANA (instancias grandes). Hay algunos costos relacionados con el entorno local del circuito ExpressRoute. Para más información, consulte los precios del [complemento Global Reach](https://azure.microsoft.com/pricing/details/expressroute/). No pagará costos adicionales por el circuito que conecta HANA (instancias grandes) a Azure. 

> [!IMPORTANT]  
> Al usar Global Reach para habilitar el acceso directo entre las unidades de HANA (instancias grandes) y los recursos locales, los datos de red y el flujo de control **no se enrutan a través de redes virtuales de Azure**. En su lugar, los datos de red y el flujo de control se enrutan directamente entre los enrutadores de intercambio de empresa de Microsoft. Por tanto, las reglas NSG o ASG, o cualquier tipo de firewall, NVA o proxy que haya implementado en una red virtual de Azure, no se modificarán. **Si usa ExpressRoute Global Reach para permitir el acceso directo entre el entorno local y las unidades de HANA (instancias grandes), es necesario definir restricciones y permisos para acceder a ellas en los firewalls del lado local**. 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Conexión de HANA (Instancias grandes) a diferentes regiones de Azure
Igualmente, ExpressRoute Global Reach también puede usarse para conectar inquilinos de HANA (instancias grandes) implementados en regiones diferentes. El aislamiento son los circuitos ExpressRoute que usan los inquilinos de HANA (instancias grandes) para conectarse a Azure en ambas regiones. No hay ningún cargo adicional por conectar dos inquilinos de HANA (instancias grandes) que están implementados en regiones diferentes. 

> [!IMPORTANT]  
> El flujo de datos y el flujo de control del tráfico de red entre los inquilinos de HANA (instancias grandes) no se enrutará a través de redes de Azure. Por tanto, no se puede usar la funcionalidad de Azure ni las aplicaciones virtuales de red para aplicar restricciones de comunicación entre los inquilinos de HANA (instancias grandes). 

Para más información sobre cómo habilitar ExpressRoute Global Reach, consulte [Conexión de una red virtual a instancias grandes de HANA](./hana-connect-vnet-express-route.md).


## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividad a Internet de HANA (instancias grandes)
Las unidades de HANA (instancias grandes) *no* tienen conectividad directa a Internet. Por ejemplo, esta limitación puede restringir la capacidad de registrar la imagen del sistema operativo directamente con el proveedor del sistema operativo. Debe trabajar con la herramienta de administración de suscripciones de SUSE Linux Enterprise Server local o con el administrador de suscripciones de Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Cifrado de datos entre las máquinas virtuales y HANA (instancias grandes)
Los datos transferidos entre HANA (instancias grandes) y las máquinas virtuales no se cifran. No obstante, solo para el intercambio entre el DBMS de HANA y las aplicaciones basadas en JDBC y ODBC, puede habilitar el cifrado del tráfico. Para más información, consulte [Comunicación segura entre SAP HANA y clientes JDBC/ODBC](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Uso de unidades de HANA (instancias grandes) en varias regiones

Para la recuperación ante desastres, debe tener unidades de HANA (instancias grandes) en varias regiones de Azure. Solo con el uso del [emparejamiento de Vnet global](../../../virtual-network/virtual-network-peering-overview.md) de Azure, el enrutamiento transitivo no funcionará de forma predeterminada entre los inquilinos de HANA (instancias grandes) de dos regiones diferentes. Global Reach, no obstante, abre la comunicación entre las unidades de HANA (instancias grandes) de distintas regiones. En este escenario, el uso de ExpressRoute Global Reach permite:

 - La replicación del sistema HANA sin firewalls ni proxies adicionales.
 - La copia de seguridad entre las unidades de HANA (instancias grandes) de dos regiones diferentes para hacer copias o actualizaciones del sistema.


![Redes virtuales conectadas a demarcaciones de instancias grandes de Azure en diferentes regiones de Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

En la ilustración anterior se muestra cómo las redes virtuales de ambas regiones se conectan a dos circuitos ExpressRoute. Los circuitos se usan para conectarse a SAP HANA en Azure (instancias grandes) en ambas regiones de Azure (líneas grises). El motivo de estas dos conexiones cruzadas es proteger frente a una interrupción de los MSEE en ambos lados. Se supone que el flujo de comunicación entre las dos redes virtuales de las dos regiones de Azure diferentes debe controlarse a través del [emparejamiento global](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) de estas dos redes virtuales (línea azul de puntos). La línea roja gruesa describe la conexión de ExpressRoute Global Reach. Esta conexión permite que las unidades de HANA (instancias grandes) de los inquilinos de distintas regiones se comuniquen entre sí. 

> [!IMPORTANT] 
> Si se usan varios circuitos ExpressRoute, utilice las configuraciones de anteposición de AS Path y de preferencia local de BGP para garantizar el enrutamiento adecuado del tráfico.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la arquitectura de almacenamiento de SAP HANA en Azure (instancias grandes).

> [!div class="nextstepaction"]
> [Arquitectura de almacenamiento de SAP HANA (instancias grandes)](hana-storage-architecture.md)
