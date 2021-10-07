---
title: Administración de una aplicación existente con Open Service Mesh
description: Procedimiento para administrar una aplicación existente con Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 092e42a5f9c1779fc5968b9fc733d260d405a90a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625912"
---
# <a name="manage-an-existing-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Administración de una aplicación existente con el complemento Open Service Mesh (OSM) de Azure Kubernetes Service (AKS)

## <a name="before-you-begin"></a>Antes de empezar

En los pasos que se detallan en este tutorial se supone que ya ha habilitado el complemento OSM de AKS para el clúster de AKS. Si no es así, revise el artículo [Implementación del complemento OSM de AKS](./open-service-mesh-deploy-addon-az-cli.md) antes de continuar. Además, el clúster de AKS debe ser de la versión Kubernetes `1.19+` o posterior, tener habilitado RBAC de Kubernetes y haber establecido una conexión `kubectl` con el clúster (si necesita ayuda con cualquiera de estos elementos, consulte el [inicio rápido de AKS](./kubernetes-walkthrough.md)), además de haber instalado el complemento OSM de AKS.

Debe tener instalados los siguientes recursos:

- CLI de Azure, versión 2.20.0 o posterior
- Extensión `aks-preview`, versión 0.5.5 o posterior
- Versión v.0.8.0 o posterior de OSM
- Procesador JSON "jq" versión 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Comprobación de la directiva de modo de tráfico permisivo de Open Service Mesh (OSM)

El modo de la directiva de tráfico permisivo de OSM es un modo en el que se omite la aplicación de directivas de tráfico de [SMI](https://smi-spec.io/). En este modo, OSM detecta automáticamente los servicios que forman parte de la malla de servicio y programa reglas de la directiva de tráfico en cada sidecar del proxy de Envoy para comunicarse con estos servicios.

Para comprobar el modo de tráfico permisivo actual de OSM para el clúster, ejecute el siguiente comando:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

La salida de MeshConfig de OSM debe tener este aspecto:

```Output
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

Si **enablePermissiveTrafficPolicyMode** está configurado en **true**, puede incorporar los espacios de nombres de forma segura sin que se produzca ninguna interrupción en las comunicaciones entre servicios. Si **enablePermissiveTrafficPolicyMode** está configurado en **false**, deberá asegurarse de que tiene implementados los manifiestos de directiva de acceso de tráfico [SMI](https://smi-spec.io/) correctos. También deberá asegurarse de que tiene una cuenta de servicio que representa cada servicio implementado en el espacio de nombres. Para obtener más información sobre el modo de tráfico permisivo, visite y lea el artículo [Modo de directiva de tráfico permisivo](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Incorporación de aplicaciones implementadas existentes con la directiva de tráfico permisivo de Open Service Mesh (OSM) configurada como True

Lo primero que haremos es agregar los espacios de nombres de la aplicación implementada a OSM para administrarlos. En el ejemplo siguiente se incorporará el espacio de nombres **bookstore** a OSM.

```azurecli-interactive
osm namespace add bookstore
```

Debería ver la siguiente salida:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

A continuación, echaremos un vistazo a la implementación del pod actual en el espacio de nombres. Ejecute el siguiente comando para ver los pods en el espacio de nombres `bookbuyer`.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Debería ver una salida similar a la siguiente:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Observe que la columna **READY** muestra **1/1**, lo que significa que la aplicación del pod solo tiene un contenedor. A continuación, será necesario reiniciar las implementaciones de la aplicación para que OSM pueda insertar el contenedor del proxy sidecar de Envoy con el pod de la aplicación. Vamos a obtener una lista de las implementaciones del espacio de nombres.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Debería ver la siguiente salida:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Ahora se reiniciará la implementación para insertar el contenedor sidecar del proxy de Envoy con el pod de la aplicación. Ejecute el siguiente comando:

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Debería ver la siguiente salida:

```Output
deployment.apps/bookbuyer restarted
```

Si echamos un vistazo a los pods en el espacio de nombres de nuevo:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Ahora observará que la columna **READY** muestra **2/2** contenedores listos para su pod. El segundo contenedor es el proxy de sidecar de Envoy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Podemos inspeccionar más el pod para ver el proxy de Envoy mediante la ejecución del comando de descripción para ver la configuración.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Compruebe que la aplicación sigue siendo funcional después de la inserción del proxy de sidecar de Envoy.

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Incorporación de aplicaciones implementadas existentes con la directiva de tráfico permisivo de Open Service Mesh (OSM) configurada como False

Cuando la configuración de OSM para la directiva de tráfico permisivo está establecida en `false`, OSM requerirá directivas de acceso de tráfico de [SMI](https://smi-spec.io/) explícitas implementadas para que la comunicación entre servicios se produzca en el clúster. Actualmente, OSM también usa cuentas de servicio de Kubernetes como parte de la autorización de comunicaciones entre servicios. Para asegurarse de que las aplicaciones implementadas existentes se comunicarán cuando se administren mediante la malla de OSM, será necesario comprobar la existencia de una cuenta de servicio para usarla, actualizar la implementación de la aplicación con la información de la cuenta de servicio y aplicar las directivas de acceso de tráfico de [SMI](https://smi-spec.io/).

### <a name="verify-kubernetes-service-accounts"></a>Comprobación de cuentas de servicio de Kubernetes

Compruebe si tiene una cuenta de servicio de Kubernetes en el espacio de nombres en el que está implementada la aplicación.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

En el siguiente, hay una cuenta de servicio denominada `bookbuyer` en el espacio de nombres `bookbuyer`.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Si no tiene una cuenta de servicio que no sea la cuenta predeterminada, deberá crear una para la aplicación. Use el comando siguiente como ejemplo para crear una cuenta de servicio en el espacio de nombres implementado de la aplicación.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

### <a name="view-your-applications-current-deployment-specification"></a>Ver la especificación de la implementación actual de la aplicación

Si tuviera que crear una cuenta de servicio desde la sección anterior, lo más probable es que la implementación de la aplicación no esté configurada con un valor `serviceAccountName` específico en las especificaciones de implementación. Podemos ver la especificación de la implementación de la aplicación con los siguientes comandos:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

En la salida se mostrará una lista de las implementaciones.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Describiremos la implementación como una comprobación para ver si hay una cuenta de servicio enumerada en la sección de plantilla de pod.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

En esta implementación en particular, puede ver que hay una cuenta de servicio asociada a la implementación enumerada en la sección de plantilla de pod. Esta implementación usa la cuenta de servicio `bookbuyer`. Si no ve la propiedad **Service Account:** , la implementación no está configurada para usar una cuenta de servicio.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Hay varias técnicas para actualizar la implementación con el fin de agregar una cuenta de servicio de kubernetes. Revise la documentación de Kubernetes sobre cómo [actualizar una implementación](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) insertada o [configurar cuentas de servicio para pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Una vez que haya actualizado las especificaciones de implementación con la cuenta de servicio, vuelva a implementar (kubectl apply -f your-deployment.yaml) su implementación en el clúster.

### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Implementación de las directivas de Service Mesh Interface (SMI) necesarias

El último paso para permitir que el tráfico autorizado fluya en la malla es implementar las directivas de acceso de tráfico de [SMI](https://smi-spec.io/) necesarias para la aplicación. La cantidad de configuración que se puede lograr con las directivas de acceso de tráfico de [SMI](https://smi-spec.io/) queda fuera del ámbito de este tutorial, pero se detallan algunos de los componentes comunes de la especificación y se muestra cómo configurar una directiva sencilla de TrafficTarget y HTTPRouteGroup para habilitar la comunicación entre servicios para la aplicación.

La especificación [SMI](https://smi-spec.io/) [**Traffic Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) permite a los usuarios definir la directiva de control de acceso para sus aplicaciones. Nos centraremos en los recursos de API de **TrafficTarget** y **HTTPRoutGroup**.

El recurso TrafficTarget consta de tres valores de configuración principales: destino, reglas y orígenes. A continuación, se muestra un ejemplo de TrafficTarget.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

En la especificación de TrafficTarget anterior, `destination` denota la cuenta de servicio que está configurada para el servicio de origen de destino. Recuerde que la cuenta de servicio que se agregó a la implementación anterior se usará para autorizar el acceso a la implementación a la que está asociada. La sección `rules`, en este ejemplo concreto, define el tipo de tráfico HTTP que se permite a través de la conexión. Puede configurar patrones regex precisos para que los encabezados HTTP sean específicos respecto al tráfico que se permite a través de HTTP. La sección `sources` es el servicio que origina las comunicaciones. Esta especificación lee que el elemento `bookbuyer` necesita comunicarse con bookstore.

El recurso HTTPRouteGroup se compone de una coincidencia o una matriz de ellas de la información del encabezado HTTP y es un requisito de la especificación TrafficTarget. En el ejemplo siguiente, puede ver que HTTPRouteGroup está autorizando tres acciones HTTP, dos GET y una POST.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Si no está familiarizado con el tipo de tráfico HTTP que la aplicación de front-end realiza a otros niveles de la aplicación, ya que la especificación de TrafficTarget requiere una regla, puede crear el equivalente de una regla de permitir todo con la siguiente especificación para HTTPRouteGroup.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Una vez configurada la especificación de TrafficTarget y HTTPRouteGroup, puede combinarla como un YAML e implementarlo. A continuación se muestra la configuración del ejemplo de bookstore.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Visite el sitio de [SMI](https://smi-spec.io/) para obtener información más detallada sobre la especificación.

## <a name="manage-the-applications-namespace-with-osm"></a>Administración del espacio de nombres de la aplicación con OSM

A continuación, configuraremos OSM para administrar el espacio de nombres y reiniciaremos las implementaciones para obtener el proxy de sidecar de Envoy insertado con la aplicación.

Ejecute el siguiente comando para configurar el espacio de nombres `azure-vote` que se va a administrar en mi OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

A continuación, reinicie las implementaciones `azure-vote-front` y `azure-vote-back` con los siguientes comandos.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Si vemos los pods del espacio de nombres `azure-vote`, veremos la fase **READY** de `azure-vote-front` y `azure-vote-back` como 2/2, lo que significa que se ha insertado el proxy de sidecar de Envoy junto a la aplicación.
