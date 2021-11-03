---
title: Límites de servicio de Azure Chaos Studio
description: Información sobre la limitación y los límites de uso de Azure Chaos Studio
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.date: 11/01/2021
ms.topic: reference
ms.custom: ignite-fall-2021
ms.openlocfilehash: f9b5352597afb00b379363a2f151615a902dff8f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091661"
---
# <a name="azure-chaos-studio-service-limits"></a>Límites de servicio de Azure Chaos Studio
En este artículo se proporcionan los límites de servicio de Azure Chaos Studio. 
## <a name="experiment-and-target-limits"></a>Límites de experimento y destino

Chaos Studio aplica límites al número de objetos, la duración de las actividades y la retención de datos.

| Límite | Value |
| -- | -- |
| Acciones por experimento | 9 |
| Ramas por experimento | 9 |
| Pasos por experimento | 4 |
| Duración de la acción (en horas) | 12 |
| Experimentos simultáneos que se ejecutan por región y suscripción | 5 |
| Duración total del experimento (en horas) | 12 |
| Número de experimentos por región y suscripción | 500 |
| Número de destinos por acción | 50 |
| Número de agentes activos por destino | 1,000 |
| Número de destinos por región y suscripción | 10 000 |

## <a name="api-throttling-limits"></a>Límites de la API

Chaos Studio aplica límites a todas las operaciones de ARM. Las solicitudes realizadas sobre el límite están limitadas. Todos los límites de solicitud se aplican durante un intervalo de cinco minutos, a menos que se especifique lo contrario.

| Operación | Requests |
| -- | -- |
| Microsoft.Chaos/experiments/write | 100 |
| Microsoft.Chaos/experiments/read | 300 |
| Microsoft.Chaos/experiments/delete | 100 |
| Microsoft.Chaos/experiments/start/action | 20 |
| Microsoft.Chaos/experiments/cancel/action | 100 |
| Microsoft.Chaos/experiments/statuses/read | 100 |
| Microsoft.Chaos/experiments/executionDetails/read | 100 |
| Microsoft.Chaos/targets/write | 200 |
| Microsoft.Chaos/targets/read | 600 |
| Microsoft.Chaos/targets/delete | 200 |
| Microsoft.Chaos/targets/capabilities/write | 600 |
| Microsoft.Chaos/targets/capabilities/read | 1800 |
| Microsoft.Chaos/targets/capabilities/delete | 600 |
| Microsoft.Chaos/locations/targetTypes/read | 50 |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/read | 50 |
