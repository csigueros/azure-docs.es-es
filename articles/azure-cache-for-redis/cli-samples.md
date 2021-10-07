---
title: Administración de Azure Cache for Redis con la CLI de Azure
description: 'Ejemplos de la CLI de Azure para administrar Azure Cache for Redis: Crear una caché, eliminar una caché, obtener detalles de la caché, nombre de host, puertos y claves, conectar una aplicación web.'
author: curib
ms.author: cauribeg
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: f6a3b1701a73c4b71ee07b0110761566a4b2e93d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535803"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Administración de Azure Cache for Redis con la CLI de Azure

En la tabla siguiente se incluyen vínculos a scripts de Bash creados con la CLI de Azure.

| Create cache | Descripción |
| ------------ | ----------- |
| [Creación de una caché](./scripts/create-cache.md) | Crea un grupo de recursos y una instancia de Azure Cache for Redis de nivel básico. |
| [Creación de una caché premium con agrupación en clústeres](./scripts/create-premium-cache-cluster.md) | Crea un grupo de recursos y un caché de nivel premium con la agrupación de clústeres habilitada.|
| [Obtención de detalles de caché](./scripts/show-cache.md) | Obtiene los detalles de una instancia de Azure Cache for Redis, incluido el estado de aprovisionamiento. |
| [Obtención del nombre de host, los puertos y las claves](./scripts/cache-keys-ports.md) | Obtiene el nombre de host, los puertos y las claves de una instancia de Azure Cache for Redis. |
|**Web app plus cache**| **Descripción**|
| [Conexión de una aplicación web a Azure Cache for Redis](./../app-service/scripts/cli-connect-to-redis.md) | Crea una aplicación web de Azure y una instancia de Azure Cache for Redis y después agrega los detalles de conexión de Redis a la configuración de la aplicación. |
|**Delete cache**| **Descripción** |
| [Eliminación de una caché](./scripts/delete-cache.md) | Elimina una instancia de Azure Cache for Redis.  |

Para obtener más información sobre la CLI de Azure, vea [Instalar la CLI de Azure](/cli/azure/install-azure-cli) y [Empezar a trabajar con la CLI de Azure](/cli/azure/get-started-with-azure-cli).