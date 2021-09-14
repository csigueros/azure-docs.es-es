---
title: Plataforma de proceso Azure API Management
description: Información sobre la plataforma de proceso que se usa para hospedar la instancia del servicio API Management
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 15bc0aa0c4a657bbb6f363a9c2786c1d4f9116ee
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544385"
---
# <a name="compute-platform-for-azure-api-management"></a>Plataforma de proceso para Azure API Management

Como plataforma como servicio en la nube (PaaS), Azure API Management abstrae muchos detalles de la infraestructura que se usa para hospedar y ejecutar el servicio. Puede crear, administrar y modificar la escala de la mayoría de los aspectos de la instancia de API Management sin necesidad de conocer sus recursos subyacentes.

Para mejorar las funcionalidades del servicio, estamos actualizando la versión de la plataforma de proceso API Management (los recursos de proceso de Azure que hospedan el servicio) para instancias de varios [niveles de servicio](api-management-features.md). En este artículo se proporciona contexto sobre la actualización y las versiones principales de la plataforma de proceso API Management: `stv1` y `stv2`.

Se han minimizado los impactos de esta actualización en el funcionamiento de la instancia de API Management. Pero si la instancia está conectada a una [red virtual de Azure](virtual-network-concepts.md), tendrá que cambiar algunos valores de configuración de red cuando la instancia se actualice a la versión de la plataforma `stv2`.

## <a name="compute-platform-versions"></a>Versiones de la plataforma de proceso

| Version | Descripción | Architecture | Niveles de API Management |
| -------| ----------| ----------- | ------- |
| `stv2` | Inquilino único v2 | [Conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/overview.md) | Desarrollador, Básico, Estándar y Prémium |
| `stv1` |  Inquilino único v1 | [Servicio en la nube (clásico)](../cloud-services/cloud-services-choose-me.md) | Desarrollador, Básico, Estándar y Prémium |
| `mtv1` | Multiinquilino v1 |  [App Service](../app-service/overview.md) | Consumo |


## <a name="how-do-i-know-which-platform-hosts-my-api-management-instance"></a>Cómo saber qué plataforma hospeda la instancia de API Management

### <a name="developer-basic-standard-and-premium-tiers"></a>Niveles Desarrollador, Básico, Estándar y Premium

* Las instancias con conexiones de red virtual creadas o actualizadas mediante Azure Portal después de **abril de 2021**, o mediante la API REST versión **2021-01-01-preview** o posterior de API Management, se hospedan en la plataforma `stv2`.
* Si ha habilitado la [redundancia de zona](zone-redundancy.md) en la instancia de nivel Premium, se hospeda en la plataforma `stv2`.
* De lo contrario, la instancia se hospeda en la plataforma `stv1`.

> [!TIP]
> A partir de la versión de API `2021-04-01-preview`, la instancia de API Management tiene una propiedad `PlatformVersion` de solo lectura que muestra esta información de la plataforma. 

### <a name="consumption-tier"></a>Nivel de consumo

* Todas las instancias se hospedan en la plataforma `mtv1`

## <a name="how-do-i-upgrade-to-the-stv2-platform"></a>¿Cómo se actualiza a la plataforma `stv2`? 

La actualización solo es posible para una instancia del nivel Desarrollador, Básico, Estándar o Premium. 

Cree o actualice la conexión de red virtual o la configuración de la zona de disponibilidad en una instancia de API Management mediante lo siguiente:

* [Azure Portal](https://portal.azure.com)
* API REST de Azure, o plantilla de ARM, en la que se especifique la versión de API **2021-01-01-preview** o posterior

> [!IMPORTANT]
> Al actualizar la versión de la plataforma de proceso de una instancia conectada a una [red virtual de Azure](virtual-network-concepts.md):
> * Tendrá que proporcionar un recurso de [dirección IPv4 pública](../virtual-network/public-ip-addresses.md#standard) de SKU estándar.
> * Las direcciones VIP de la instancia de API Management cambiarán.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre el uso de una [red virtual](virtual-network-concepts.md) con API Management.
* Obtenga más información sobre la [redundancia de zona](zone-redundancy.md).