---
title: Uso de la entrada de Azure Application Gateway
description: Uso de la entrada de Azure Application Gateway con Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: ac0d8adff819e51be8c5649130447590741bc082
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440106"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Implementación de una aplicación administrada por Open Service Mesh (OSM) mediante el complemento de AKS de entrada de Azure Application Gateway

En este tutorial va a:

> [!div class="checklist"]
>
> - Ver la configuración actual del clúster de OSM
> - Crear los espacios de nombres de OSM para administrar las aplicaciones implementadas en los espacios de nombres
> - Incorporar los espacios de nombres que se van a administrar mediante OSM
> - Implementación de la aplicación de ejemplo
> - Comprobar que la aplicación se ejecuta en el clúster de AKS
> - Crear una instancia de Azure Application Gateway para usarla como controlador de entrada de la aplicación
> - Exponer un servicio a través de la entrada de Azure Application Gateway a Internet

## <a name="before-you-begin"></a>Antes de empezar

En los pasos que se detallan en este tutorial se supone que ya ha habilitado el complemento OSM de AKS para el clúster de AKS. Si no es así, revise el artículo [Implementación del complemento OSM de AKS](./open-service-mesh-deploy-add-on.md) antes de continuar. Además, el clúster de AKS debe ser de la versión Kubernetes `1.19+` o posterior, tener habilitado RBAC de Kubernetes y haber establecido una conexión `kubectl` con el clúster (si necesita ayuda con cualquiera de estos elementos, consulte el [inicio rápido de AKS](./kubernetes-walkthrough.md)), además de haber instalado el complemento OSM de AKS.

Debe tener instalados los siguientes recursos:

- CLI de Azure, versión 2.20.0 o posterior
- Extensión `aks-preview`, versión 0.5.5 o posterior
- Versión v.0.8.0 o posterior de OSM
- Procesador JSON "jq" versión 1.6+

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visualización y comprobación de la configuración actual del clúster de OSM

Una vez que el complemento OSM para AKS se ha habilitado en el clúster de AKS, puede ver los parámetros de configuración actuales del recurso osm-mesh-config. Ejecute el siguiente comando para ver las propiedades:

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
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

Observe que **enablePermissiveTrafficPolicyMode** está configurado en **true**. El modo de la directiva de tráfico permisivo en OSM es un modo en el que se omite la aplicación de directivas de tráfico de [SMI](https://smi-spec.io/). En este modo, OSM detecta automáticamente los servicios que forman parte de la malla de servicio y programa reglas de la directiva de tráfico en cada sidecar del proxy de Envoy para comunicarse con estos servicios.

## <a name="create-namespaces-for-the-application"></a>Creación de espacios de nombres para la aplicación

En este tutorial, vamos a usar la aplicación bookstore de OSM que tiene los siguientes componentes:

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

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

La adición de los espacios de nombres a la malla de OSM permitirá que el controlador de OSM inserte automáticamente los contenedores del proxy de sidecar de Envoy en la aplicación. Ejecute el siguiente comando para incorporar los espacios de nombres de la aplicación de librería de OSM.

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

## <a name="deploy-the-bookstore-application"></a>Implementación de la aplicación Bookstore

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

## <a name="update-the-bookbuyer-service"></a>Actualización del servicio bookbuyer

Actualice el servicio bookbuyer a la configuración de puerto de entrada correcta con el siguiente manifiesto de servicio.

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

## <a name="verify-the-bookstore-application"></a>Verificación de la aplicación Bookstore

Ahora, hemos implementado la aplicación bookstore de varios contenedores, pero solo se puede acceder a ella desde el clúster de AKS. Más adelante se agregará el controlador de entrada de Azure Application Gateway para exponer la aplicación fuera del clúster de AKS. Para comprobar que la aplicación se ejecuta dentro del clúster, utilizaremos un reenvío de puertos para ver la interfaz de usuario del componente bookbuyer.

En primer lugar, vamos a obtener el nombre del pod de bookbuyer.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Debería ver una salida similar a la siguiente. El pod de bookbuyer tendrá un nombre único anexado.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Una vez que tenemos el nombre del pod, podemos usar el comando de reenvío de puerto para configurar un túnel desde nuestro sistema local a la aplicación dentro del clúster de AKS. Ejecute el siguiente comando para configurar el reenvío de puertos para el puerto del sistema local 8080. Vuelva a usar el nombre del pod de bookbuyer específico.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Debería mostrarse una salida similar a esta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Mientras la sesión de reenvío de puertos está en marcha, navegue a la siguiente URL desde un navegador `http://localhost:8080`. Ahora debería poder ver la interfaz de usuario de la aplicación bookbuyer en el explorador, similar a la imagen siguiente.

![Imagen de interfaz de usuario de la aplicación bookbuyer de OSM para App Gateway](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application"></a>Creación de una instancia de Azure Application Gateway para exponer la aplicación bookbuyer 

> [!NOTE]
> Las instrucciones siguientes crearán una nueva instancia de Azure Application Gateway que se usará para la entrada. Si ya tiene una instancia de Azure Application Gateway que desea usar, vaya a la sección para habilitar el complemento del controlador de entrada de Application Gateway.

### <a name="deploy-a-new-application-gateway"></a>Implementación de una nueva instancia de Application Gateway

> [!NOTE]
> Nos estamos refiriendo a la documentación existente para habilitar el complemento de Controlador de entrada de Application Gateway para un clúster de AKS existente. Se han realizado algunas modificaciones para adaptarse a los materiales de OSM. [Aquí](../application-gateway/tutorial-ingress-controller-add-on-existing.md)encontrará documentación más detallada sobre el tema.

Ahora implementará una nueva instancia de Application Gateway para simular una instancia de Application Gateway existente que quiera usar para equilibrar la carga del tráfico en el clúster de AKS, _myCluster_. El nombre de la instancia de Application Gateway será _myApplicationGateway_, pero tendrá que crear primero un recurso de dirección IP pública, denominado _myPublicIp_, y una nueva red virtual denominada _myVnet_ con el espacio de direcciones 11.0.0.0/8 y una subred con el espacio de direcciones 11.1.0.0/16 llamada _mySubnet_ e implementar la instancia de Application Gateway en _mySubnet_ con _myPublicIp_.

Cuando se usa un clúster de AKS y una instancia de Application Gateway en redes virtuales independientes, los espacios de direcciones de las dos redes virtuales no deben superponerse. El espacio de direcciones predeterminado que implementa un clúster de AKS es 10.0.0.0/8, por lo que establecemos el prefijo de dirección de red virtual de la instancia de Application Gateway en 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> El complemento del controlador de entrada de Application Gateway (AGIC) admite **solo** las SKU de Application Gateway v2 (estándar y WAF) y **no** las SKU de Application Gateway v1.

### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Habilitación del complemento AGIC para un clúster de AKS existente a través de la CLI de Azure

Si quiere seguir usando la CLI de Azure, puede seguir habilitando el complemento AGIC en el clúster de AKS que creó, _myCluster_, y especificar el complemento AGIC para usar la instancia de Application Gateway existente que creó, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Puede comprobar que el complemento de AKS de Azure Application Gateway se ha habilitado mediante el siguiente comando.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Este comando debería mostrar la salida como `true`.

### <a name="peer-the-two-virtual-networks-together"></a>Emparejamiento de dos redes virtuales juntas

Dado que hemos implementado el clúster de AKS en su propia red virtual y la instancia de Application Gateway en otra red virtual, deberá emparejar las dos redes virtuales juntas para que el tráfico fluya de la instancia de Application Gateway a los pods del clúster. Emparejar las dos redes virtuales requiere ejecutar el comando de la CLI de Azure dos veces independientes para asegurarse de que la conexión sea bidireccional. El primer comando creará una conexión de emparejamiento desde la red virtual de Application Gateway a la red virtual de AKS; el segundo comando creará una conexión de emparejamiento en la otra dirección.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exposición del servicio bookbuyer a Internet

Aplique el siguiente manifiesto de entrada al clúster de AKS para exponer el servicio bookbuyer a Internet a través de Azure Application Gateway.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

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

Debería ver la siguiente salida.

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Dado que el nombre de host en el manifiesto de entrada es un pseudonombre que se usa para las pruebas, el nombre DNS no estará disponible en Internet. También podemos usar el programa de curl y después el encabezado de nombre de host para la dirección IP pública de Azure Application Gateway y recibir un código 200 que nos conecta correctamente con el servicio bookbuyer.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Debería ver la siguiente salida.

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
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

## <a name="troubleshooting"></a>Solución de problemas

- [Documentación de solución de problemas de AGIC](../application-gateway/ingress-controller-troubleshoot.md)
- [Hay otras herramientas de solución de problemas disponibles en el repositorio de GitHub de AGIC](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md).