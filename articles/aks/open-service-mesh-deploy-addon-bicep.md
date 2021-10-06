---
title: Implementación del complemento Open Service Mesh de AKS mediante Bicep
description: Implementación de Open Service Mesh en Azure Kubernetes Service (AKS) mediante Bicep
services: container-service
ms.topic: article
ms.date: 9/20/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 2096176c64781e99161188ce66b7dd87d226278e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594566"
---
# <a name="deploy-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-using-bicep"></a>Implementación del complemento Open Service Mesh (OSM) en Azure Kubernetes Service (AKS) mediante Bicep

En este artículo se explica cómo implementar el complemento OSM en AKS mediante una plantilla de [Bicep](/azure/azure-resource-manager/bicep/).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

[Bicep](/azure/azure-resource-manager/bicep/overview) es un lenguaje específico de dominio (DSL) que usa sintaxis declarativa para implementar recursos de Azure. Bicep se puede usar en lugar de crear plantillas de [ARM](/azure/azure-resource-manager/templates/overview) de Azure para implementar los recursos de Azure de infraestructura como código.

## <a name="prerequisites"></a>Requisitos previos

- CLI de Azure, versión 2.20.0 o posterior
- Extensión `aks-preview`, versión 0.5.5 o posterior
- Versión v 0.9.1 o posterior de OSM
- Una clave pública SSH usada para implementar AKS
- [Visual Studio Code](https://code.visualstudio.com/) con un terminal de Bash
- La [extensión Bicep](/azure/azure-resource-manager/bicep/install) de Visual Studio Code

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

## <a name="install-the-osm-aks-add-on-for-a-new-aks-cluster-using-bicep"></a>Instalación del complemento OSM de AKS para un nuevo clúster de AKS mediante Bicep

Para un nuevo escenario de implementación de clústeres de AKS, comience con una nueva implementación de un clúster de AKS con el complemento OSM habilitado en la operación de creación del clúster. El siguiente conjunto de instrucciones usará una plantilla genérica de Bicep que implementa un clúster de AKS mediante discos efímeros, con el CNI [`kubenet`](/azure/aks/configure-kubenet) y el complemento OSM de AKS habilitado. Para escenarios de implementación más avanzados, visite la documentación de [Bicep](/azure/azure-resource-manager/bicep/overview).

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

En Azure, puede asociar recursos relacionados mediante un grupo de recursos. Para crear un grupo de recursos, use [az group create](/cli/azure/group#az_group_create). El ejemplo siguiente se usa para crear un grupo de recursos con nombre en una ubicación de Azure (región) especificada:

```azurecli-interactive
az group create --name <my-osm-bicep-aks-cluster-rg> --location <azure-region>
```

### <a name="create-the-main-and-parameters-bicep-files"></a>Creación de los archivos de Bicep principal y de parámetros

Con Visual Studio Code con un terminal de Bash abierto, cree un directorio para almacenar los archivos de implementación de Bicep necesarios. En el ejemplo siguiente se crea un directorio llamado `bicep-osm-aks-addon` y se cambia a él.

```azurecli-interactive
mkdir bicep-osm-aks-addon
cd bicep-osm-aks-addon
```

Después, cree los archivos principal y de parámetros, como se muestra en el ejemplo siguiente.

```azurecli-interactive
touch osm.aks.bicep && touch osm.aks.parameters.json
```

Abra el archivo `osm.aks.bicep`, copie dentro el siguiente contenido de ejemplo y, después, guarde el archivo.

```azurecli-interactive
// https://docs.microsoft.com/azure/aks/troubleshooting#what-naming-restrictions-are-enforced-for-aks-resources-and-parameters
@minLength(3)
@maxLength(63)
@description('Provide a name for the AKS cluster. The only allowed characters are letters, numbers, dashes, and underscore. The first and last character must be a letter or a number.')
param clusterName string
@minLength(3)
@maxLength(54)
@description('Provide a name for the AKS dnsPrefix. Valid characters include alphanumeric values and hyphens (-). The dnsPrefix can\'t include special characters such as a period (.)')
param clusterDNSPrefix string
param k8Version string
param sshPubKey string


resource aksCluster 'Microsoft.ContainerService/managedClusters@2021-03-01' = {
  name: clusterName
  location: resourceGroup().location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    kubernetesVersion: k8Version
    dnsPrefix: clusterDNSPrefix
    enableRBAC: true
    agentPoolProfiles: [
      {
        name: 'agentpool'
        count: 3
        vmSize: 'Standard_DS2_v2'
        osDiskSizeGB: 30
        osDiskType: 'Ephemeral'
        osType: 'Linux'
        mode: 'System'
      }
    ]
    linuxProfile: {
      adminUsername: 'adminUserName'
      ssh: {
        publicKeys: [
          {
            keyData: sshPubKey
          }
        ]
      }
    }
    addonProfiles: {
        openServiceMesh: {
            enabled: true
            config: {}
      }
    }
  }
}
```

Abra el archivo `osm.aks.parameters.json` y copie dentro el siguiente contenido de ejemplo. Agregue los parámetros específicos de la implementación y guarde el archivo.

> [!NOTE]
> `osm.aks.parameters.json` es un archivo de parámetros de plantilla de ejemplo necesario para la implementación de Bicep. Tendrá que actualizar los parámetros especificados específicamente para el entorno de implementación. Los valores de parámetro específicos utilizados en este ejemplo necesitan que se actualicen los parámetros siguientes. Son _clusterName_, _clusterDNSPrefix_, _k8Version_ y _sshPubKey_. Para buscar una lista de la versión compatible de Kubernetes en la región, use el comando `az aks get-versions --location <region>`.

```azurecli-interactive
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "<YOUR CLUSTER NAME HERE>"
    },
    "clusterDNSPrefix": {
      "value": "<YOUR CLUSTER DNS PREFIX HERE>"
    },
    "k8Version": {
      "value": "<YOUR SUPPORTED KUBERNETES VERSION HERE>"
    },
    "sshPubKey": {
      "value": "<YOUR SSH KEY HERE>"
    }
  }
}
```

### <a name="deploy-the-bicep-file"></a>Implementación del archivo de Bicep

Para implementar los archivos de Bicep creados anteriormente, abra el terminal y autentíquese en la cuenta de Azure para la CLI de Azure mediante el comando `az login`. Una vez que esté autenticado en la suscripción de Azure, ejecute los siguientes comandos para la implementación.

```azurecli-interactive
az group create --name osm-bicep-test --location eastus2

az deployment group create \
  --name OSMBicepDeployment \
  --resource-group osm-bicep-test \
  --template-file osm.aks.bicep \
  --parameters @osm.aks.parameters.json
```

Una vez finalizada la implementación, debería mostrarse un mensaje indicando que la implementación se realizó correctamente.

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

Actualmente puede acceder a la configuración del controlador de OSM y configurarla mediante el recurso MeshConfig de OSM, y puede ver las opciones de configuración del controlador de OSM mediante la CLI con el comando **kubectl** get como se muestra a continuación.

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

La salida de MeshConfig se muestra a continuación:

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

Observe que **enablePermissiveTrafficPolicyMode** está configurado en **true**. El modo de la directiva de tráfico permisivo en OSM es un modo en el que se omite la aplicación de directivas de tráfico de [SMI](https://smi-spec.io/). En este modo, OSM detecta automáticamente los servicios que forman parte de la malla de servicio. Los servicios detectados tendrán reglas de directiva de tráfico programadas en cada sidecar de proxy de Envoy para permitir las comunicaciones entre estos servicios.

> [!WARNING]
> Antes de continuar, compruebe que el modo de la directiva de tráfico permisivo está establecida en true; si no es así, cámbiela a **true** mediante el comando siguiente.

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure ya no sean necesarios, use la CLI de Azure para eliminar el grupo de recursos de prueba de implementación.

```
az group delete --name osm-bicep-test
```

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
