---
title: Obtención de detalles de Azure Cache for Redis mediante la CLI de Azure
description: Este ejemplo de código de la CLI de Azure muestra cómo recuperar los detalles de una instancia de Azure Cache for Redis, incluido el estado de aprovisionamiento.
author: curib
ms.author: cauribeg
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e41b432fe7259fac9769a70a89bb6357c3d550e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535350"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Obtención de detalles de Azure Cache for Redis

En este escenario, obtendrá información sobre cómo recuperar los detalles de una instancia de Azure Cache for Redis, incluido el estado de aprovisionamiento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los comandos siguientes para recuperar los detalles de una instancia de Azure Cache for Redis. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az redis show](/cli/azure/redis) | Recuperación de los detalles de una instancia de Azure Cache for Redis. |


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Encontrará más ejemplos de scripts de CLI de Azure Cache for Redis en la [documentación de Azure Cache for Redis](../cli-samples.md).