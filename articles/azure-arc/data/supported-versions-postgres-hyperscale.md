---
title: Versiones admitidas de Postgres con Hiperescala de PostgreSQL habilitada para Azure Arc
description: Versiones admitidas de Postgres con Hiperescala de PostgreSQL habilitada para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 2afdf621b2d5a7ba0b5b08669a892ed11f90826d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562815"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Versiones admitidas de Postgres con Hiperescala de PostgreSQL habilitada para Azure Arc
La lista de versiones admitidas evoluciona con el tiempo a medida que se avanza en garantizar la paridad con los servicios administrados de Postgres en PaaS de Azure. Actualmente, las versiones principales admitidas son las siguientes:
- Postgres 12 (versión predeterminada)
- Postgres 11


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Procedimiento para elegir entre versiones
Se recomienda consultar para qué versiones se han diseñado las aplicaciones y cuáles son las capacidades de cada una de las versiones. Para obtener más detalles, lea la información de cada versión en el sitio oficial de Postgres:
- [Postgres 12 (versión predeterminada)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Procedimiento para crear una versión determinada en Hiperescala de PostgreSQL habilitada para Azure Arc
En el momento de la creación, tiene la posibilidad de indicar qué versión se va a crear pasando el parámetro _--engine-version_. Si no indica la información de versión, de forma predeterminada, se creará un grupo de servidores de la versión 12 de Postgres.

Tenga en cuenta que solo hay una definición de recursos personalizados (CRD) de Postgres en el clúster de Kubernetes independientemente de las versiones que se admitan.
Por ejemplo, ejecute el siguiente comando:
```console
kubectl get crds
```

Se devolverá una salida como la siguiente:
```console
NAME                                                             CREATED AT
dags.sql.arcdata.microsoft.com                                   2021-10-12T23:53:40Z
datacontrollers.arcdata.microsoft.com                            2021-10-13T01:00:27Z
exporttasks.tasks.arcdata.microsoft.com                          2021-10-12T23:53:39Z
healthstates.azmon.container.insights                            2021-10-12T19:04:44Z
monitors.arcdata.microsoft.com                                   2021-10-13T01:00:26Z
postgresqls.arcdata.microsoft.com                                2021-10-12T23:53:37Z
sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com   2021-10-12T23:53:38Z
sqlmanagedinstances.sql.arcdata.microsoft.com                    2021-10-12T23:53:37Z
```

En este ejemplo, esta salida indica que hay una CRD relacionada con Postgres: postgresqls.arcdata.microsoft.com, nombre corto postgresqls. La CRD no es una instancia de Postgres ni un grupo de servidores de Postgres. La presencia de una CRD no indica que se haya creado o no un grupo de servidores. La CRD indica el tipo de recursos que se pueden crear en el clúster de Kubernetes.

## <a name="how-can-i-be-notified-when-other-versions-are-available"></a>Procedimiento para recibir notificaciones si hay otras versiones disponibles
Vuelva a consultar este artículo más adelante, se actualizará según corresponda.


## <a name="next-steps"></a>Pasos siguientes:
- [Obtenga información sobre cómo crear Hiperescala de PostgreSQL habilitada para Azure Arc](create-postgresql-hyperscale-server-group.md).
- [Obtenga información sobre cómo obtener una lista de los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc que se han creado en el controlador de datos de Arc](list-server-groups-postgres-hyperscale.md).
