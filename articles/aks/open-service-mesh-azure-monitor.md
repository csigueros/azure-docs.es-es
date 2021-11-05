---
title: Uso de Azure Monitor y Application Insights
description: Uso de Azure Monitor y Application Insights con Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 139c2dbd201791fd7960e66564a293545c3ca524
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066736"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Supervisión y observabilidad de Open Service Mesh (OSM) mediante Azure Monitor y Application Insights

Tanto Azure Monitor como Azure Application Insights ayudan a maximizar la disponibilidad y el rendimiento de sus aplicaciones y servicios. Azure Monitor ofrece una solución completa para recopilar y analizar la telemetría, así como actuar sobre ella, desde los entornos local y de nube.

El complemento OSM de AKS ofrecerá una mayor integración en ambos servicios de Azure y una experiencia fluida con Azure para ver los parámetros KPI críticos asociados a las métricas de OSM y responder a ellos.

## <a name="enable-azure-monitor"></a>Habilitación de Azure Monitor

Una vez que se ha habilitado el complemento de AKS de OSM en el clúster de AKS, Azure Monitor debe habilitarse en el clúster mediante Azure Portal. Haga clic en el clúster de AKS, vaya a la pestaña "Información detallada" en "Supervisión" y seleccione "Habilitar".

Una vez que Azure Monitor se ha habilitado, debería poder ver los pods siguientes en el espacio de nombres kube-system:

```
kube-system     omsagent-5pn4c                        1/1     Running   0          24m
kube-system     omsagent-6r6zt                        1/1     Running   0          24m
kube-system     omsagent-j8xrh                        1/1     Running   0          24m
kube-system     omsagent-rs-74b8f7dfd8-rp5vx          1/1     Running   1          24m
```

## <a name="enable-metrics-in-osm-monitored-namespaces"></a>Habilitación de métricas en los espacios de nombres supervisados por OSM

Para que las métricas se puedan extraer de un espacio de nombres determinado supervisado por la malla, es necesario ejecutar el siguiente comando:

```sh
osm metrics enable --namespace <namespace>
```

Por ejemplo, si ejecuta la [demostración de la tienda de libros](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/), ejecutaría el comando `osm metrics enable` en los siguientes espacios de nombres:

```sh
osm metrics enable --namespace bookbuyer
osm metrics enable --namespace bookstore
osm metrics enable --namespace bookthief
osm metrics enable --namespace bookwarehouse
```

## <a name="apply-configmap"></a>Aplicación de ConfigMap

Cree el siguiente objeto ConfigMap en `kube-system`, que le mostrará a AzMon qué espacios de nombres se deben supervisar. Por ejemplo, para la demostración del comprador de libros/tienda de libros, ConfigMap tendría el siguiente aspecto:

```yaml
kind: ConfigMap
apiVersion: v1
data:
  schema-version: v1
  config-version: ver1
  osm-metric-collection-configuration: |-
    # OSM metric collection settings
    [osm_metric_collection_configuration]
      [osm_metric_collection_configuration.settings]
          # Namespaces to monitor
          monitor_namespaces = ["bookstore", "bookbuyer", "bookthief", "bookwarehouse"]
metadata:
  name: container-azm-ms-osmconfig
  namespace: kube-system
```

## <a name="view-metrics-in-the-azure-portal"></a>Visualización de métricas en Azure Portal

En Azure Portal, seleccione el clúster de Kubernetes y, luego, la pestaña "Registros" en "Supervisión". Ahora debería poder consultar la tabla `InsightsMetrics` para ver las métricas en los espacios de nombres habilitados. Por ejemplo, si quisiera ver las métricas de los enviados de `bookbuyer`, usaría la consulta siguiente:

```sh
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "bookbuyer"
```

## <a name="additional-information"></a>Información adicional

Para más información sobre cómo habilitar y configurar Azure Monitor y Azure Application Insights para el complemento OSM de AKS, visite la página [Azure Monitor para OSM](https://aka.ms/azmon/osmpreview).
