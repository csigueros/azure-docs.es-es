---
title: Enumeración de los grupos de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc creados en un controlador de datos de Azure Arc
description: Enumeración de los grupos de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc creados en un controlador de datos de Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 95bda32a3ffc305a4523e952f0cc975917996292
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555312"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Enumeración de los grupos de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc creados en un controlador de datos de Azure Arc

En este artículo se explica cómo se puede recuperar la lista de grupos de servidores creados en el controlador de datos de Arc.

Para recuperar esta lista, use cualquiera de los métodos siguientes una vez que esté conectado al controlador de datos de Arc:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azure-cli-extension-az"></a>Desde la CLI con la extensión de la CLI de Azure (az)

El formato general del comando es el siguiente:
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

Devolverá un resultado como este:
```console
[
  {
    "name": "postgres01",
    "replicas": 1,
    "state": "Ready",
    "workers": 4
  }
]
```
Para obtener información detallada acerca de los parámetros disponibles para este comando, ejecute lo siguiente:
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>Desde la CLI con kubectl
Ejecute uno de los comandos siguientes:

**Para enumerar los grupos de servidores con independencia de la versión de Postgres, ejecute lo siguiente:**
```console
kubectl get postgresqls -n <namespace>
```
Devolverá un resultado como este:
```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          12.345.67.890:5432   12d
```

## <a name="next-steps"></a>Pasos siguientes:

* [Lea el artículo sobre cómo obtener los puntos de conexión y formar las cadenas de conexión para conectarse a su grupo de servidores](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
* [Lea el artículo sobre mostrar la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc](show-configuration-postgresql-hyperscale-server-group.md).
