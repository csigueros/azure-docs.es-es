---
title: ¿Qué es Hiperescala de PostgreSQL habilitada para Azure Arc?
description: ¿Qué es Hiperescala de PostgreSQL habilitada para Azure Arc?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d43753084f79184b338b16223ed45cb00a2dfb33
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732465"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>¿Qué es Hiperescala de PostgreSQL habilitada para Azure Arc?

Hiperescala de PostgreSQL habilitada para Azure Arc es uno de los servicios de base de datos disponibles como parte de los servicios de datos habilitados para Azure Arc. Azure Arc permite ejecutar servicios de datos de Azure en el entorno local, en el perímetro y en nubes públicas con Kubernetes y la infraestructura de su elección. La propuesta de valor de los servicios de datos habilitados para Azure Arc se articula sobre lo siguiente:
- Siempre actual
- Escalado elástico
- Aprovisionamiento de autoservicio
- Administración unificada
- Compatibilidad con los escenarios sin conexión

Más información en:
- [¿Qué son los servicios de datos habilitados para Azure Arc?](overview.md)
- [Modos de conectividad y requisitos](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Para obtener más información sobre estas funcionalidades, también puede ver este episodio de Data Exposed.
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]

## <a name="compare-solutions"></a>Comparación de soluciones

En esta sección se describe el modo en que Hiperescala de PostgreSQL habilitada para Azure Arc difiere de Hiperescala (Citus) de Azure Database for PostgreSQL.

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Hiperescala (Citus) de Azure Database for PostgreSQL

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Hiperescala (Citus) de Azure SQL Database for PostgreSQL":::

Este es el factor de forma de hiperescala del motor de base de datos de Postgres disponible como base de datos como servicio en Azure (PaaS). Cuenta con tecnología de la extensión Citus, que habilita la experiencia de la hiperescala. En este factor de forma, el servicio se ejecuta en los centros de datos de Microsoft y está operado por Microsoft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Hiperescala de PostgreSQL habilitada para Azure Arc

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Hiperescala de PostgreSQL habilitada para Azure Arc":::

Este es el factor de forma de hiperescala del motor de base de datos de Postgres disponible con los servicios de datos habilitados para Azure Arc. También cuenta con tecnología de la extensión Citus, que habilita la experiencia de la hiperescala. En este factor de forma, los clientes proporcionan la infraestructura que hospeda los sistemas y los operan.

## <a name="next-steps"></a>Pasos siguientes
- **Pruébelo.** Empiece a trabajar rápidamente con el [Inicio rápido de Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) en Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure. 

- **Cree el suyo propio.** Siga estos pasos para crear en su propio clúster de Kubernetes: 
   1. [Instalación de las herramientas de cliente](install-client-tools.md)
   2. [Creación del controlador de datos de Azure Arc](create-data-controller.md)
   3. [Creación de un grupo de servidores Hiperescala de Azure Database for PostgreSQL en Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Más información sobre los servicios de datos habilitados para Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Obtenga información sobre Azure Arc](https://aka.ms/azurearc).
