---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 47e3d4e86e5288bb9a75d9d2b9a0b053ac50fc8e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242294"
---
|  | **Emparejamiento privado** | **Emparejamiento de Microsoft** |  **Emparejamiento público** (en desuso para circuitos nuevos) |
| --- | --- | --- | --- |
| **Número máximo de prefijos admitidos por emparejamiento** |4000 de forma predeterminada, 10.000 con ExpressRoute Premium |200 |200 |
| **Intervalos de direcciones IP admitidas** |Cualquier dirección IP válida de la WAN. |Direcciones IP públicas propiedad suya o de su proveedor de conectividad. |Direcciones IP públicas propiedad suya o de su proveedor de conectividad. |
| **Requisitos del número de sistema autónomo (AS)** |Números de sistema autónomo (AS) públicos y privados Debe poseer el número de AS público si opta por utilizar uno. |Números de sistema autónomo (AS) públicos y privados Sin embargo, debe comprobar la titularidad de las direcciones IP públicas. |Números de sistema autónomo (AS) públicos y privados Sin embargo, debe comprobar la titularidad de las direcciones IP públicas. |
| **Protocolos IP admitidos**| IPv4, IPv6 |  IPv4, IPv6 | IPv4 |
| **Direcciones IP de la interfaz de enrutamiento** |Direcciones IP públicas y de RFC1918 |Direcciones IP públicas registradas para usted en los registros de enrutamiento. |Direcciones IP públicas registradas para usted en los registros de enrutamiento. |
| **Compatibilidad con Hash MD5** |Sí |Sí |Sí |
