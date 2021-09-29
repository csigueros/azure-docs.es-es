---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 4fa712e8cf19bf4deb9b536777520a3ffec7261c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645534"
---
| Recurso | Límite |
| --- | --- |
| Tamaño de memoria caché |1,2 TB |
| Bases de datos |64 |
| Número máximo de clientes conectados |40.000 |
| Réplicas de Azure Cache for Redis, para alta disponibilidad |1 |
| Particiones en una caché premium con agrupación en clústeres |10 |

Los tamaños y límite de Azure Cache for Redis son diferentes para cada plan de tarifa. Para ver los planes de tarifa y sus tamaños asociados, vea los [precios de Azure Cache for Redis](https://azure.microsoft.com/pricing/details/cache/).

Para información sobre los límites de configuración de Azure Cache for Redis, consulte [Configuración predeterminada del servidor Redis](../cache-configure.md#default-redis-server-configuration).

Dado que Microsoft realiza la configuración y la administración de instancias de Azure Cache for Redis, no se admiten todos los comandos de Redis en Azure Cache for Redis. Para más información, consulte [Comandos de Redis que no se admiten en Azure Cache for Redis](../cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).
