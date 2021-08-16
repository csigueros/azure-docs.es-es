---
title: Novedades de Azure Load Balancer
description: Conozca las novedades de Azure Load Balancer, como, por ejemplo, las notas de la versión más recientes, los problemas conocidos, las correcciones de errores, las funcionalidades en desuso y los próximos cambios.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: a6950907e6ff52088030844a509f91193bc329dc
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215584"
---
# <a name="whats-new-in-azure-load-balancer"></a>Novedades de Azure Load Balancer

Azure Load Balancer se actualiza con regularidad. Manténgase al día con los anuncios más recientes. En este artículo se proporciona información acerca de lo siguiente:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores
- Funcionalidad en desuso (si procede)

También puede encontrar las últimas actualizaciones de Azure Load Balancer y suscribirse a la fuente RSS [aquí](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="recent-releases"></a>Versiones recientes

| Tipo |Nombre |Descripción  |Fecha de adición  |
| ------ |---------|---------|---------|
| Característica | [Compatibilidad con grupos de back-end basados en IP (disponibilidad general)](https://azure.microsoft.com/updates/iplbga/) | Azure Load Balancer permite agregar y quitar recursos de un grupo de back-end a través de direcciones IPv4 o IPv6. Esto permite la administración sencilla de contenedores, máquinas virtuales y conjuntos de escalado de máquinas virtuales asociados a Load Balancer. También permitirá reservar direcciones IP como parte de un grupo de back-end antes de que se creen los recursos asociados. Obtenga más información [aquí](backend-pool-management.md).|Marzo de 2021 |
| Característica | [Compatibilidad con metadatos de instancia para las direcciones IP públicas y los equilibradores de carga de la SKU estándar](https://azure.microsoft.com/updates/standard-load-balancer-and-ip-addresses-metadata-now-available-through-azure-instance-metadata-service-imds/)|Los metadatos de las direcciones IP públicas y Standard Load Balancer ahora se pueden recuperar mediante Azure Instance Metadata Service (IMDS). Los metadatos están disponibles desde las instancias en ejecución de las máquinas virtuales y los conjuntos de escalado de máquinas virtuales (VMSS). Puede aprovechar los metadatos para administrar las máquinas virtuales. Más información [aquí](instance-metadata-service-load-balancer.md).| Febrero de 2021 |
| Característica | [Actualización de la SKU de la dirección IP pública de Básico a Estándar sin perder la dirección IP](https://azure.microsoft.com/updates/public-ip-sku-upgrade-generally-available/) | Cuando pase del equilibrador de carga Básico al Estándar, puede conservar la dirección IP pública. Obtenga más información [aquí](../virtual-network/virtual-network-public-ip-address-upgrade.md).| Enero de 2021|
| Característica | Compatibilidad con movimientos entre grupos de recursos | Compatibilidad de Standard Load Balancer y direcciones IP públicas estándar para los [movimientos de grupos de recursos](https://azure.microsoft.com/updates/standard-resource-group-move/). | Octubre de 2020 |
| Característica| Información sobre Azure Load Balancer con Azure Monitor | Integrados como parte de Azure Monitor para redes, ahora los clientes cuentan con mapas topológicos de todas sus configuraciones de Load Balancer y paneles de mantenimiento de sus instancias de Standard Load Balancer preconfiguradas con métricas en Azure Portal. [Introducción y más información](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Junio de 2020 |
| Validación | Incorporación de la validación para puertos de alta disponibilidad | Se ha agregado una validación para garantizar que las reglas de puerto de alta disponibilidad y las reglas que no son de este tipo solo se puedan configurar cuando se habilite la IP flotante. Anteriormente, esta configuración se procesaba, pero no funcionaba según lo previsto. No se realizó ningún cambio en la funcionalidad. Puede obtener más información [aquí](load-balancer-ha-ports-overview.md#limitations)| Junio de 2020 |
| Característica| Compatibilidad de IPv6 con Azure Load Balancer (disponible con carácter general) | Puede tener direcciones IPv6 como front-end para las instancias de Azure Load Balancer. Aprenda a [crear una aplicación de pila doble aquí](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). |Abril de 2020|
| Característica| Restablecimientos TCP en tiempo de espera de inactividad (disponible con carácter general)| Use los restablecimientos TCP para crear un comportamiento más predecible de las aplicaciones. [Más información](load-balancer-tcp-reset.md)| Febrero de 2020 |

## <a name="known-issues"></a>Problemas conocidos

El grupo de productos funciona activamente en la resolución de los siguientes problemas conocidos:

|Incidencia |Descripción  |Mitigación  |
| ---------- |---------|---------|
| Dirección IP de salida del equilibrador de carga basado en IP | El equilibrador de carga basado en IP aprovecha la dirección IP de acceso de salida predeterminada de Azure para la salida cuando no se han configurado reglas de salida. | Para evitar el acceso de salida desde esta dirección IP, aproveche las reglas de salida o una puerta de enlace NAT para tener una dirección IP predecible y para evitar el agotamiento de puertos SNAT. |

  

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Load Balancer, consulte [¿Qué e Azure Load Balancer?](load-balancer-overview.md) y las [preguntas frecuentes](load-balancer-faqs.yml).
