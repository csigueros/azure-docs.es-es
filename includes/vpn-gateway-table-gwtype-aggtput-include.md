---
title: Archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7205fa7095608084140f7ac6e1c3965b1296a8fe
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336302"
---
|**Generación<br>de<br>VPN Gateway** |**SKU**   | **Túneles<br>S2S/entre redes virtuales** | Conexiones **P2S<br> SSTP** | Conexiones **P2S<br> IKEv2/OpenVPN** | **Pruebas comparativas de rendimiento<br>agregado** | **BGP** | **Con redundancia de zona** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generación 1**|**Basic**   | Máx. 10    | Máx. 128  | No compatible  | 100 Mbps  | No compatible| No |
|**Generación 1**|**VpnGw1**  | Máx. 30*   | Máx. 128  | Máx. 250       | 650 Mbps  | Compatible | No |
|**Generación 1**|**VpnGw2**  | Máx. 30*   | Máx. 128  | Máx. 500       | 1 Gbps    | Compatible | No |
|**Generación 1**|**VpnGw3**  | Máx. 30*   | Máx. 128  | Máx. 1000      | 1,25 Gbps | Compatible | No |
|**Generación 1**|**VpnGw1AZ**| Máx. 30*   | Máx. 128  | Máx. 250       | 650 Mbps  | Compatible | Sí |
|**Generación 1**|**VpnGw2AZ**| Máx. 30*   | Máx. 128  | Máx. 500       | 1 Gbps    | Compatible | Sí |
|**Generación 1**|**VpnGw3AZ**| Máx. 30*   | Máx. 128  | Máx. 1000      | 1,25 Gbps | Compatible | Sí |
|        |            |            |           |                |           |           |     |
|**Generación 2**|**VpnGw2**  | Máx. 30*   | Máx. 128  | Máx. 500       | 1,25 Gbps | Compatible | No |
|**Generación 2**|**VpnGw3**  | Máx. 30*   | Máx. 128  | Máx. 1000      | 2,5 Gbps  | Compatible | No |
|**Generación 2**|**VpnGw4**  | Máx. 30*   | Máx. 128  | Máx. 5000      | 5 Gbps    | Compatible | No |
|**Generación 2**|**VpnGw5**  | Máx. 30*   | Máx. 128  | Máx. 10000      | 10 Gbps   | Compatible | No |
|**Generación 2**|**VpnGw2AZ**| Máx. 30*   | Máx. 128  | Máx. 500       | 1,25 Gbps | Compatible | Sí |
|**Generación 2**|**VpnGw3AZ**| Máx. 30*   | Máx. 128  | Máx. 1000      | 2,5 Gbps  | Compatible | Sí |
|**Generación 2**|**VpnGw4AZ**| Máx. 30*   | Máx. 128  | Máx. 5000      | 5 Gbps    | Compatible | Sí |
|**Generación 2**|**VpnGw5AZ**| Máx. 30*   | Máx. 128  | Máx. 10000      | 10 Gbps   | Compatible | Sí |

(*) Use una [red WAN virtual](../articles/virtual-wan/virtual-wan-about.md) si necesita más de 30 túneles VPN S2S.

* Se permite el cambio de tamaño de las SKU de VpnGw en la misma generación, excepto el cambio de tamaño de la SKU básica. La SKU básica es una SKU heredada y tiene limitaciones de características. Para pasar de una SKU básica a otra SKU de VpnGw, debe eliminar la instancia de VPN Gateway de la SKU básica y crear una nueva puerta de enlace con la combinación de generación y tamaño de SKU deseada.

* Estos límites de conexión son independientes. Por ejemplo, en una SKU de VpnGw1 puede tener 128 conexiones SSTP, además de 250 conexiones IKEv2.

* Puede encontrar más información sobre los precios en la página de [precios](https://azure.microsoft.com/pricing/details/vpn-gateway).

* La información del SLA (contrato de nivel de servicio) puede encontrarse en la página [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* En un único túnel, se puede lograr un rendimiento máximo de 1 Gbps. Las pruebas comparativas de rendimiento agregado de la tabla anterior se basan en las mediciones de varios túneles agregados a través de una sola puerta de enlace. El banco de pruebas de rendimiento agregado para una puerta de enlace de VPN es la combinación de S2S + P2S. **Si tiene una gran cantidad de conexiones P2S, puede afectar negativamente a una conexión S2S debido a las limitaciones del rendimiento.** Las pruebas comparativas de rendimiento agregado no es un rendimiento garantizado debido a las condiciones del tráfico de Internet y a los comportamientos de las aplicaciones.

Para ayudar a nuestros clientes a comprender el rendimiento relativo de las SKU mediante distintos algoritmos, usamos las herramientas iPerf y CTSTraffic disponibles públicamente para medir los rendimientos. En la tabla siguiente se enumeran los resultados de las pruebas de rendimiento de las SKU de VpnGw de la generación 1. Como puede ver, el mejor rendimiento se obtiene cuando usamos el algoritmo GCMAES256 para el cifrado y la integridad IPsec. Obtuvimos un rendimiento medio al usar AES256 para el cifrado IPsec y SHA256 para la integridad. Cuando usamos DES3 para el cifrado IPsec y SHA256 para la integridad, obtuvimos el rendimiento más bajo.

|**Generación**|**SKU**   | **Algoritmos<br>usados** | **Rendimiento<br>observado** | **Paquetes por segundo por <br>túnel observado** |
|---           |---       | ---                 | ---            | ---                    |
|**Generación 1**|**VpnGw1**| GCMAES256<br>AES256 y SHA256<br>DES3 y SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58 000<br>50.000<br>50.000|
|**Generación 1**|**VpnGw2**| GCMAES256<br>AES256 y SHA256<br>DES3 y SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90 000<br>80 000<br>55 000|
|**Generación 1**|**VpnGw3**| GCMAES256<br>AES256 y SHA256<br>DES3 y SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105 000<br>90 000<br>60 000|
|**Generación 1**|**VpnGw1AZ**| GCMAES256<br>AES256 y SHA256<br>DES3 y SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58 000<br>50.000<br>50.000|
|**Generación 1**|**VpnGw2AZ**| GCMAES256<br>AES256 y SHA256<br>DES3 y SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90 000<br>80 000<br>55 000|
|**Generación 1**|**VpnGw3AZ**| GCMAES256<br>AES256 y SHA256<br>DES3 y SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105 000<br>90 000<br>60 000|
