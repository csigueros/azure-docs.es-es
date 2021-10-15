---
title: Administración de una nueva aplicación con Open Service Mesh
description: Procedimiento para administrar una aplicación nueva con Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 77728c7b61fc6b76cb5ecb51ff1d90e0c0a93e99
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535537"
---
# <a name="manage-a-new-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Administración de una aplicación nueva con el complemento Open Service Mesh (OSM) de Azure Kubernetes Service (AKS)

## <a name="before-you-begin"></a>Antes de empezar

En los pasos que se detallan en este tutorial se supone que ha creado un clúster de AKS (Kubernetes `1.19+` y versiones posteriores, con el RBAC de Kubernetes habilitado), ha establecido una conexión `kubectl` con el clúster (si necesita ayuda con cualquiera de estos elementos, consulte el [inicio rápido de AKS](./kubernetes-walkthrough.md)) y ha instalado el complemento OSM de AKS.

Debe tener instalados los siguientes recursos:

- CLI de Azure, versión 2.20.0 o posterior
- Extensión `aks-preview`, versión 0.5.5 o posterior
- Versión v.0.8.0 o posterior de OSM
- Procesador JSON "jq" versión 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="create-namespaces-for-the-application"></a>Creación de espacios de nombres para la aplicación

En este tutorial, vamos a usar la aplicación de librería de OSM que tiene los siguientes servicios de Kubernetes:

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

Al agregar los espacios de nombres a la malla de OSM, el controlador de OSM podrá insertar automáticamente los contenedores del proxy de sidecar de Envoy en la aplicación. Ejecute el siguiente comando para incorporar los espacios de nombres de la aplicación de librería de OSM.

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
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="checkpoint-what-got-installed"></a>Punto de control: ¿qué se ha instalado?

Bookstore es una aplicación de varios niveles de ejemplo que funciona bien para probar la funcionalidad de malla de servicio. La aplicación consta de cuatro servicios: `bookbuyer`, `bookthief`, `bookstore` y `bookwarehouse`. Los servicios `bookbuyer` y `bookthief` se comunican con el servicio `bookstore` para recuperar libros del servicio `bookstore`. El servicio `bookstore` recupera libros del servicio `bookwarehouse` para proporcionar `bookbuyer` y `bookthief`. Se trata de una aplicación sencilla de varios niveles que funciona bien para mostrar cómo se puede usar una malla de servicio para proteger y autorizar las comunicaciones entre los servicios de aplicaciones. A medida que sigamos con el tutorial, se habilitarán y deshabilitarán las directivas de Service Mesh Interface (SMI) para permitir y no permitir que los servicios se comuniquen a través de OSM. A continuación se muestra un diagrama de arquitectura de lo que se ha instalado para la aplicación bookstore.

![Arquitectura de la aplicación bookbuyer de OSM](./media/aks-osm-addon/osm-bookstore-app-arch.png)

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Comprobación de que la aplicación Bookstore se ejecuta en el clúster de AKS

Ya ha implementado la aplicación de varios contenedores `bookstore`, pero solo se puede acceder a ella desde el clúster de AKS. Los tutoriales posteriores le ayudarán a exponer la aplicación fuera del clúster a través de un controlador de entrada. Por ahora, se usará el reenvío de puertos para acceder a la aplicación `bookbuyer` dentro del clúster de AKS a fin de comprobar si compra libros del servicio `bookstore`.

Para comprobar que la aplicación se ejecuta dentro del clúster, se usará un reenvío de puertos para ver la interfaz de usuario de los componentes `bookbuyer` y `bookthief`.

En primer lugar, se obtendrá el nombre del pod de `bookbuyer`.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Debería ver una salida similar a la siguiente. El pod de `bookbuyer` tiene un nombre único anexado.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Una vez que tenemos el nombre del pod, podemos usar el comando de reenvío de puerto para configurar un túnel desde nuestro sistema local a la aplicación dentro del clúster de AKS. Ejecute el siguiente comando para configurar el reenvío de puertos para el puerto del sistema local 8080. Vuelva a usar el nombre del pod de bookbuyer especificado.

> [!NOTE]
> Para todos los comandos de reenvío de puertos es mejor utilizar un terminal adicional para que pueda seguir trabajando en este tutorial y no desconectar el túnel. También es mejor establecer el túnel de reenvío de puertos fuera de Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Debería ver un resultado similar al siguiente:

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Mientras la sesión de reenvío de puertos está en marcha, navegue a la siguiente URL desde un navegador `http://localhost:8080`. Ahora debería poder ver la interfaz de usuario de la aplicación `bookbuyer` en el explorador, similar a la imagen siguiente.

![Imagen de la interfaz de usuario de la aplicación bookbuyer de OSM](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

También observará que el número total de libros comprados continúa aumentando al servicio `bookstore` v1. Todavía no se ha implementado el servicio `bookstore` v2. El servicio `bookstore` v2 se implementará cuando se demuestren las directivas de división del tráfico de SMI.

También puede comprobar lo mismo para el servicio `bookthief`.

```azurecli-interactive
kubectl get pod -n bookthief
```

Debería ver una salida similar a la siguiente. El pod de `bookthief` tendrá un nombre único anexado.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Reenvíe el puerto al pod de `bookthief`.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Abra un explorador y vaya a la dirección URL siguiente `http://localhost:8080`. Debería ver que `bookthief` roba libros del servicio `bookstore`. Más adelante, se implementará una directiva de tráfico para detener a `bookthief`.

![Imagen de la interfaz de usuario de la aplicación bookthief de OSM](./media/aks-osm-addon/osm-bookthief-service-ui.png)

## <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Deshabilitación del modo de tráfico permisivo de OSM para la malla

Ahora deshabilitaremos la directiva del modo de tráfico permisivo y OSM necesitará directivas de [SMI](https://smi-spec.io/) explícitas implementadas en el clúster para permitir las comunicaciones en la malla de cada servicio. Para deshabilitar el modo de tráfico permisivo, ejecute el comando siguiente para actualizar la propiedad del recurso MeshConfig de OSM cambiando el valor de `true` a `false`.

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

Debería ver una salida similar a la siguiente.

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

Para comprobar que se ha deshabilitado el modo de tráfico permisivo, vuelva a enviar el puerto al pod de `bookbuyer` o `bookthief` para ver su interfaz de usuario en el explorador y comprobar si los libros comprados o los libros robados ya no se incrementan. Asegúrese de actualizar el explorador. Si se ha detenido el incremento, la directiva se aplicó correctamente. Ha conseguido impedir que `bookthief` robe libros, pero ni `bookbuyer` puede comprar en el servicio `bookstore` ni `bookstore` puede recuperar libros de `bookwarehouse`. A continuación, implementaremos directivas de [SMI](https://smi-spec.io/) para permitir que solo se comuniquen los servicios de la malla que desee. Para obtener más información sobre el modo de tráfico permisivo, visite y lea el artículo [Modo de directiva de tráfico permisivo](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Aplicación de directivas de acceso de tráfico de Service Mesh Interface (SMI)

Ahora que se han deshabilitado todas las comunicaciones de la malla, se permitirá que el servicio `bookbuyer` se comunique con el servicio `bookstore` para comprar libros, y que el servicio `bookstore` se comunique con el servicio `bookwarehouse` para recuperar los libros que se van a vender.

Implemente las siguientes directivas de [SMI](https://smi-spec.io/).

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
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
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Debería ver una salida similar a la siguiente.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Ahora puede configurar una sesión de reenvío de puertos en los pods de `bookbuyer` o `bookstore`, y ver que las métricas de libros comprados y libros vendidos vuelven a incrementarse. También puede hacer lo mismo con el pod de `bookthief` para comprobar que sigue sin poder robar libros.

## <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Aplicación de directivas de división de tráfico de Service Mesh Interface (SMI)

Para nuestra demostración final, crearemos una directiva de división de tráfico de [SMI](https://smi-spec.io/) para configurar la ponderación de las comunicaciones de un servicio a varios servicios como back-end. La función de división del tráfico permite desplazar progresivamente las conexiones de un servicio a otro ponderando el tráfico en una escala de 0 a 100.

El gráfico siguiente es un diagrama de la directiva de división del tráfico de [SMI](https://smi-spec.io/) que se va a implementar. Se implementará otra aplicación `Bookstore` como versión 2 y, después, se dividirá el tráfico entrante de `bookbuyer` de forma ponderada: el 25 % al servicio `bookstore` v1 y el 75 % al servicio `bookstore` v2.

![Diagrama de división del tráfico de bookbuyer de OSM](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Implemente el servicio `bookstore` v2.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
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
EOF
```

Debería ver la siguiente salida.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Ahora implemente la directiva de división del tráfico para dividir el tráfico de `bookbuyer` entre los dos servicios `bookstore` v1 y v2.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Debería ver la siguiente salida.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Configure un túnel de reenvío de puertos al pod de `bookbuyer`, y ahora debería ver los libros que se compran del servicio `bookstore` v2. Si sigue observando el incremento de las compras, debería notar un incremento más rápido de las que se realizan por medio del servicio `bookstore` v2.

![Interfaz de usuario de libros comprados de bookbuyer en OSM](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)