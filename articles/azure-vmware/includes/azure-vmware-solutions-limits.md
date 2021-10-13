---
title: Límites de Azure VMware Solution
description: Limitaciones de Azure VMware Solution.
ms.topic: include
ms.date: 09/02/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 7a8479a5262c1db61dbb03558632ebebac1a6983
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638313"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

En la tabla siguiente se describen los límites máximos para Azure VMware Solution.

| **Recurso** | **Límite** |
| :-- | :-- |
| Clústeres por nube privada | 12 |
| Número mínimo de hosts por clúster | 3 |
| Número máximo de hosts por clúster | 16 |
| Hosts por nube privada | 96 |
| vCenter por nube privada | 1  |
| Emparejamientos con sitios de HCX | 25 (cualquier edición) |
| Número máximo de nubes privadas vinculadas a ExpressRoute de Azure VMware Solution | 4<br />La puerta de enlace de red virtual utilizada determina el número máximo real de nubes privadas vinculadas.  Para más detalles, vea [Acerca de las puertas de enlace de red virtual de ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| Velocidad de puerto de ExpressRoute de Azure VMware Solution | 10 Gbps<br />La puerta de enlace de red virtual usada determina el ancho de banda real. Para más detalles, vea [Acerca de las puertas de enlace de red virtual de ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| Direcciones IP públicas expuestas a través de vWAN | 100 |
| Límites de capacidad de vSAN | 75 % del total que se puede usar (el 25 % restante se mantiene disponible para el contrato de nivel de servicio)  |

En el caso de otros límites específicos de VMware, use la [herramienta de configuración máxima de VMware](https://configmax.vmware.com/).
