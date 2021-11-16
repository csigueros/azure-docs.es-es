---
title: Visualización del inventario de las instancias en Azure Portal
description: Visualización del inventario de las instancias en Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 5a5f13c74799e172d7ce9aefb170626cf61eb98e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558789"
---
# <a name="inventory-of-arc-enabled-data-services"></a>Inventario de servicios de datos habilitados para Arc


## <a name="view-resources-in-azure-portal"></a>Visualización de recursos en Azure Portal

Después de cargar las [métricas, los registros](upload-metrics-and-logs-to-azure-monitor.md) o el [uso](view-billing-data-in-azure.md), puede ver las instancias administradas de SQL habilitadas para Azure Arc o los grupos de servidores de Hiperescala de Postgres habilitados para Azure Arc en Azure Portal. Para ver el recurso en el [portal](https://portal.azure.com), siga estos pasos:

1. Vaya a **Todos los servicios**.
1. Busque el tipo de instancia de base de datos.
1. Agregue el tipo a sus favoritos.
1. En el menú de la izquierda, seleccione el tipo de instancia.
1. Vea las instancias en la misma vista que los demás recursos de Hiperescala de Azure SQL o Azure PostgreSQL (use filtros para obtener una vista más detallada).

## <a name="view-resources-in-your-kubernetes-cluster"></a>Visualización de recursos en el clúster de Kubernetes

Si el controlador de datos de Azure Arc se implementa en modo de conectividad **indirecta**, puede ejecutar el siguiente comando para obtener una lista de todas las instancias administradas de SQL de Azure Arc:
```
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
#Example
az sql mi-arc list --k8s-namespace arc --use-k8s
```

Si el controlador de datos de Azure Arc se implementa en modo de conectividad **directa**, puede ejecutar el siguiente comando para obtener una lista de todas las instancias administradas de SQL de Azure Arc:
```
az sql mi-arc list --resource-group <resourcegroup>
#Example
az sql mi-arc list --resource-group myResourceGroup
```
