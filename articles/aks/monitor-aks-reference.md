---
title: Supervisión de la referencia de datos de AKS
description: Material de referencia importante necesario para la supervisión de AKS
ms.service: container-service
ms.custom: subject-monitoring
ms.date: 07/29/2021
ms.topic: reference
ms.openlocfilehash: 9e8a12f8d3075bade1619ce766c74dc8cac23481
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816049"
---
# <a name="monitoring-aks-data-reference"></a>Supervisión de la referencia de datos de AKS

Vea [Supervisión de AKS](monitor-aks.md) para obtener más información sobre la recopilación y el análisis de datos de supervisión de AKS.

## <a name="metrics"></a>Métricas

En la siguiente tabla se indican las métricas de plataforma recopiladas para AKS.  Siga cada vínculo para obtener una lista detallada de las métricas de cada tipo en particular.

|Tipo de métrica | Espacio de nombres de proveedor de recursos/tipo<br/> y vínculo a métricas individuales |
|-------|-----|
| Clústeres administrados | [Microsoft.ContainerService/managedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters)
| Clústeres conectados | [microsoft.kubernetes/connectedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftkubernetesconnectedclusters)
| Máquinas virtuales| [Microsoft.Compute/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
| Conjuntos de escalado de máquinas virtuales | [Microsoft.Compute/virtualMachineScaleSets](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
| Máquinas virtuales de los conjuntos de escalado de máquinas virtuales | [Microsoft.Compute/virtualMachineScaleSets/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)|

Para obtener más información, vea una lista de [todas las métricas de la plataforma que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="metric-dimensions"></a>Dimensiones de métricas

En la siguiente tabla, se enumeran las [dimensiones](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics) para las métricas de AKS. 

<!-- listed here /azure/azure-monitor/essentials/metrics-supported#microsoftcontainerservicemanagedclusters-->

| Nombre de dimensión | Descripción |
| ------------------- | ----------------- |
| requestKind | La usan métricas como *Inflight Requests* (Solicitudes en proceso) para realizar la división por tipo de solicitud. |
| condición | La usan métricas como *Statuses for various node conditions* (Estados de diversas condiciones de nodo), *Number of pods in Ready state* (Número de pods con estado Listo) para realizar la división por tipo de condición. |
| status | La usan métricas como *Statuses for various node conditions* (Estados de diversas condiciones de nodo) para realizar la división por estado de la condición. |
| status2 | La usan métricas como *Statuses for various node conditions* (Estados de diversas condiciones de nodo) para realizar la división por estado de la condición.  |
| Nodo | La usan métricas como *Uso de CPU (millares de núcleos)* para realizar la división por nombre del nodo. |
| phase | La usan métricas como *Number of pods by phase* (Número de pods por fase) para realizar la división por fase del pod. |
| espacio de nombres | La usan métricas como *Number of pods by phase* (Número de pods por fase) para realizar la división por espacio de nombres del pod. |
| pod | La usan métricas como *Number of pods by phase* (Número de pods por fase) para realizar la división por nombre del pod. |
| grupo de nodos | La usan métricas como *Disk Used Bytes* (Bytes usados en disco) para realizar la división por nombre del grupo de nodos. |
| device | La usan métricas como *Disk Used Bytes* (Bytes usados en disco) para realizar la división por nombre del dispositivo. |

## <a name="resource-logs"></a>Registros del recurso

En la tabla siguiente, se enumeran las categorías de registro de recursos que puede recopilar para AKS. Estos son los registros de los componentes del plano de control de AKS. Consulte [Configuración de la supervisión](monitor-aks.md#configure-monitoring) para obtener información sobre cómo crear una configuración de diagnóstico para recopilar estos registros y recomendaciones sobre cuál habilitar. Consulte [Procedimiento para consultar registros desde Container Insights](../azure-monitor/containers/container-insights-log-query.md#resource-logs) para ver ejemplos de consulta.

Como referencia, vea una lista de [todos los tipos de categorías de registros de recursos admitidos en Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md). 

| Category                | Descripción |
|:---|:---|
| cluster-autoscaler       | Comprenda por qué el clúster de AKS se está escalando o reduciendo verticalmente, lo que puede que no sea lo esperado. Esta información también es útil para poner en correlación los intervalos de tiempo en los que puede haber ocurrido algo interesante en el clúster. |
| guard                   | Auditorías del control de acceso basado en roles de Azure y Azure Active Directory administrado. Para Azure AD administrado, esto incluye el token dentro y la información de usuario fuera. Para Azure RBAC, esto incluye las revisiones de acceso dentro y fuera. |
| kube-apiserver          | Registros del servidor de API. |
| kube-audit              | Datos del registro de auditoría para cada evento de auditoría, incluidos get, list, create, update, delete, patch y post. |
| kube-audit-admin        | Subconjunto de la categoría de registro kube-audit. Reduce considerablemente el número de registros al excluir los eventos de auditoría get y list del registro. |
| kube-controller-manager | Obtenga una mayor visibilidad de los problemas que pueden surgir entre Kubernetes y el plano de control de Azure. Un ejemplo típico es que el clúster de AKS no tenga permisos para interactuar con Azure. |
| kube-scheduler          | Registros del programador. |
| AllMetrics              | Incluye todas las métricas de la plataforma. Envía estos valores al área de trabajo de Log Analytics, donde se puede realizar una evaluación con otros datos mediante consultas de registro. |

## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor

En esta sección, se hace referencia a todas las tablas de registros de Azure Monitor relacionadas con AKS y que están disponibles para consulta mediante Log Analytics. 



|Tipo de recurso | Notas |
|-------|-----|
| [Servicios de Kubernetes](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services) | Siga este vínculo para obtener una lista de todas las tablas que usa AKS y una descripción de su estructura. |


Puede encontrar una referencia de todas las tablas de registros de Azure Monitor o de Log Analytics en [Referencia de la tabla de registros de Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).


## <a name="activity-log"></a>Registro de actividades

En la tabla siguiente, se indican unas cuantas operaciones de ejemplo relacionadas con AKS que se pueden crear en el [registro de actividad](../azure-monitor/essentials/activity-log.md). Use el registro de actividad para realizar un seguimiento de datos como, por ejemplo, cuándo se creó un clúster o se cambió su configuración. Puede ver esta información en el portal o crear una alerta del registro de actividad para recibir notificaciones de forma proactiva cuando se produzca un evento.

| Operación | Descripción |
|:---|:---|
| Microsoft.ContainerService/managedClusters/write | Crear o actualizar el clúster administrado |
| Microsoft.ContainerService/managedClusters/delete | Eliminar el clúster administrado |
| Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | Enumerar la credencial clusterMonitoringUser |
| Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Mostrar la credencial clusterAdmin |
| Microsoft.ContainerService/managedClusters/agentpools/write | Crear o actualizar el grupo de agentes |

Para obtener una lista completa de las posibles entradas de registro, vea [Opciones del proveedor de recursos Microsoft.ContainerService](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).

Para obtener más información sobre el esquema de las entradas del registro de actividad, vea [Esquema del registro de actividad](../azure-monitor/essentials/activity-log-schema.md). 

## <a name="see-also"></a>Vea también

- Vea [Supervisión de Azure AKS](monitor-aks.md) para obtener una descripción de la supervisión de Azure AKS.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).