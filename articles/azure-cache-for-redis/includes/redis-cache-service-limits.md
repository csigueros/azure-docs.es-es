---
author: curib
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: cauribeg
ms.openlocfilehash: a04462ab50a5db71e9d019ab9d49fb137f569569
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585024"
---
| Resource | Límite |
| --- | --- |
| Tamaño de memoria caché |1,2 TB |
| Bases de datos |64 |
| Número máximo de clientes conectados |40.000 |
| Réplicas de Azure Cache for Redis, para alta disponibilidad |1 |
| Particiones en una caché premium con agrupación en clústeres |10 |

Los tamaños y límite de Azure Cache for Redis son diferentes para cada plan de tarifa. Para ver los planes de tarifa y sus tamaños asociados, vea los [precios de Azure Cache for Redis](https://azure.microsoft.com/pricing/details/cache/).

Para información sobre los límites de configuración de Azure Cache for Redis, consulte [Configuración predeterminada del servidor Redis](../cache-configure.md#default-redis-server-configuration).

Dado que Microsoft realiza la configuración y la administración de instancias de Azure Cache for Redis, no se admiten todos los comandos de Redis en Azure Cache for Redis. Para más información, consulte [Comandos de Redis que no se admiten en Azure Cache for Redis](../cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).
