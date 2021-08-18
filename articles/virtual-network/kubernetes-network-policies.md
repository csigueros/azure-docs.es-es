---
title: Directivas de red de Azure Kubernetes | Microsoft Docs
description: Obtenga información sobre las directivas de red de Kubernetes para proteger su clúster de Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 96e055205c5c016504cd6246b9c1b6d1533ad6ac
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736178"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Introducción a las directivas de red de Azure Kubernetes

Las directivas de red proporcionan microsegmentación para pods del mismo modo que los grupos de seguridad de red proporcionan microsegmentación para máquinas virtuales. La implementación de Azure Network Policy Manager (conocido también como Azure NPM) admite la especificación de la directiva de red de Kubernetes estándar. Puede usar etiquetas para seleccionar un grupo de pods y definir una lista de reglas de entrada y salida para filtrar tanto el tráfico que llega a estos pods como el que sale de ellos. Obtenga más información sobre las directivas de red de Kubernetes en la [documentación de Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Introducción a las directivas de red de Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

La implementación de Azure NPM funciona conjuntamente con Azure CNI, que proporciona integración con red virtual a los contenedores. En la actualidad, NPM solo se admite en Linux. La implementación aplica el filtrado de tráfico mediante la configuración de las reglas de permiso y denegación de IP en Linux IPTables en función de las directivas definidas. Estas reglas se agrupan mediante Linux IPSets.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planeación de seguridad para el clúster de Kubernetes
Al implementar la seguridad del clúster, utilice grupos de seguridad red para filtrar el tráfico que entra y sale de la subred del clúster (tráfico de norte a sur). Use Azure NPM para el tráfico entre los pods del clúster (tráfico de este a oeste).

## <a name="using-azure-npm"></a>Uso de Azure NPM
Azure NPM se puede usarse de las siguientes formas para proporcionar microsegmentación para los pods.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
NPM está disponible de forma nativa en AKS y se puede habilitar en el momento de la creación del clúster. Para más información al respecto, consulte [Protección del tráfico entre pods mediante directivas de red en Azure Kubernetes Service (AKS)](../aks/use-network-policies.md).

### <a name="aks-engine"></a>AKS-engine
AKS-Engine es una herramienta que genera una plantilla de Azure Resource Manager para la implementación de un clúster de Kubernetes en Azure. La configuración del clúster se especifica en un archivo JSON que se pasa a la herramienta al generar la plantilla. Para ver la lista completa de las configuraciones de clúster compatibles y sus descripciones, consulte Microsoft Azure Container Service Engine - Cluster Definition (Motor de Microsoft Azure Container Service: definición de clúster).

Para habilitar las directivas en clústeres implementados con motor de ACS, especifique el valor de la configuración de networkPolicy en el archivo de definición del clúster como "azure".

#### <a name="example-configuration"></a>Ejemplo de configuración

En la siguiente configuración de ejemplo JSON se crea una nueva red virtual y una subred e se implementa un clúster de Kubernetes en ella con CNI de Azure. Se recomienda que utilice "Notepad" para editar el archivo JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Clústeres de Kubernetes personales (DIY) en Azure
 En el caso de los clústeres DIY, en primer lugar, debe instalar el complemento de CNI y habilitarlo en todas las máquinas virtuales de un clúster. Para instrucciones detalladas, consulte [Implementación del complemento para un clúster de Kubernetes que usted mismo haya implementado](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Una vez implementado el clúster, ejecute el siguiente comando `kubectl` para descargar y aplicar el *conjunto de demonios* de Azure NPM al clúster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
La solución también es de código abierto y el código está disponible en el [repositorio de redes de Azure Container](https://github.com/Azure/azure-container-networking/tree/master/npm).

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Supervisión y visualización de configuraciones de red con Azure NPM
Azure NPM incluye métricas de Prometheus informativas que permiten supervisar y comprender mejor las configuraciones. Proporciona visualizaciones integradas en Azure Portal o en Grafana Labs. Puede empezar a recopilar estas métricas mediante Azure Monitor o un servidor de Prometheus.

### <a name="benefits-of-azure-npm-metrics"></a>Ventajas de las métricas de Azure NPM
Anteriormente, los usuarios solo podían obtener información sobre su configuración de red con el comando `iptables -L`, que se ejecutaba en un nodo del clúster, lo que genera una salida detallada y difícil de entender. Las métricas de NPM proporcionan las siguientes ventajas relacionadas con las directivas de red, las reglas de IPTables y los IPSets.
- Proporciona una visión general de la relación entre las tres y una dimensión temporal para depurar una configuración.
- Número de entradas de todos los IPSets y de cada uno de ellos.
- Tiempo necesario para aplicar una directiva con granularidad de nivel de IPTable o IPSet.
 
### <a name="supported-metrics"></a>Métricas compatibles
A continuación, se muestra una lista de métricas compatibles:

|Nombre de la métrica |Descripción  |Tipo de métrica de Prometheus  |Etiquetas  |
|---------|---------|---------|---------|
|`npm_num_policies`     |número de directivas de red          |Indicador         |-         |
|`npm_num_iptables_rules`     | número de reglas de IPTables     | Indicador        |-         |         
|`npm_num_ipsets`     |número de IPSets         |Indicador            |-         |
|`npm_num_ipset_entries`     |número de entradas de dirección IP en todos los IPSets         |Indicador         |-         |
|`npm_add_policy_exec_time`     |runtime para agregar una directiva de red         |Resumen         |cuantil (0,5; 0,9 o 0,99)         |
|`npm_add_iptables_rule_exec_time`     |runtime para agregar una regla IPTables         |Resumen         |cuantil (0,5; 0,9 o 0,99)         |
|`npm_add_ipset_exec_time`     |runtime para agregar un IPSet         |Resumen         |cuantil (0,5; 0,9 o 0,99)         |
|`npm_ipset_counts` (avanzado)     |número de entradas dentro de cada IPSet individual         |GaugeVec         |establecer nombre y hash         |

Los distintos niveles de cuantil en las métricas de "exec_time" le ayudan a diferenciar entre los escenarios generales y los peores escenarios.

Hay también una métrica "exec_time_count" y "exec_time_sum" para cada métrica de resumen "exec_time".

Las métricas se pueden descartar mediante Azure Monitor para contenedores o de Prometheus.

### <a name="setup-for-azure-monitor"></a>Configuración de Azure Monitor
El primer paso es habilitar Azure Monitor para contenedores para un clúster de Kubernetes. Los pasos se pueden encontrar en [Introducción a Azure Monitor para contenedores](../azure-monitor/containers/container-insights-overview.md). Una vez que Azure Monitor para contenedores está habilitado, configure [ConfigMap de Azure Monitor para contenedores](https://aka.ms/container-azm-ms-agentconfig) para habilitar la integración de NPM y la recopilación de métricas de NPM de Prometheus. ConfigMap Azure Monitor para contenedores tiene una sección ```integrations``` con la configuración para recopilar métricas de NPM. Esta configuración está deshabilitada de forma predeterminada en ConfigMap. La habilitación de la configuración básica ```collect_basic_metrics = true``` se recopilarán las métricas de NPM básicas. La habilitación de la configuración avanzada ```collect_advanced_metrics = true``` recopilará métricas avanzadas, además de las métricas básicas. 

Después de editar ConfigMap, guárdelo localmente y aplique ConfigMap al clúster como se indica a continuación.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` A continuación encontrará un fragmento de código de [ConfigMap de Azure Monitor para contenedores](https://aka.ms/container-azm-ms-agentconfig), que muestra la integración de NPM habilitada con la recopilación de métricas avanzadas.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Las métricas avanzadas son opcionales y al activarlas, se activará automáticamente la recopilación de métricas básicas. Actualmente, las métricas avanzadas solo incluyen `npm_ipset_counts`

Más información acerca de la [configuración de la colección de la recopilación de Azure Monitor para contenedores en ConfigMap](../azure-monitor/containers/container-insights-agent-config.md)

### <a name="visualization-options-for-azure-monitor"></a>Opciones de visualización para Azure Monitor
Una vez habilitada la recopilación de métricas de NPM, éstas se pueden ver en Azure Portal mediante Container Insights o en Grafana.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Visualización en Azure Portal en la sección Información del clúster
Abra Azure Portal. Una vez en la sección Información del clúster, vaya a "Libros" y abra "Configuración de Policy Manager (NPM)".

Además de ver el libro (encontrará las imágenes más abajo), también puede consultar directamente las métricas de Prometheus en "Registros", en la sección Información. Por ejemplo, esta consulta devolverá todas las métricas que se recopilan.
| where TimeGenerated > ago(5h) | where Name contains "npm_"

También puede consultar las métricas directamente en Log Analytics. Más información al respecto en [Introducción a las consultas de Log Analytics](../azure-monitor/containers/container-insights-log-query.md) 

#### <a name="viewing-in-grafana-dashboard"></a>Visualización en un panel de Grafana
Configure un servidor de Grafana y un origen de datos de Log Analytics como se describe [aquí](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource). Luego, importe el [panel de Grafana con un back-end de Log Analytics](https://grafana.com/grafana/dashboards/10956) en Grafana Labs.

El panel tiene objetos visuales similares al libro de Azure. Puede agregar paneles para crear gráficos y visualizar métricas de NPM desde la tabla InsightsMetrics.

### <a name="setup-for-prometheus-server"></a>Configuración del servidor de Prometheus
Algunos usuarios pueden optar por recopilar métricas con un servidor de Prometheus, en lugar de con Azure Monitor para contenedores. Basta con agregar dos trabajos a la configuración de rechazo para recopilar métricas de NPM.

Para instalar un servidor de Prometheus simple, agregue este repositorio de Helm al clúster
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
luego agregue un servidor
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
donde `prometheus-server-scrape-config.yaml` consta de
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


También puede reemplazar el trabajo `azure-npm-node-metrics` por el contenido que aparece a continuación, o bien incorporarlo a un trabajo existente para pods de Kubernetes:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Opciones de visualización para Prometheus
Cuando se usa un servidor de Prometheus, solo se admite el panel de Grafana. 

Si aún no lo ha hecho, configure el servidor de Grafana y, después, un origen de datos de Prometheus. Luego, importe nuestro [panel de Grafana con un back-end de Prometheus](https://grafana.com/grafana/dashboards/13000) en Grafana Labs.

Los objetos visuales de este panel son idénticos los del panel con back-end de información de Container Insights o Log Analytics.

### <a name="sample-dashboards"></a>Paneles de ejemplo
A continuación, encontrará un panel de ejemplo para las métricas de NPM en Container Insights (CI) y Grafana

#### <a name="ci-summary-counts"></a>Resumen de recuentos de CI
![Resumen de recuentos del libro de Azure](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>Recuentos de CI con el paso del tiempo
[![Recuentos del libro de Azure a lo largo del tiempo](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>Entradas de IPSet de CI
[![Entradas de IPSet del libro de Azure](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>Cuantiles en tiempo de ejecución de CI
![Cuantiles en tiempo de ejecución del libro de Azure](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Resumen de recuentos del panel de Grafana
![Resumen de recuentos del panel de Grafana](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Recuentos del panel de Grafana a lo largo del tiempo
[![Recuentos del panel de Grafana a lo largo del tiempo](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Entradas de IPSet del panel de Grafana
[![Entradas de IPSet del panel de Grafana](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Cuantiles en tiempo de ejecución del panel de Grafana
[![Cuantiles en tiempo de ejecución del panel de Grafana](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información acerca de [Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Obtenga más información acerca de [redes de contenedores](container-networking-overview.md).
- [Implemente el complemento](deploy-container-networking.md) para clústeres de Kubernetes o contenedores de Docker.

