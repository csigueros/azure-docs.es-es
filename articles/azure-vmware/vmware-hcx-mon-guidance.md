---
title: Guía de VMware HCX Mobility Optimized Networking (MON)
description: Obtenga información sobre casos de uso específicos de Azure VMware Solution para Mobility Optimized Networking (MON).
ms.topic: reference
ms.date: 09/07/2021
ms.openlocfilehash: 19e8fce28bc9582c388a6c2667fa6dfc6ca64636
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646234"
---
# <a name="vmware-hcx-mobility-optimized-networking-mon-guidance"></a>Guía de VMware HCX Mobility Optimized Networking (MON)

[HCX Mobility Optimized Networking (MON)](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) es una característica opcional que se puede habilitar al usar [HCX Network Extension (NE)](configure-hcx-network-extension.md). MON proporciona un enrutamiento óptimo del tráfico en determinados escenarios para evitar el desvío de tráfico entre los recursos locales y los recursos basados en la nube en redes extendidas. 

A lo largo del ciclo de migración, MON optimiza la movilidad de las aplicaciones para lo siguiente:

- Optimizar la comunicación L2 de máquina virtual (VM) a máquina virtual cuando se usan redes extendidas 

- Optimizar y prevenir los flujos de tráfico asimétrico entre el entorno local, Azure VMware Solution y Azure


En este artículo, conocerá los casos de uso específicos de Azure VMware Solution para MON.


## <a name="optimize-traffic-flows-across-standard-and-stretched-segments-on-the-private-cloud-side"></a>Optimización de los flujos de tráfico entre segmentos estándar y extendidos en el lado de la nube privada 

En este escenario, VM1 se migra a la nube mediante NE, lo que proporciona una latencia óptima de máquina virtual a máquina virtual. Como resultado, VM1 necesita una latencia baja para VM3 en el segmento local de Azure VMware Solution. Migramos la puerta de enlace VM1 del entorno local a Azure VMware Solution (nube) para garantizar una ruta de acceso óptima para el tráfico (línea azul). Si la puerta de enlace permanece en el entorno local (línea roja), se observa un efecto de desvío de tráfico y una mayor latencia. 

>[!NOTE]
>Habilitar MON sin migrar la puerta de enlace de la máquina virtual al lado de la nube no garantiza una ruta de acceso óptima para el flujo de tráfico.  Tampoco permite la evaluación de rutas de directiva.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-1.png" alt-text="Diagrama que muestra la optimización de la comunicación L2 entre máquinas virtuales cuando se usan redes extendidas." border="false":::



## <a name="optimize-and-avoid-asymmetric-traffic-flows"></a>Optimización y prevención de flujos de tráfico asimétricos 

En este escenario, se da por hecho que una máquina virtual del entorno local se ha migrado a Azure VMware Solution y participa en la comunicación L2. Además, el tráfico L3 fluye de vuelta al entorno local para acceder a los servicios. También se da por hecho que alguna comunicación de máquina virtual de Azure (en la red virtual conectada de Azure VMware Solution) podría llegar a la nube privada de Azure VMware Solution.

>[!IMPORTANT]
>La cuestión principal aquí es planear y evitar cuidadosamente los flujos de tráfico asimétrico. 

De forma predeterminada y sin usar MON, una máquina virtual de Azure VMware Solution en una red extendida sin MON puede comunicarse de nuevo con el entorno local mediante la ruta de acceso preferida de ExpressRoute. Idealmente, y según los casos de uso de los clientes, se recomienda evaluar si una máquina virtual en un segmento extendido de Azure VMware Solution habilitado con MON debería volver al entorno local mediante la puerta de enlace T0 o mediante NE a través de ExpressRoute, pero manteniendo los flujos de tráfico simétricos.

Si elige la ruta de acceso de NE, por ejemplo, las rutas de directiva de MON tienen que dirigirse específicamente a la subred en el lado local; de lo contrario, se usa la ruta 0.0/0. Las rutas de directiva se pueden encontrar en las opciones avanzadas del segmento de NE. De forma predeterminada, todas las direcciones IP RFC1918 se incluyen en la definición de las rutas de directiva de MON. 

:::image type="content" source="media/tutorial-vmware-hcx/default-hcx-mon-policy-based-routes.png" alt-text="Captura de pantalla en la que se muestran las rutas predeterminadas basadas en directivas.":::

Las rutas de directiva solo se evalúan si la puerta de enlace de la máquina virtual se migra a la nube. El resultado de esta configuración es que las subredes coincidentes del destino se tunelizan mediante el dispositivo NE.  Si las subredes no coinciden, se enrutan a través de la puerta de enlace T0.

>[!NOTE]
>Para usar MON en Azure VMware Solution, es importante proporcionar las rutas /32 anunciadas por medio de BGP a sus pares; esto incluye el entorno local y Azure mediante la conexión de ExpressRoute. Por ejemplo, una máquina virtual de Azure aprende la ruta de acceso a una máquina virtual de Azure VMware Solution en un segmento de Azure VMware Solution habilitado para MON. Una vez que el tráfico de retorno se devuelve a la puerta de enlace T0 según lo previsto, si la subred devuelta coincide con RFC1918, el tráfico se fuerza a través de NE en lugar de a través de T0.  Después, vuelve a Azure a través de ExpressRoute en el lado local.  Esto puede provocar confusión para los firewalls con estado en cuanto al comportamiento de enrutamiento asimétrico e intermedio. También es conveniente determinar cómo tendrán que acceder a Internet las máquinas virtuales de los segmentos de NE MON, ya sea mediante la puerta de enlace T0 en Azure VMware Solution o solo a través de NE de vuelta al entorno local.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-3.png" alt-text="Diagrama que muestra el flujo de tráfico de salida y la salida de RFC1918." border="false":::

Tal y como se muestra en el diagrama anterior, lo importante es hacer coincidir una ruta de directiva con cada subred necesaria. De lo contrario, el tráfico se enruta a través de T0 y no de NE.

 
Para obtener más información sobre las rutas de directiva, consulte el artículo [Mobility Optimized Networking Policy Routes](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-F45B1DB5-C640-4A75-AEC5-45C58B1C9D63.html) (Rutas de directiva para Mobility Optimized Networking).

