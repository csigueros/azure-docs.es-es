---
title: Procedimientos recomendados para escalar una instancia de Azure Cache for Redis
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo escalar su instancia de Azure Cache for Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 01fbebb06ba408232add1f0f05c5b715816e9e69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114386"
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
