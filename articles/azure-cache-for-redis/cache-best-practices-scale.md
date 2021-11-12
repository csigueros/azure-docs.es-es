---
title: Procedimientos recomendados de escalado
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo escalar su instancia de Azure Cache for Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: b0d43e062db591600d1cbcc0cdb18b7ce6b8ebac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323512"
---
# <a name="scaling"></a>Ampliación

## <a name="scaling-under-load"></a>Escalado bajo carga

Al escalar una caché bajo carga, configure la opción maxmemory-reserved para mejorar la capacidad de respuesta del sistema. Para obtener más información, consulte [Configuración de la opción maxmemory-reserved](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting).

## <a name="scaling-clusters"></a>Escalado de clústeres

Intente reducir los datos tanto como pueda en la caché antes de escalar o reducir horizontalmente la caché en clúster. Esta reducción garantiza que se tengan que mover volúmenes más pequeños de datos, lo que a su vez reduce el tiempo necesario para la operación de escalado. Para obtener más información sobre cuándo escalar, consulte [Cuándo escalar](cache-how-to-scale.md#when-to-scale).

## <a name="scale-before-load-is-too-high"></a>Escalado antes de que la carga sea demasiado alta

Inicie el escalado antes de que la carga o el uso de memoria del servidor sean demasiado altos. Si son demasiado altos, significa que el servidor de Redis está ocupado. Estando ocupado, el servidor de Redis no tiene suficientes recursos para escalar y redistribuir datos.

## <a name="cache-sizes"></a>Tamaños de caché

Si usa TLS y tiene un gran número de conexiones, considere la posibilidad de escalar horizontalmente para poder distribuir la carga entre más núcleos. Algunos tamaños de caché se hospedan en máquinas virtuales con cuatro o más núcleos.

Distribuya el cifrado o descifrado de TLS y las cargas de trabajo de conexión o desconexión de TLS en varios núcleos para reducir el uso general de la CPU en las máquinas virtuales de caché. Para obtener más información, consulte los [detalles sobre los tamaños y núcleos de las máquinas virtuales](./cache-planning-faq.yml#azure-cache-for-redis-performance).

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la opción maxmemory-reserved](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
- [Escalado de una instancia de Azure Cache for Redis](cache-how-to-scale.md)
