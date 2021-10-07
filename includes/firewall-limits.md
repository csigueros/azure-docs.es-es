---
title: Archivo de inclusión
description: archivo de inclusión
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 09/17/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: e0926dc0a211990a64812b00702e84acb0d1f140
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645543"
---
| Recurso | Límite |
| --- | --- |
| Rendimiento de los datos |30 Gbps|
|Límites de reglas|10 000 destinos u orígenes únicos en las reglas de red y de aplicación|
|Número máximo de reglas DNAT|298 (para firewalls configurados con una única dirección IP pública)<br><br> La limitación de DNAT se debe a la plataforma subyacente. El número máximo de reglas DNAT es 298. Sin embargo, todas las direcciones IP públicas adicionales reducen el número de reglas de DNAT disponibles. Por ejemplo, dos direcciones IP públicas permiten 297 reglas de DNAT. Si el protocolo de una regla está configurado tanto para TCP como para UDP, cuenta como dos reglas.|
|Tamaño mínimo de AzureFirewallSubnet |/26|
|Intervalo de puertos en reglas de red y aplicación|1 - 65535|
|Direcciones IP públicas|250 máximo. Todas las IP públicas se pueden usar en las reglas de DNAT y todas ellas contribuyen a los puertos SNAT disponibles.|
|Direcciones IP de grupos de IP|Máximo de 100 grupos de IP por firewall.<br>Máximo de 5000 direcciones IP individuales o prefijos IP por cada grupo de IP.
|Tabla de rutas|De forma predeterminada, AzureFirewallSubnet tiene una ruta 0.0.0.0/0 con el valor de NextHopType establecido en **Internet**.<br><br>Azure Firewall debe tener conectividad directa a Internet. Si AzureFirewallSubnet aprende una ruta predeterminada a la red local mediante BGP, debe reemplazarla por una UDR 0.0.0.0/0 con el valor **NextHopType** establecido como **Internet** para mantener la conectividad directa a Internet. De forma predeterminada, Azure Firewall no admite la tunelización forzada a una red local.<br><br>Sin embargo, si la configuración requiere la tunelización forzada a una red local, Microsoft proporcionará soporte según el caso. Póngase en contacto con soporte técnico para que podamos revisar su caso. Si acepta, permitiremos su suscripción y nos aseguraremos de que se mantenga la conectividad del firewall a Internet requerida.|
|Nombres de dominio completo en las reglas de red|Para lograr que el rendimiento sea bueno, no debe haber más de 1000 nombres de dominio completo en todas las reglas de red por firewall.|