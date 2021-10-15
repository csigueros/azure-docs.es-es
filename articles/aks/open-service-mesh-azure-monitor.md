---
title: Uso de Azure Monitor y Application Insights
description: Uso de Azure Monitor y Application Insights con Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 4e1df1275e225b42bf01bd2a4092cea07f9c4c12
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057821"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Supervisión y observabilidad de Open Service Mesh (OSM) mediante Azure Monitor y Application Insights

Tanto Azure Monitor como Azure Application Insights ayudan a maximizar la disponibilidad y el rendimiento de sus aplicaciones y servicios. Azure Monitor ofrece una solución completa para recopilar y analizar la telemetría, así como actuar sobre ella, desde los entornos local y de nube.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

El complemento OSM de AKS ofrecerá una mayor integración en ambos servicios de Azure y una experiencia fluida con Azure para ver los parámetros KPI críticos asociados a las métricas de OSM y responder a ellos. 

## <a name="enable-azure-monitor"></a>Habilitación de Azure Monitor

Una vez que se ha habilitado el complemento de AKS de OSM en el clúster de AKS, Azure Monitor debe habilitarse en el clúster mediante Azure Portal. Haga clic en el clúster de AKS, vaya a la pestaña "Información detallada" en "Supervisión" y seleccione "Habilitar". 

Una vez que Azure Monitor se ha habilitado, debería poder ver los siguientes registros en el espacio de nombres kube-system: 

```
kube-system     omsagent-5pn4c                        1/1     Running   0          24m
kube-system     omsagent-6r6zt                        1/1     Running   0          24m
kube-system     omsagent-j8xrh                        1/1     Running   0          24m
kube-system     omsagent-rs-74b8f7dfd8-rp5vx          1/1     Running   1          24m
```

## <a name="enable-metrics-in-osm-monitored-namespaces"></a>Habilitación de métricas en los espacios de nombres supervisados por OSM

Para que las métricas se puedan extraer de un espacio de nombres determinado supervisado por la malla, es necesario ejecutar el siguiente comando:

```sh
osm metrics enable --osm-namespace <namespace>
```

Por ejemplo, si ejecuta la [demostración de la tienda de libros](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/), ejecutaría el comando `osm metrics enable` en los siguientes espacios de nombres:

```sh
osm metrics enable --osm-namespace bookbuyer
osm metrics enable --osm-namespace bookstore
osm metrics enable --osm-namespace bookthief
osm metrics enable --osm-namespace bookwarehouse
```
## <a name="apply-configmaps"></a>Aplicación de ConfigMaps

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

A continuación, es necesario crear un segundo objeto ConfigMap para establecer [monitor_kubernetes_pods en true](https://github.com/microsoft/Docker-Provider/blob/24b709f9e3c3b18779102b491fc98b87a99d1335/kubernetes/container-azm-ms-agentconfig.yaml#L72).

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: container-azm-ms-agentconfig
  namespace: kube-system
data:
  schema-version: v1
  config-version: ver1
  prometheus-data-collection-settings: |-
    [prometheus_data_collection_settings.cluster]
        interval = "30s"
        monitor_kubernetes_pods = true
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