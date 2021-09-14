---
title: Observabilidad del software de código abierto OSM
description: Procedimiento para configurar la observabilidad de código abierto para Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: ce03fc4007ad55485150feb715242d4cc216433e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440094"
---
# <a name="manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Implementación manual de Prometheus, Grafana y Jaeger para ver las métricas de Open Service Mesh (OSM) con fines de observabilidad

> [!WARNING]
> La instalación de Prometheus, Grafana y Jaeger se proporciona como guía general para mostrar cómo se pueden usar estas herramientas para ver los datos de métricas de OSM. La guía de instalación no se utiliza para una configuración de producción. Consulte la documentación de cada herramienta sobre la mejor manera de adaptarse a sus necesidades de instalación. Lo más notable es la falta de almacenamiento persistente, lo que significa que todos los datos se pierden una vez que se cierra un pod de Prometheus Grafana o Jaeger.

Open Service Mesh (OSM) genera métricas detalladas relacionadas con todo el tráfico dentro de la malla. Estas métricas proporcionan información sobre el comportamiento de las aplicaciones de la malla, ayudando a los usuarios a solucionar, mantener y analizar sus aplicaciones.

Hoy, OSM recopila las métricas directamente de los servidores proxy de sidecar (Envoy). OSM proporciona métricas enriquecidas para el tráfico entrante y saliente de todos los servicios de la malla. Con estas métricas, el usuario puede obtener información sobre el volumen total de tráfico, los errores dentro del tráfico y el tiempo de respuesta de las solicitudes.

OSM usa Prometheus para recopilar y almacenar estadísticas y métricas de tráfico coherentes para todas las aplicaciones que se ejecutan en la malla. Prometheus es un kit de herramientas de supervisión y alertas de código abierto, que se usa normalmente en entornos de Kubernetes y de malla de servicio (pero no exclusivamente).

Cada aplicación que forma parte de la malla se ejecuta en un pod que contiene un sidecar de Envoy que expone las métricas (métricas de proxy) en el formato de Prometheus. Además, cada pod que forma parte de la malla tiene anotaciones de Prometheus, lo que permite que el servidor de Prometheus desbloquee la aplicación de forma dinámica. Este mecanismo habilita automáticamente la extracción de métricas cada vez que se agrega un nuevo espacio de nombres/pod/servicio a la malla.

Las métricas de OSM se pueden ver con Grafana, que es un software de análisis y visualización de código abierto. Permite consultar, visualizar, enviar alertas y explorar las métricas.

En este tutorial va a:

> [!div class="checklist"]
>
> - Crear e implementar una instancia de Prometheus
> - Configurar OSM para permitir la extracción de Prometheus
> - Actualizar `Configmap` de Prometheus
> - Crear e implementar una instancia de Grafana
> - Configurar Grafana con el origen de datos de Prometheus
> - Importar el panel de OSM para Grafana
> - Crear e implementar una instancia de Jaeger
> - Configurar el seguimiento de Jaeger para OSM

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de empezar

Debe tener instalados los siguientes recursos:

- CLI de Azure, versión 2.20.0 o posterior
- Extensión `aks-preview`, versión 0.5.5 o posterior
- Versión v.0.8.0 o posterior de OSM
- Procesador JSON "jq" versión 1.6+

## <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Implementación y configuración de una instancia de Prometheus para OSM

Usaremos Helm para implementar la instancia de Prometheus. Ejecute los siguientes comandos para instalar Prometheus a través de Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Debería ver una salida similar a la siguiente si la instalación se realizó correctamente. Anote el puerto del servidor de Prometheus y el nombre DNS del clúster. Esta información se usará más adelante para configurar Prometheus como un origen de datos para Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

### <a name="configure-osm-to-allow-prometheus-scraping"></a>Configuración de OSM para permitir la extracción de Prometheus

Para asegurarse de que los componentes de OSM estén configurados para las extracciones de Prometheus, queremos comprobar la configuración de **prometheus_scraping** ubicada en el archivo de configuración de osm-config. Vea la configuración con el siguiente comando:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

La salida del comando anterior debe devolver `true` si OSM está configurado para la extracción de Prometheus. Si el valor devuelto es `false`, es necesario actualizar la configuración para que sea `true`. Ejecute el siguiente comando para **activar** la extracción de Prometheus en OMS:

```azurecli-interactive
kubectl patch configmap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Debería ver la siguiente salida.

```Output
configmap/osm-config patched
```

### <a name="update-the-prometheus-configmap"></a>Actualización del archivo Configmap de Prometheus

La instalación predeterminada de Prometheus contendrá dos elementos `configmaps` de Kubernetes. Puede ver la lista de elementos `configmaps` de Prometheus con el siguiente comando.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Tendrá que reemplazar la configuración de prometheus.yml que se encuentra en el elemento `configmap` **stable-prometheus-server** por la siguiente configuración de OSM. Hay varias técnicas de edición de archivos para realizar esta tarea. Una manera sencilla y segura consiste en exportar el elemento `configmap`, crear una copia de seguridad y, después, editarlo con un editor como Visual Studio Code.

> [!NOTE]
> Si no tiene Visual Studio Code instalado, puede descargarlo e instalarlo [aquí](https://code.visualstudio.com/Download).

Vamos a exportar primero el configmap **stable-prometheus-server** y luego realizar una copia de seguridad.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

A continuación, vamos a abrir el archivo con Visual Studio Code para editarlo.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Una vez que tenga el elemento `configmap` abierto en el editor de Visual Studio Code, reemplace el archivo prometheus.yml por la configuración de OSM que aparece a continuación y guarde el archivo.

> [!WARNING]
> Es muy importante asegurarse de que mantiene la estructura de la sangría del archivo YAML. Cualquier cambio en la estructura del archivo YAML podría provocar que el configmap no se pudiera volver a aplicar.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Aplique el archivo YAML de `configmap` actualizado con el siguiente comando.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Es posible que reciba un mensaje que indica que falta una anotación kubernetes necesaria. Esto puede pasarse por alto por ahora.

### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Comprobación de que Prometheus está configurado para extraer los puntos de conexión de la API y la malla de OSM

Para comprobar que Prometheus está correctamente configurado para extraer los puntos de conexión de la API y la malla de OSM, haremos un reenvío de puertos al pod de Prometheus y veremos la configuración de destino. Ejecute los comandos siguientes:

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Abra un explorador en `http://localhost:9090/targets`.

Si se desplaza hacia abajo, debería ver que el estado de los puntos de conexión de la métrica de SMI es **UP**, así como otras métricas de OSM definidas como se muestra a continuación.

![Imagen de la IU de métricas de destino de Prometheus de OSM](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

## <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Implementación y configuración de una instancia de Grafana para OSM

Usaremos Helm para implementar la instancia de Grafana. Ejecute los siguientes comandos para instalar Grafana a través de Helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

A continuación, recuperaremos la contraseña predeterminada de Grafana para iniciar sesión en el sitio de Grafana.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Anote la contraseña de Grafana.

A continuación, recuperaremos el pod de Grafana para hacer un reenvío de puertos al panel de Grafana para iniciar sesión.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Abra un explorador en `http://localhost:3000`.

En la pantalla de inicio de sesión que se muestra a continuación, escriba **admin** como nombre de usuario y use la contraseña de Grafana capturada anteriormente.

![Imagen de IU de la página de inicio de sesión de Grafana de OSM](./media/aks-osm-addon/osm-grafana-ui-login.png)

### <a name="configure-the-grafana-prometheus-data-source"></a>Configuración del origen de datos de Prometheus en Grafana

Una vez que haya iniciado sesión correctamente en Grafana, el siguiente paso es agregar Prometheus como orígenes de datos para Grafana. Para ello, vaya al icono de configuración en el menú de la izquierda y seleccione Orígenes de datos, como se muestra a continuación.

![Imagen de la interfaz de usuario de la página de orígenes de datos de Grafana de OSM](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Haga clic en el botón **Agregar origen de datos** y seleccione Prometheus en las bases de datos de serie temporal.

![Imagen de la interfaz de usuario de la página de selección de orígenes de datos de Grafana de OSM](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

En la página para **configurar el origen de datos de Prometheus**, escriba el FQDN del clúster de Kubernetes para el servicio Prometheus para la configuración de la dirección URL de HTTP. El FQDN predeterminado debe ser `stable-prometheus-server.default.svc.cluster.local`. Una vez que haya escrito ese punto de conexión de servicio Prometheus, desplácese hasta la parte inferior de la página y seleccione **Guardar y probar**. Debe recibir una casilla verde que indica que el origen de datos funciona.

### <a name="importing-osm-dashboards"></a>Importación de paneles de OSM

Los paneles de OSM están disponibles a través de:

- [Nuestro repositorio](https://github.com/grafana/grafana), y son importables como blobs de JSON a través del portal de administración web;
- o [en línea en Grafana.com](https://grafana.com/grafana/dashboards/14145).

Para importar un panel, busque el signo `+` en el menú de la izquierda y seleccione `import`.
Puede importar directamente el panel por su identificador en `Grafana.com`. Por ejemplo, el panel `OSM Mesh Details` usa el identificador `14145`, puede usar el identificador directamente en el formulario y seleccionar `import`:

![Imagen de la IU de importar página del panel de Grafana de OSM](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

En cuanto seleccione importar, se le llevará automáticamente al panel importado.

![Imagen de IU de la página de detalles de malla del panel de Grafana de OSM](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

## <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Implementación y configuración de un operador de Jaeger en Kubernetes para OSM

[Jaeger](https://www.jaegertracing.io/) es un sistema de seguimiento de código abierto que se usa para supervisar sistemas distribuidos y solucionar sus problemas. Se puede implementar con OSM como una nueva instancia o puede traer su propia instancia. Las instrucciones siguientes implementan una nueva instancia de Jaeger en el espacio de nombres `jaeger` del clúster de AKS.

### <a name="deploy-jaeger-to-the-aks-cluster"></a>Implementación de Jaeger en el clúster de AKS

Aplique el siguiente manifiesto para instalar Jaeger:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

### <a name="enable-tracing-for-the-osm-add-on"></a>Habilitación del seguimiento para el complemento OSM

A continuación, deberá habilitar el seguimiento para el complemento OSM.

Ejecute el comando siguiente para habilitar el seguimiento para el complemento OSM:

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}' --type=merge
```

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Visualización de la interfaz de usuario de Jaeger con el reenvío de puertos

La interfaz de usuario de Jaeger se está ejecutando en el puerto 16686. Para ver la interfaz de usuario web, puede usar el reenvío de puertos de kubectl:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

En el explorador, debería ver un desplegable de servicio, que le permite seleccionar entre las distintas aplicaciones implementadas por el servicio bookstore de demostración. Seleccione un servicio para ver todos sus intervalos. Por ejemplo, si selecciona `bookbuyer` con una retrospectiva de una hora, puede ver sus interacciones con bookstore-v1 y bookstore-v2 ordenadas por hora.

![Imagen de IU de página de seguimiento de Jaeger de OSM](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Seleccione cualquier elemento para verlo con más detalle. Seleccione varios elementos para comparar los seguimientos. Por ejemplo, puede comparar las interacciones de `bookbuyer` con bookstore y bookstore-v2 en un momento determinado en el tiempo.

También puede seleccionar la pestaña de arquitectura del sistema para ver un gráfico de cómo las distintas aplicaciones han estado interactuando o comunicándose. Esto proporciona una idea de cómo fluye el tráfico entre las aplicaciones.

![Imagen de IU de arquitectura de sistema de Jaeger de OSM](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)