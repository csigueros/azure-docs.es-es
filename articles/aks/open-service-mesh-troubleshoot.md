---
title: Solución de problemas de Open Service Mesh
description: Procedimiento para solucionar problemas de Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 27a553bee765dd1369490cd9f6825cc44c48c59a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065672"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Guías de solución de problemas del complemento Open Service Mesh (OSM) de AKS

Al implementar el complemento OSM de AKS, es posible que experimente problemas asociados con la configuración de la malla de servicio. La guía siguiente le ayudará a solucionar problemas y resolver errores comunes.

## <a name="verifying-and-troubleshooting-osm-components"></a>Comprobación y solución de problemas de componentes de OSM

### <a name="check-osm-controller-deployment-pod-and-service"></a>Comprobación de la implementación, el pod y el servicio del controlador de OSM

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-controller
```

Un controlador de OSM correcto tendría el siguiente aspecto:

```Output
NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-controller   2/2     2            2           3m4s

NAME                                  READY   STATUS    RESTARTS   AGE
pod/osm-controller-65bd8c445c-zszp4   1/1     Running   0          2m
pod/osm-controller-65bd8c445c-xqhmk   1/1     Running   0          16s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                       AGE
service/osm-controller   ClusterIP   10.96.185.178   <none>        15128/TCP,9092/TCP,9091/TCP   3m4s
service/osm-validator    ClusterIP   10.96.11.78     <none>        9093/TCP                      3m4s
```

> [!NOTE]
> Para los servicios osm-controller, CLUSTER-IP sería diferente. Los valores NAME y PORT(S) del servicio deben ser iguales al ejemplo anterior.

### <a name="check-osm-injector-deployment-pod-and-service"></a>Comprobación de la implementación, el pod y el servicio del inyector de OSM

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-injector
```

Un inyector de OSM correcto tendría este aspecto:

```Output
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-injector   2/2     2            2           4m37s

NAME                                READY   STATUS    RESTARTS   AGE
pod/osm-injector-5c49bd8d7c-b6cx6   1/1     Running   0          4m21s
pod/osm-injector-5c49bd8d7c-dx587   1/1     Running   0          4m37s

NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/osm-injector   ClusterIP   10.96.236.108   <none>        9090/TCP   4m37s
```

### <a name="check-osm-bootstrap-deployment-pod-and-service"></a>Comprobación de la implementación, el pod y el servicio de arranque de OSM

```azurecli-interactive
kubectl get deployment,pod,service -n kube-system --selector app=osm-bootstrap
```

Un arranque de OSM correcto tendría el siguiente aspecto:

```Output
NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/osm-bootstrap   1/1     1            1           5m25s

NAME                                 READY   STATUS    RESTARTS   AGE
pod/osm-bootstrap-594ffc6cb7-jc7bs   1/1     Running   0          5m25s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
service/osm-bootstrap   ClusterIP   10.96.250.208   <none>        9443/TCP,9095/TCP   5m25s
```

### <a name="check-validating-and-mutating-webhooks"></a>Comprobación de los webhooks de validación y mutación

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Un webhook de validación de OSM correcto tendría el siguiente aspecto:

```Output
NAME              WEBHOOKS   AGE
aks-osm-validator-mesh-osm   1      81m
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

### <a name="check-the-osm-mesh-config-resource"></a>Comprobación del recurso `osm-mesh-config`

Comprobación de la existencia:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

Comprobación del contenido de MeshConfig de OSM

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
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
      address: jaeger.kube-system.svc.cluster.local
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
| spec.traffic.enableEgress | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"outboundPortExclusionList":[6379,8080]}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":["10.0.0.0/32","1.1.1.1/24"]}}}'  --type=merge` |
| spec.traffic.inboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"inboundPortExclusionList":[6379,8080]}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | string | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | bool | `true` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"enableDebugServer":true}}}'  --type=merge` |
| spec.observability.tracing.enable | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.address | string | `"jaeger.kube-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.kube-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.endpoint | string | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.port | int | `9411`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.observability.tracing.osmLogLevel | string | `"info"`| `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"osmLogLevel": "info"}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | bool | `false` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | string | `"error"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | int | `0` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | string | `"mcr.microsoft.com/oss/envoyproxy/envoy:v1.19.1"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"envoyImage":"mcr.microsoft.com/oss/envoyproxy/envoy:v1.19.1"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | string | `"mcr.microsoft.com/oss/openservicemesh/init:v0.11.1"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"initContainerImage":"mcr.microsoft.com/oss/openservicemesh/init:v0.11.1"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | string | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |
| spec.featureFlags.enableWASMStats | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableWASMStats":"true"}}}'  --type=merge` |
| spec.featureFlags.enableEgressPolicy | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableEgressPolicy":"true"}}}'  --type=merge` |
| spec.featureFlags.enableMulticlusterMode | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableMulticlusterMode":"false"}}}'  --type=merge` |
| spec.featureFlags.enableSnapshotCacheMode | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableSnapshotCacheMode":"false"}}}'  --type=merge` |
| spec.featureFlags.enableAsyncProxyServiceMapping | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableAsyncProxyServiceMapping":"false"}}}'  --type=merge` |
| spec.featureFlags.enableIngressBackendPolicy | bool | `"true"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableIngressBackendPolicy":"true"}}}'  --type=merge` |
| spec.featureFlags.enableEnvoyActiveHealthChecks | bool | `"false"` | `kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"featureFlags":{"enableEnvoyActiveHealthChecks":"false"}}}'  --type=merge` |


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

### <a name="verify-osm-crds"></a>Compruebe los CRD de OSM:

Compruebe si el clúster tiene las CRD necesarias:

```azurecli-interactive
kubectl get crds
```

Debemos tener lo siguiente instalado en el clúster:

- egresses.policy.openservicemesh.io
- httproutegroups.specs.smi-spec.io 
- ingressbackends.policy.openservicemesh.io
- meshconfigs.config.openservicemesh.io
- multiclusterservices.config.openservicemesh.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io

Obtenga las versiones de los CRD de SMI instalados con este comando:

```azurecli-interactive
osm mesh list
```

Resultado esperado:

```
MESH NAME   MESH NAMESPACE   VERSION   ADDED NAMESPACES
osm         kube-system      v0.11.1

MESH NAME   MESH NAMESPACE   SMI SUPPORTED
osm         kube-system      HTTPRouteGroup:v1alpha4,TCPRoute:v1alpha4,TrafficSplit:v1alpha2,TrafficTarget:v1alpha3

To list the OSM controller pods for a mesh, please run the following command passing in the mesh's namespace
        kubectl get pods -n <osm-mesh-namespace> -l app=osm-controller
```

El controlador de OSM 0.11.1 requiere las siguientes versiones:

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io - No compatible
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)


### <a name="certificate-management"></a>Administración de certificados

Puede encontrar información sobre cómo OSM emite y administra certificados en servidores proxy de Envoy que se ejecutan en pods de aplicación en el [sitio de documentación OpenServiceMesh](https://docs.openservicemesh.io/docs/guides/certificates/).

### <a name="upgrading-envoy"></a>Actualización de Envoy

Cuando se crea un nuevo pod en un espacio de nombres supervisado por el complemento, OSM insertará un [sidecar de proxy de Envoy](https://docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/) en ese pod. Puede encontrar información sobre cómo actualizar la versión de Envoy en la [Guía de actualización](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/upgrade/#envoy) del sitio de documentos de OpenServiceMesh.
