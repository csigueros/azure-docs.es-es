---
title: Planes de servicio y cuotas para Azure Spring Cloud
description: Más información sobre las cuotas y los planes de servicio de Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 1bd1ed797ce737260289ab40ed6a16c4ce4581e7
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015589"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Cuotas y planes de servicio de Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java ✔️ C#

Todos los servicios de Azure establecen límites y cuotas predeterminados para recursos y características.   Azure Spring Cloud ofrece dos planes de tarifa: Básico y Estándar. En este artículo se detallan los límites de ambos niveles.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Límites y niveles de servicio de Azure Spring Cloud

| Recurso | Ámbito | Básico | Estándar
------- | ------- | -------
vCPU | por instancia de aplicación | 1 | 4
Memoria | por instancia de aplicación | 2 GB | 8 GB
Instancias del servicio Azure Spring Cloud | por región y suscripción | 10 | 10
Instancias de aplicación totales | por instancia del servicio Azure Spring Cloud | 25 | 500
Dominios personalizados | por instancia del servicio Azure Spring Cloud | 0 | 25
Volúmenes persistentes | por instancia del servicio Azure Spring Cloud | 1 GB/aplicación x 10 aplicaciones | 50 GB/aplicación x 10 aplicaciones
Puntos de conexión públicos de entrada | por instancia del servicio Azure Spring Cloud| 10 <sup>1</sup> | 10 <sup>1</sup>
Direcciones IP públicas de salida | por instancia del servicio Azure Spring Cloud| 1 <sup>2</sup> | 2 <sup>2</sup> <br> 1 si se usa VNet<sup>2</sup>

<sup>1</sup> Puede aumentar este límite mediante la solicitud de soporte técnico a un máximo de 1 por aplicación.

<sup>2</sup> Puede aumentar este límite mediante la solicitud de soporte técnico a un máximo de 10.

> [!TIP]
> Los límites indicados para las instancias de aplicación totales por instancia de servicio se aplican a las aplicaciones y las implementaciones en cualquier estado, incluido el estado Detenido. Elimine las aplicaciones o implementaciones que no estén en uso.

## <a name="next-steps"></a>Pasos siguientes

Algunos límites predeterminados se pueden aumentar. Si su configuración requiere un aumento, [cree una solicitud de soporte técnico](../azure-portal/supportability/how-to-create-azure-support-request.md).
