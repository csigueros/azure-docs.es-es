---
title: Protección contra DDoS L7 en línea con Gateway Load Balancer y NVA de asociados
description: Obtener información sobre cómo crear y habilitar la protección contra DDoS L7 en línea con Gateway Load Balancer y NVA de asociados
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.author: yitoh
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 49e4bc5dbe2214cafe039ceb0d6163a42f388629
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715328"
---
# <a name="inline-l7-ddos-protection-with-gateway-load-balancer-and-partner-nvas"></a>Protección contra DDoS L7 en línea con Gateway Load Balancer y NVA de asociados

Azure DDoS Protection está siempre activo pero no en línea y tarda entre 30 y 60 segundos desde el momento en que se detecta un ataque hasta que el ataque se mitiga. Azure DDoS Protection Standard también funciona en L3/4 (capa de red) y no inspecciona la carga de paquetes, es decir, la capa de aplicación (L7).  
Las cargas de trabajo que son altamente sensibles a la latencia y no pueden tolerar entre 30 y 60 segundos de tiempo de inicio para que la protección contra DDoS entre en vigora requieren una protección en línea. La protección en línea implica que todo el tráfico pasa siempre a través de la canalización de protección contra DDoS. Además, en escenarios como la protección web o la protección de cargas de trabajo de juegos (UDP), resulta fundamental inspeccionar la carga de paquetes para mitigar los ataques extremos de bajo volumen que aprovechan la vulnerabilidad en el nivel de aplicación (L7). 

Las NVA de asociados implementadas con Gateway Load Balancer e integradas con Azure DDoS Protection Standard ofrecen una protección contra DDoS L7 en línea para escenarios de alto rendimiento y alta disponibilidad. La combinación de la protección de DDoS L7 en línea con Azure DDoS Protection Standard proporciona una protección L3-L7 completa contra ataques DDoS volumétricos, así como ataques DDoS de bajo volumen. 

## <a name="what-is-a-gateway-load-balancer"></a>¿Qué es una instancia de Gateway Load Balancer?
Gateway Load Balancer es una SKU de Azure Load Balancer destinada específicamente para escenarios de alto rendimiento y alta disponibilidad con aplicaciones virtuales de red (NVA) de terceros.

Con las funcionalidades de Gateway LB, puede implementar, escalar y administrar NVA con facilidad: encadenar una instancia de Gateway LB al punto de conexión público simplemente requiere un clic.  Puede insertar dispositivos para una variedad de escenarios, como firewalls, análisis avanzado de paquetes, sistemas de detección y prevención de intrusiones o escenarios personalizados que se adapten a sus necesidades en la ruta de acceso de red con Gateway LB. En escenarios con NVA, es especialmente importante que los flujos sean "simétricos": esto garantiza que las sesiones se mantengan y sean simétricas. Gateway LB mantiene la simetría de flujo a una instancia específica del grupo de back-end.

Para obtener más información sobre Gateway Load Balancer, consulte la documentación y el producto [Gateway LB](../load-balancer/gateway-overview.md). 

## <a name="inline-ddos-protection-with-gateway-lb-and-partner-nvas"></a>Protección contra DDoS en línea con Gateway LB y NVA de asociados

Los ataques DDoS en cargas de trabajo confidenciales de latencia alta (por ejemplo, juegos) pueden provocar interrupciones que van de 2 a 10 segundos, lo que provoca una interrupción de la disponibilidad. Gateway Load Balancer permite la protección de estas cargas de trabajo al garantizar que las NVA pertinentes se insertan en la ruta de acceso de entrada del tráfico de Internet. Una vez encadenada a una configuración de front-end o IP de equilibrador de carga público estándar en una máquina virtual, no se necesita ninguna configuración adicional para garantizar que el tráfico hacia y desde el punto de conexión de la aplicación se envía a Gateway LB. 

El tráfico entrante siempre se inspecciona a través de las NVA en la ruta de acceso y el tráfico limpio se devuelve a la infraestructura de back-end (servidores de juegos). 

El tráfico fluye desde la red virtual de consumidor a la red virtual del proveedor y, posteriormente, vuelve a la red virtual del consumidor. La red virtual de consumidor y la red virtual del proveedor pueden estar en distintas suscripciones, inquilinos o regiones, lo cual permite una mayor flexibilidad y facilidad de administración.

![Protección contra DDoS en línea mediante Gateway Load Balancer](./media/ddos-glb.png)
 
Al habilitar Azure DDoS Protection Standard en la red virtual de front-end del equilibrador de carga público estándar o la red virtual de la máquina virtual, se ofrece protección frente a ataques DDoS L3/4. 
1.  El tráfico de juegos sin filtrar desde Internet se dirige a la dirección IP pública del equilibrador de carga de servidores de juegos. 
2.  El tráfico de juegos sin filtrar se redirige a la dirección IP privada de Gateway Load Balancer encadenada. 
3.  El tráfico de juegos sin filtrar se inspecciona en busca de ataques DDoS en tiempo real a través de las NVA de asociados. 
4.  El tráfico de juegos filtrado se envía de vuelta a los servidores de juegos para su procesamiento final.
5.  Azure DDoS Protection Standard en el equilibrador de carga de servidores de juegos protege frente a ataques DDoS L3/4 y las directivas de protección contra DDoS se ajustan automáticamente para el perfil de tráfico de los servidores de juegos y la escala de aplicaciones. 

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre los [asociados de protección contra DDoS L7 en línea](https://aka.ms/inlineddospartners)
- Más información sobre [Azure DDoS Protection Standard](./ddos-protection-overview.md)
- Más información sobre [Gateway Load Balancer](../load-balancer/gateway-overview.md)