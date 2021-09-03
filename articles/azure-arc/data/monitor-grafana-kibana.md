---
title: Visualización de registros y métricas mediante Kibana y Grafana
description: Visualización de registros y métricas mediante Kibana y Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: d8d18d296a54b85cb3b571099e13f8c3e4e7a9c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750244"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Visualización de registros y métricas mediante Kibana y Grafana

Los paneles web Kibana y Grafana se proporcionan para ofrecer información y claridad a los espacios de nombres de Kubernetes que usan los servicios de datos habilitados para Azure Arc.



## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Supervisión de instancias administradas de Azure SQL en Azure Arc

Para acceder a los paneles de registros y supervisión para SQL Managed Instance con ARC habilitado, ejecute el siguiente comando de la CLI `azdata`

```azurecl
az sql mi-arc endpoint list -n <name of SQL instance>

```
Los paneles de control de Grafana pertinentes son:

* "Métricas de la instancia administrada de Azure SQL"
* "Métricas del nodo de host"
* "Métricas de los pods de host"


> [!NOTE]
>  Cuando se le pida que escriba un nombre de usuario y una contraseña, escriba el nombre de usuario y la contraseña que proporcionó en el momento en que creó el controlador de datos de Azure Arc.

> [!NOTE]
>  Se le mostrará una advertencia de certificado porque los certificados son autofirmados.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Supervisar Azure DB for PostgreSQL para la hiperescala en Azure Arc

Para acceder a los registros y los paneles de supervisión de la hiperescala de PostgreSQL, ejecute el siguiente comando de la CLI `azdata`

```bash

az postgres arc-server endpoint list -n <name of postgreSQL instance> --k8s-namespace <namespace> --use-k8s

```

Los paneles de postgreSQL pertinentes son:

* "Métricas de Postgres"
* "Métricas de la tabla Postgres"
* "Métricas del nodo de host"
* "Métricas de los pods de host"


## <a name="additional-firewall-configuration"></a>Configuración adicional del firewall

Dependiendo de dónde se implemente el controlador de datos, puede que tenga que abrir los puertos en el firewall para tener acceso a los puntos de conexión Kibana y Grafana.

A continuación, se muestra un ejemplo del procedimiento para una máquina virtual de Azure. Tendrá que hacer esto si se ha implementado Kubernetes mediante el script.

En los pasos siguientes, se resalta cómo crear una regla de NSG para los puntos de conexión Kibana y Grafana:

### <a name="find-the-name-of-the-nsg"></a>Buscar el nombre del NSG

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Agregar la regla de NSG

Una vez que tenga el nombre del NSG, se puede agregar una regla usando el comando siguiente:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Pasos siguientes
- Intente [cargar métricas y registros en Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Obtenga información acerca de Grafana:
   - [Introducción](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Aspectos básicos de Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Tutoriales de Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Obtenga información acerca de Kibana:
   - [Introducción](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Guía de Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Introducción al panel de exploraciones en profundidad con visualizaciones de datos en Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Cómo crear paneles de Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

