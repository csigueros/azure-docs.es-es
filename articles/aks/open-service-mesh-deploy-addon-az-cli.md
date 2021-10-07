---
title: Implementación de Open Service Mesh
description: Implementación de Open Service Mesh en Azure Kubernetes Service (AKS) mediante la CLI de Azure
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: f5680d21abe1f4dffb163b6e0b16fd836e6a3fc2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699797"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-using-azure-cli"></a>Implementación del complemento Open Service Mesh de AKS mediante la CLI de Azure

En este artículo se explica cómo implementar el complemento OSM en AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Requisitos previos

- CLI de Azure, versión 2.20.0 o posterior
- Extensión `aks-preview`, versión 0.5.5 o posterior
- Versión v 0.9.1 o posterior de OSM

## <a name="install-the-aks-preview-extension"></a>Instalación de la extensión aks-preview

Necesitará la versión 0.5.24 o posterior de la extensión de la CLI de Azure _aks-preview_. Instale la extensión de la CLI de Azure _aks-preview_ mediante el comando [az extension add][az-extension-add]. También puede instalar las actualizaciones disponibles mediante el comando [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="register-the-aks-openservicemesh-preview-feature"></a>Registro de la característica en vista previa (GB) `AKS-OpenServiceMesh`

Para crear un clúster de AKS que use el complemento Open Service Mesh, debe habilitar la marca de características `AKS-OpenServiceMesh` en su suscripción.

Registre la marca de la característica `AKS-OpenServiceMesh` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Tarda unos minutos en que el estado muestre _Registrado_. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos _Microsoft.ContainerService_ con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Instalación del complemento Open Service Mesh (OSM) de Azure Kubernetes Service (AKS) para un nuevo clúster de AKS

Para un nuevo escenario de implementación de clústeres de AKS, comenzará con una nueva implementación de un clúster de AKS habilitando el complemento OSM en la operación de creación del clúster.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

En Azure, puede asignar recursos relacionados a un grupo de recursos. Para crear un grupo de recursos, use [az group create](/cli/azure/group#az_group_create). El ejemplo siguiente se usa para crear un grupo de recursos en una ubicación (región) especificada:

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Implementación de un clúster de AKS con el complemento OSM habilitado

Ahora implementará un nuevo clúster de AKS con el complemento OSM habilitado.

> [!NOTE]
> Tenga en cuenta que el siguiente comando de implementación de AKS utiliza discos de sistema operativo efímeros. Puede encontrar más información aquí sobre los [discos de sistema operativo efímeros para AKS](./cluster-configuration.md#ephemeral-os)

```azurecli-interactive
az aks create -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg> --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Obtención de credenciales de acceso de clúster de AKS

Obtenga las credenciales de acceso para el nuevo clúster de Kubernetes administrado.

```azurecli-interactive
az aks get-credentials -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Habilitación del complemento Open Service Mesh (OSM) de Azure Kubernetes Service (AKS) para un clúster de AKS existente

En un escenario de clúster de AKS existente, habilitará el complemento OSM en un clúster de AKS existente que ya se ha implementado.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Habilitación del complemento OSM en el clúster de AKS existente

Para habilitar el complemento OSM de AKS, deberá ejecutar el comando `az aks enable-addons --addons` pasando el parámetro `open-service-mesh`.

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <my-osm-aks-cluster-rg> -n <my-osm-aks-cluster-name>
```

Debería ver una salida similar a la salida que se muestra a continuación para confirmar que se ha instalado el complemento OSM de AKS.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Validación de la instalación del complemento OSM de AKS

Hay varios comandos que se ejecutan para comprobar que todos los componentes del complemento OSM de AKS están habilitados y en ejecución:

En primer lugar, se pueden consultar los perfiles de complemento del clúster para comprobar el estado habilitado de los complementos instalados. El siguiente comando debe devolver "true".

```azurecli-interactive
az aks list -g <my-osm-aks-cluster-rg> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Los siguientes comandos `kubectl` informarán del estado del controlador de OSM.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on-configuration"></a>Acceso a la configuración del complemento OSM de AKS

Actualmente, puede acceder al controlador de OSM y establecer su configuración por medio del recurso MeshConfig de OSM. Para ver los valores de configuración del controlador de OSM desde la CLI, use el comando **kubectl** get como se muestra a continuación.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

La salida de MeshConfig debe tener este aspecto:

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

> [!WARNING]
> Antes de continuar, compruebe que el modo de la directiva de tráfico permisivo está establecida en true; si no es así, cámbiela a **true** mediante el comando siguiente.

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
