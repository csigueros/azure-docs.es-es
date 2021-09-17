---
title: Uso de la entrada de NGINX
description: Uso de la entrada de NGINX con Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: c06e6fe787070adbe0817e295380b2a2ef2775b5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440133"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Implementación de una aplicación administrada por Open Service Mesh (OSM) con entrada de NGINX

Open Service Mesh (OSM) es una malla de servicio nativa ligera, extensible y en la nube que permite a los usuarios administrar, proteger y obtener de manera uniforme características de observación de serie para entornos de microservicios muy dinámicos.

En este tutorial va a:

> [!div class="checklist"]
>
> - Ver la configuración actual del clúster de OSM
> - Crear los espacios de nombres de OSM para administrar las aplicaciones implementadas en los espacios de nombres
> - Incorporar los espacios de nombres que se van a administrar mediante OSM
> - Implementación de la aplicación de ejemplo
> - Comprobar que la aplicación se ejecuta en el clúster de AKS
> - Crear un controlador de entrada de NGINX usado para aplicación
> - Exponer un servicio a través de la entrada de Azure Application Gateway a Internet

## <a name="before-you-begin"></a>Antes de empezar

En los pasos que se detallan en este artículo se supone que ha creado un clúster de AKS (Kubernetes `1.19+` y versiones posteriores, con el RBAC de Kubernetes habilitado), ha establecido una conexión `kubectl` con el clúster (si necesita ayuda con cualquiera de estos elementos, consulte el [inicio rápido de AKS](./kubernetes-walkthrough.md)) y ha instalado el complemento OSM de AKS.

Debe tener instalados los siguientes recursos:

- CLI de Azure, versión 2.20.0 o posterior
- Extensión `aks-preview`, versión 0.5.5 o posterior
- Versión v.0.8.0 o posterior de OSM
- Procesador JSON "jq" versión 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visualización y comprobación de la configuración actual del clúster de OSM

Una vez que el complemento OSM para AKS se ha habilitado en el clúster de AKS, puede ver los parámetros de configuración actuales del recurso osm-mesh-config. Ejecute el siguiente comando para ver las propiedades:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

La salida muestra la configuración actual de OSM para el clúster.

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

Observe que **enablePermissiveTrafficPolicyMode** está configurado en **true**. El modo de la directiva de tráfico permisivo en OSM es un modo en el que se omite la aplicación de directivas de tráfico de [SMI](https://smi-spec.io/). En este modo, OSM detecta automáticamente los servicios que forman parte de la malla de servicio y programa reglas de la directiva de tráfico en cada sidecar del proxy de Envoy para comunicarse con estos servicios. Para obtener más información sobre el modo de tráfico permisivo, visite y lea el artículo [Modo de directiva de tráfico permisivo](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="create-namespaces-for-the-application"></a>Creación de espacios de nombres para la aplicación

En este tutorial se va a usar la aplicación `bookstore` de OSM, que tiene los siguientes componentes:

- `bookbuyer`
- `bookthief`
- `bookstore`
- `bookwarehouse`

Cree espacios de nombres para cada uno de estos componentes de la aplicación.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Debería ver la siguiente salida:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

## <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Incorporación de los espacios de nombres que se van a administrar mediante OSM

La adición de los espacios de nombres a la malla de OSM permitirá que el controlador de OSM inserte automáticamente los contenedores del proxy de sidecar de Envoy en la aplicación. Ejecute el siguiente comando para incorporar los espacios de nombres de la aplicación `bookstore` de OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Debería ver la siguiente salida:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

## <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Implementación de la aplicación Bookstore en el clúster de AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

A continuación se resumen todas las salidas de la implementación.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

## <a name="update-the-bookbuyer-service"></a>Actualización del servicio bookbuyer

Actualice el servicio `bookbuyer` a la configuración de puerto de entrada correcta con el siguiente manifiesto de servicio.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Comprobación de que la aplicación Bookstore se ejecuta en el clúster de AKS

Ya ha implementado la aplicación de varios contenedores `bookstore`, pero solo se puede acceder a ella desde el clúster de AKS. Más adelante se agregará el controlador de entrada de Azure Application Gateway para exponer la aplicación fuera del clúster de AKS. Para comprobar que la aplicación se ejecuta dentro del clúster, se va a usar un reenvío de puertos para ver la interfaz de usuario del componente `bookbuyer`.

En primer lugar, se obtiene el nombre del pod de `bookbuyer`.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Debería ver una salida similar a la siguiente. El pod de `bookbuyer` tiene un nombre único anexado.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Una vez que tenemos el nombre del pod, podemos usar el comando de reenvío de puerto para configurar un túnel desde nuestro sistema local a la aplicación dentro del clúster de AKS. Ejecute el siguiente comando para configurar el reenvío de puertos para el puerto del sistema local 8080. Vuelva a usar el nombre del pod de bookbuyer especificado.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Debería mostrarse una salida similar a la siguiente:

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Mientras la sesión de reenvío de puertos está en marcha, navegue a la siguiente URL desde un navegador `http://localhost:8080`. Ahora debería poder ver la interfaz de usuario de la aplicación `bookbuyer` en el explorador, que es similar a la imagen siguiente.

![Imagen de la IU de la aplicación bookbuyer para NGINX de OSM](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Creación de un controlador de entrada de NGINX en Azure Kubernetes Service (AKS)

Un controlador de entrada es un software que proporciona el proxy inverso, el enrutamiento del tráfico configurable y la terminación de TLS para los servicios de Kubernetes. Los recursos de entrada de Kubernetes se usan para configurar las reglas de entrada y las rutas de los distintos servicios de Kubernetes. Mediante reglas de entrada y un controlador de entrada, se puede usar una sola dirección IP para enrutar el tráfico a varios servicios en un clúster de Kubernetes.

Usaremos el controlador de entrada para exponer la aplicación administrada por OSM a Internet. Para crear el controlador de entrada, use Helm para instalar nginx-ingress. Para obtener redundancia adicional, se implementan dos réplicas de los controladores de entrada NGINX con el parámetro `--set controller.replicaCount`. Para sacar el máximo provecho de las réplicas en ejecución del controlador de entrada, asegúrese de que hay más de un nodo en el clúster de AKS.

El controlador de entrada se programa en un nodo de Linux. Los nodos de Windows Server no deben ejecutar el controlador de entrada. Un selector de nodos se especifica mediante el parámetro `--set nodeSelector` para indicar al programador de Kubernetes que ejecute el controlador de entrada NGINX en un nodo basado en Linux.

> [!TIP]
> En el siguiente ejemplo se crea un espacio de nombres de Kubernetes para los recursos de entrada denominado _ingress-basic_. Especifique un espacio de nombres para su propio entorno según sea necesario.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Se crea un servicio de equilibrador de carga de Kubernetes para el controlador de entrada de NGINX. Se asigna una dirección IP pública, como se muestra en esta salida de ejemplo:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

No se ha creado ninguna regla de entrada. Ahora mismo, si va a la dirección IP interna, se muestra la página 404 predeterminada del controlador de entrada de NGINX. Las reglas de entrada se configuran en los pasos siguientes.

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exposición del servicio bookbuyer a Internet

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Debería ver la siguiente salida:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

## <a name="view-the-nginx-logs"></a>Visualización de los registros de NGINX

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

La salida muestra el estado del controlador de entrada de NGINX cuando se ha aplicado correctamente la regla de entrada:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

## <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Visualización de los servicios de NGINX y el servicio bookbuyer externamente

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Dado que el nombre de host en el manifiesto de entrada es un pseudonombre que se usa para las pruebas, el nombre DNS no estará disponible en Internet. También podemos usar el programa de curl y después el encabezado de nombre de host para la dirección IP pública de NGINX y recibir un código 200 que nos conecta correctamente con el servicio bookbuyer.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Debería ver la siguiente salida:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```