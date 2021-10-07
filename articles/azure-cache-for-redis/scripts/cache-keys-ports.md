---
title: Obtención del nombre de host, los puertos y las claves para Azure Cache for Redis con la CLI de Azure
description: Este ejemplo de código de la CLI de Azure muestra cómo obtener el nombre de host, los puertos y las claves para una instancia de Azure Cache for Redis.
author: curib
ms.author: cauribeg
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 096443b1ec83c5aa7bec6446f3a67f6a750944b1
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534246"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Obtención del nombre de host, los puertos y las claves para Azure Cache for Redis

En este escenario se aprende a recuperar el nombre de host, los puertos y las claves que se usan para conectarse a una instancia de Azure Cache for Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los comandos siguientes para recuperar el nombre de host, las claves y los puertos de una instancia de Azure Cache for Redis. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az redis show](/cli/azure/redis) | Recuperación de los detalles de una instancia de Azure Cache for Redis. |
| [az redis list-keys](/cli/azure/redis) | Recuperación de las teclas de acceso de una instancia de Azure Cache for Redis. |


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Encontrará más ejemplos de scripts de CLI de Azure Cache for Redis en la [documentación de Azure Cache for Redis](../cli-samples.md).