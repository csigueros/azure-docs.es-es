---
title: Solución de problemas de Open Service Mesh
description: Procedimiento para solucionar problemas de Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 0394815b4e75fb8d1f1f277010602839dc6876eb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440078"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Guías de solución de problemas del complemento Open Service Mesh (OSM) de AKS

Al implementar el complemento OSM de AKS, es posible que experimente problemas asociados con la configuración de la malla de servicio. La guía siguiente le ayudará a solucionar problemas y resolver errores comunes.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verifying-and-troubleshooting-osm-components"></a>Comprobación y solución de problemas de componentes de OSM

### <a name="check-osm-controller-deployment"></a>Comprobación de la implementación del controlador de OSM

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Un controlador de OSM correcto tendría el siguiente aspecto:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="check-the-osm-controller-pod"></a>Comprobación del pod del controlador de OSM

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Un pod de OSM correcto tendría el siguiente aspecto:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

A pesar de que un controlador fue expulsado en algún momento, tenemos otro con el estado READY 1/1 y en ejecución con 0 reinicios. Si el valor de la columna READY es distinto de 1/1, la malla del servicio estaría en un estado interrumpido.
La columna READY con 0/1 indica que el contenedor del plano de control se está bloqueando: necesitamos obtener registros. Consulte la sección de obtención de registros del controlador de OSM del centro de soporte técnico de Azure a continuación. Una columna READY con un número mayor que 1 después de / indicaría que hay sidecars instalados. Lo más probable es que el controlador de OSM no funcione con los sidecars conectados a él.

### <a name="check-osm-controller-service"></a>Comprobación del servicio de controlador de OSM

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Un servicio de controlador de OSM correcto tendría el siguiente aspecto:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> CLUSTER-IP sería diferente. Los valores NAME y PORT(S) del servicio deben ser iguales al ejemplo anterior.

### <a name="check-osm-controller-endpoints"></a>Comprobación de los puntos de conexión del controlador de OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Un punto de conexión del controlador de OSM correcto tendría un aspecto similar al siguiente:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

### <a name="check-osm-injector-deployment"></a>Comprobación de la implementación del inyector de OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Una implementación correcta del inyector de OSM tendría el siguiente aspecto:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-pod"></a>Comprobación del pod del inyector de OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Un pod correcto del inyector de OSM tendría el siguiente aspecto:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-service"></a>Comprobación del servicio del inyector de OSM

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Un servicio correcto del inyector de OSM tendría el siguiente aspecto:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

### <a name="check-osm-endpoints"></a>Comprobación de puntos de conexión de OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Un punto de conexión de OSM correcto tendría el siguiente aspecto:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

### <a name="check-validating-and-mutating-webhooks"></a>Comprobación de los webhooks de validación y mutación

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Un webhook de validación de OSM correcto tendría el siguiente aspecto:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Un webhook de mutación de OSM correcto tendría el siguiente aspecto:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Comprobación del servicio y el paquete de CA del webhook de validación

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Una configuración de webhook de validación bien configurada tendría un aspecto similar al siguiente:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Comprobación del servicio y el paquete de CA del webhook de mutación

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Una configuración de webhook de mutación bien configurada tendría un aspecto similar al siguiente:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Comprobación de si el controlador de OSM ha dado al webhook de validación (o de mutación) un paquete de CA

> [!NOTE]
> A partir de v0.8.2, es importante saber que el RP de AKS instala el webhook de validación y el reconciliador de AKS garantiza que existe, pero el controlador de OSM es el que rellena el paquete de CA.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Este número indica el número de bytes o el tamaño del paquete de CA. Si está vacío o es 0 o algún número por debajo de 1000, el paquete de CA no está correctamente aprovisionado. Sin un paquete de CA correcto, se producirá un error en el webhook de validación y se prohibirá que el usuario realice cambios en el elemento ConfigMap osm-config del espacio de nombres kube-system.

Un ejemplo de error en el que el paquete de CA es incorrecto:

- Intento de cambiar el ConfigMap osm-config:

  ```azurecli-interactive
  kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```

- Error:

  ```
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

Solución alternativa para cuando la configuración del webhook de **validación** tenga un certificado incorrecto:

- Opción 1 - Reiniciar el controlador de OSM: se reiniciará el controlador de OSM. En el inicio, sobrescribirá el paquete de CA de los webhooks de mutación y validación.

  ```azurecli-interactive
  kubectl rollout restart deployment -n kube-system osm-controller
  ```

- Opción 2 - Eliminar el webhook de validación: al quitar el webhook de validación, las mutaciones del ConfigMap `osm-config` ya no se validan. Cualquier revisión se realizará. En algún momento, el reconciliador de AKS garantiza que el webhook de validación existe y volverá a crearlo. Es posible que sea necesario reiniciar el controlador de OSM para volver a escribir rápidamente el paquete de CA.

  ```azurecli-interactive
  kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
  ```

- Opción 3 - Eliminación y revisión: el siguiente comando eliminará el webhook de validación, lo que nos permitirá agregar valores, e intentará aplicar una revisión de inmediato. Lo más probable es que el reconciliador de AKS no tenga tiempo suficiente para reconciliar y restaurar el webhook de validación, lo que nos permite aplicar un cambio como último recurso:

  ```azurecli-interactive
  kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```

### <a name="check-the-osm-mesh-config-resource"></a>Comprobación del recurso `osm-mesh-config`

Comprobación de la existencia:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

Comprobación del contenido de MeshConfig de OSM

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

Valores del recurso `osm-mesh-config`:

| Clave | Tipo | Valor predeterminado | Ejemplos de comandos de revisión de Kubectl |
|-----|------|---------------|--------------------------------|
| spec.traffic.enableEgress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundPortExclusionList":6379,8080}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":"10.0.0.0/32,1.1.1.1/24"}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | string | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"serviceCertValidityDuration":true}}}'  --type=merge` |
| spec.observability.tracing.enable | bool | `"jaeger.osm-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.osm-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.address | string | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.endpoint | string | `false`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.port | int | `9411`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | string | `"error"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | int | `0` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | string | `"envoyproxy/envoy-alpine:v1.17.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"envoyImage":"envoyproxy/envoy-alpine:v1.17.2"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | string | `"openservicemesh/init:v0.9.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"initContainerImage":"openservicemesh/init:v0.9.2"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | string | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |

### <a name="check-namespaces"></a>Comprobación de espacios de nombres

> [!NOTE]
> El espacio de nombres espacio de nombres kube-system nunca participará en una malla de servicio y nunca se etiquetará ni anotará con la clave o los valores siguientes.

Usamos el comando `osm namespace add` para unir espacios de nombres a una malla de servicio determinada.
Cuando un espacio de nombres de K8S forma parte de la malla (o para que forme parte de la malla) debe cumplirse lo siguiente:

Vea las anotaciones con

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Las siguientes anotaciones deben estar presentes:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Vea las etiquetas con

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

La siguiente etiqueta debe estar presente:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Si un espacio de nombres no está anotado con `"openservicemesh.io/sidecar-injection": "enabled"` o no está etiquetado con `"openservicemesh.io/monitored-by": "osm"`, el inyector de agregará sidecars de Envoy.

> [!NOTE]
> Después de que se llame a `osm namespace add`, solo se insertarán los pods **nuevos** con un sidecar de Envoy. Los pods existentes deben reiniciarse con `kubectl rollout restart deployment ...`.

### <a name="verify-the-smi-crds"></a>Compruebe las CRD de SMI:

Compruebe si el clúster tiene las CRD necesarias:

```azurecli-interactive
kubectl get crds
```

Debemos tener lo siguiente instalado en el clúster:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Obtenga las versiones de las CRD instaladas con este comando:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Resultado esperado:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

El controlador de OSM v0.8.2 requiere las siguientes versiones:

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io - No compatible
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Si faltan CRD, use los siguientes comandos para instalarlas en el clúster:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```