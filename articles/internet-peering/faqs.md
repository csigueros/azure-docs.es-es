---
title: Preguntas más frecuentes sobre el emparejamiento de Internet
titleSuffix: Azure
description: Preguntas más frecuentes sobre el emparejamiento de Internet
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c4c58a0550d35575721a1d27ce7a572ac0e00e90
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024209"
---
# <a name="internet-peering---faqs"></a>Preguntas más frecuentes sobre el emparejamiento de Internet

Puede revisar la información que se ofrece más abajo para ver las preguntas generales.

**¿Cuál es la diferencia entre el emparejamiento de Internet y Peering Service?**

Peering Service es un servicio diseñado para proporcionar conectividad IP pública de categoría empresarial a Microsoft para sus clientes empresariales. Internet de categoría empresarial incluye conectividad a través de ISP que tienen conectividad de alto rendimiento con Microsoft, y redundancia para una conectividad de alta disponibilidad. Además, el tráfico del usuario se optimiza para latencia en la instancia de Microsoft Edge más cercana. Peering Service se basa en la conectividad de emparejamiento con un partner operador. La conectividad de emparejamiento con el partner debe ser emparejamiento directo, en contraposición al emparejamiento de Exchange. El emparejamiento directo debe tener redundancia local y geográfica.

**¿Qué es el emparejamiento heredado?**

La configuración de la conexión de emparejamiento mediante Azure PowerShell se administra como un recurso de Azure. Las conexiones de emparejamiento configuradas anteriormente se almacenan en nuestro sistema como emparejamiento heredado, el cual se puede convertir para administrarlo como un recurso de Azure.

**Cuando se llama a New-AzPeeringDirectConnectionObject, ¿qué direcciones IP se asignan a los dispositivos de Microsoft y a los del mismo nivel?**

Al llamar al cmdlet New-AzPeeringDirectConnectionObject, se especifica una dirección `/31` (`a.b.c.d/31`) o una dirección `/30` (`a.b.c.d/30`). La primera dirección IP (`a.b.c.d+0`) se proporciona al dispositivo del mismo nivel y la segunda (`a.b.c.d+1`), al dispositivo de Microsoft.

**¿Qué son los parámetros MaxPrefixesAdvertisedIPv4 y MaxPrefixesAdvertisedIPv6 en el cmdlet New-AzPeeringDirectConnectionObject?**

Los parámetros MaxPrefixesAdvertisedIPv4 y MaxPrefixesAdvertisedIPv6 representan el número máximo de prefijos IPv4 e IPv6 que un elemento del mismo nivel quiere que acepte Microsoft. Se pueden modificar en cualquier momento.